---
title: "Managing Cortex AI Function costs with Account Usage"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/ai-func-cost-management"
---

# Managing Cortex AI Function costs with Account Usage

Snowflake Cortex AI Functions (AI\_COMPLETE, AI\_SUMMARIZE, AI\_TRANSLATE, AI\_SENTIMENT, and others) consume credits based
on token or page usage. Without monitoring and controls, costs for using these functions can escalate quickly due to:

* Unoptimized prompts generating excessive tokens
* Long-running or runaway queries
* Lack of per-user spending limits
* Insufficient visibility into usage patterns

This topic suggests strategies for monitoring, managing, and controlling the costs associated with Snowflake Cortex AI
Functions. Using the CORTEX\_AI\_FUNCTIONS\_USAGE\_HISTORY view, you can track usage patterns and implement automated cost
controls. These techniques can help you monitor usage, alert when spending limits are exceeded, control access to
functions based on monthly limits, and stop runaway queries.

## Usage history view

The SNOWFLAKE.ACCOUNT\_USAGE.CORTEX\_AI\_FUNCTIONS\_USAGE\_HISTORY view provides detailed telemetry for all Cortex AI Functions invoked via SQL.
The view has a maximum latency of sixty minutes, although data may be available in as few as ten minutes after function execution begins.
For detailed information on this view, see the [CORTEX\_AI\_FUNCTIONS\_USAGE\_HISTORY view](../../sql-reference/account-usage/cortex_ai_functions_usage_history).

## Basic usage monitoring

The following queries help you understand your AI Functions usage patterns. Run these periodically yourself, or integrate them into dashboards for ongoing visibility.

### Daily credit consumption by function and model

Track daily spending trends to identify usage spikes and understand which functions and models consume the most credits.

CopyExpand

```
SELECT
    DATE_TRUNC('day', START_TIME) AS usage_date,
    FUNCTION_NAME,
    MODEL_NAME,
    SUM(CREDITS) AS total_credits,
    COUNT(DISTINCT QUERY_ID) AS query_count
FROM SNOWFLAKE.ACCOUNT_USAGE.CORTEX_AI_FUNCTIONS_USAGE_HISTORY
WHERE START_TIME >= DATEADD('day', -30, CURRENT_TIMESTAMP())
GROUP BY 1, 2, 3
ORDER BY usage_date DESC, total_credits DESC;
```

Show lessSee more

Scroll to top

### Monthly credit consumption by user

Identify top consumers and track per-user spending over time. This query joins with the USERS view to provide user details including email and default role for easier identification and follow-up.

CopyExpand

```
SELECT
    DATE_TRUNC('month', h.START_TIME) AS usage_month,
    u.NAME AS user_name,
    u.EMAIL,
    u.DEFAULT_ROLE,
    SUM(h.CREDITS) AS total_credits,
    COUNT(DISTINCT h.QUERY_ID) AS query_count
FROM SNOWFLAKE.ACCOUNT_USAGE.CORTEX_AI_FUNCTIONS_USAGE_HISTORY h
JOIN SNOWFLAKE.ACCOUNT_USAGE.USERS u
    ON h.USER_ID = u.USER_ID
WHERE h.START_TIME >= DATEADD('month', -3, CURRENT_TIMESTAMP())
GROUP BY 1, 2, 3, 4
ORDER BY usage_month DESC, total_credits DESC;
```

Show lessSee more

Scroll to top

## Cost control

Define automated mechanisms to detect excessive spending and take corrective action. These queries can be used independently of each other, or combined for comprehensive cost governance.

### Account-level monthly spending alert

Set up an automated alert that monitors total monthly AI Function credit consumption across your entire account. When spending exceeds a defined threshold, the alert sends an email notification to designated administrators.
Setting up the alert requires the following prerequisites:

* ACCOUNTADMIN role or appropriate privileges to [create notification integrations](../notifications/email-notifications.html#label-create-email-notification-integration) and [alerts](../alerts.html#label-alerts-privileges-granting)
* A warehouse to execute the alert condition check
* Verified email addresses of alert recipients

First, create a notification integration if one does not already exist. This example replaces any existing integration named `ai_cost_alerts`.

CopyExpand

```
CREATE OR REPLACE NOTIFICATION INTEGRATION ai_cost_alerts
    TYPE = EMAIL
    ENABLED = TRUE
    ALLOWED_RECIPIENTS = ('admin@company.com', 'finops@company.com')
```

Show lessSee more

Scroll to top

Next, create a table to track when alerts were sent for each month. This is used to prevent duplicate alerts within a month.

CopyExpand

```
CREATE TABLE IF NOT EXISTS AI_FUNCTIONS_ALERT_STATE (
    ALERT_NAME VARCHAR NOT NULL,
    ALERT_MONTH DATE NOT NULL,
    SENT_AT TIMESTAMP_LTZ DEFAULT CURRENT_TIMESTAMP(),
    CREDITS_AT_ALERT NUMBER(38,6),
    PRIMARY KEY (ALERT_NAME, ALERT_MONTH)
);
```

Show lessSee more

Scroll to top

Now create a stored procedure to check if an alert was already sent this month, record the alert state, and send the email notification.

CopyExpand

```
CREATE OR REPLACE PROCEDURE SEND_MONTHLY_SPEND_ALERT(P_THRESHOLD FLOAT)
RETURNS VARCHAR
LANGUAGE JAVASCRIPT
EXECUTE AS CALLER
AS
$$
    // Check if alert already sent this month
    var check_sent = snowflake.execute({
        sqlText: `SELECT COUNT(*) AS cnt FROM AI_FUNCTIONS_ALERT_STATE
                WHERE ALERT_NAME = 'monthly_spend'
                AND ALERT_MONTH = DATE_TRUNC('month', CURRENT_DATE())`
    });
    check_sent.next();
    var already_sent = check_sent.getColumnValue(1);

    if (already_sent > 0) {
        return 'Alert already sent for this month';
    }

    // Get current spend
    var spend_result = snowflake.execute({
        sqlText: `SELECT COALESCE(SUM(CREDITS), 0) AS total
                FROM SNOWFLAKE.ACCOUNT_USAGE.CORTEX_AI_FUNCTIONS_USAGE_HISTORY
                WHERE START_TIME >= DATE_TRUNC('month', CURRENT_TIMESTAMP())`
    });
    spend_result.next();
    var v_credits = spend_result.getColumnValue(1);

    // Check threshold
    if (v_credits <= P_THRESHOLD) {
        return 'Threshold not exceeded. Current: ' + v_credits + ' / ' + P_THRESHOLD;
    }

    // Record alert
    snowflake.execute({
        sqlText: `INSERT INTO AI_FUNCTIONS_ALERT_STATE (ALERT_NAME, ALERT_MONTH, CREDITS_AT_ALERT)
                VALUES ('monthly_spend', DATE_TRUNC('month', CURRENT_DATE()), ?)`,
        binds: [v_credits]
    });

    // Send email - update the recipient email address
    snowflake.execute({
        sqlText: `CALL SYSTEM$SEND_EMAIL(
            'ai_cost_alerts',
            'admin@company.com',
            'AI Functions Monthly Spend Alert',
            'Monthly AI Function credit consumption has exceeded the threshold.\\n\\n' ||
            'Current spend: ' || ${v_credits}::VARCHAR || ' credits\\n' ||
            'Threshold: ' || ${P_THRESHOLD}::VARCHAR || ' credits\\n\\n' ||
            'Please review usage accordingly.'
        )`
    });

    return 'Alert sent. Credits: ' + v_credits;
$$;
```

Show lessSee more

Scroll to top

Finally, create an alert that checks usage against the spending threshold each hour and calls the procedure to send the notification if needed.
You should adjust the limit of 1000 credits, which appears in two places in the example below, to the desired threshold.

CopyExpand

```
CREATE OR REPLACE ALERT ai_functions_monthly_spend_alert
    WAREHOUSE = <your_warehouse>
    SCHEDULE = 'USING CRON 0 * * * * UTC'  -- Runs every hour
    IF (EXISTS (
        SELECT 1
        FROM SNOWFLAKE.ACCOUNT_USAGE.CORTEX_AI_FUNCTIONS_USAGE_HISTORY
        WHERE START_TIME >= DATE_TRUNC('month', CURRENT_TIMESTAMP())
        HAVING SUM(CREDITS) > 1000  -- adjust the limit accordingly
    ))
    THEN
        CALL SEND_MONTHLY_SPEND_ALERT(1000);  -- please adjust the limit accordingly

-- enable the alert
ALTER ALERT ai_functions_monthly_spend_alert RESUME;
```

Show lessSee more

Scroll to top

Tip

For testing purposes, set the limit to 0 at first to trigger the alert immediately. Recreate the alert with the desired threshold after confirming that it works as expected.

After testing with a 0 threshold, run the following SQL to allow the alert to trigger again in the current month.

CopyExpand

```
DELETE FROM AI_FUNCTIONS_ALERT_STATE
WHERE ALERT_NAME = 'monthly_spend'
AND ALERT_MONTH = DATE_TRUNC('month', CURRENT_DATE());
```

Show lessSee more

Scroll to top

You can make sure that the alert is operating by querying the alert history and the alert state table as follows:

CopyExpand

```
-- Make sure alert exists
SHOW ALERTS LIKE 'ai_functions_monthly_spend_alert';

-- Check alert history
SELECT *
FROM TABLE(INFORMATION_SCHEMA.ALERT_HISTORY(
    SCHEDULED_TIME_RANGE_START => DATEADD('day', -1, CURRENT_TIMESTAMP()),
    ALERT_NAME => 'ai_functions_monthly_spend_alert'
))
ORDER BY SCHEDULED_TIME DESC;

-- Check which months have had alerts sent
SELECT * FROM AI_FUNCTIONS_ALERT_STATE ORDER BY ALERT_MONTH DESC;
```

Show lessSee more

Scroll to top

### Per-user monthly spending limits

This example implements per-user monthly spending limits. Users are granted a dedicated custom AI\_FUNCTIONS\_USER\_ROLE that
provides access to Cortex AI Functions. A table stores individual users’ monthly token budget. When a user exceeds their
budget for the month, an hourly task revokes their access to AI Functions by removing AI\_FUNCTIONS\_USER\_ROLE. A monthly
task restores the role at the beginning of the next month.

Important

By default, all users have access to AI Functions (and other Snowflake Cortex features) because the SNOWFLAKE.CORTEX\_USER database role is granted to the PUBLIC role.
To enforce per-user limits, you must revoke SNOWFLAKE.CORTEX\_USER from PUBLIC and grant it only through the AI\_FUNCTIONS\_USER\_ROLE. Use the
following SQL to revoke the role from PUBLIC:

CopyExpand

```
REVOKE DATABASE ROLE SNOWFLAKE.CORTEX_USER FROM ROLE PUBLIC;
```

Show lessSee more

Scroll to top

Be sure that all users who need access to Cortex features are granted only the AI\_FUNCTIONS\_USER\_ROLE. Use of
any other role that includes SNOWFLAKE.CORTEX\_USER allows users to bypass the spending limit controls implemented in
this example. In some cases, you could use a more specific role; for example, users who need access only to Cortex Analyst
can be granted the SNOWFLAKE.CORTEX\_ANALYST\_USER role instead of SNOWFLAKE.CORTEX\_USER.

To set up per-user spending limits, first create a role that controls access to AI Functions, allowing this access to be managed separately from other privileges.

CopyExpand

```
-- Create a role specifically for AI Function access
CREATE ROLE IF NOT EXISTS AI_FUNCTIONS_USER_ROLE;

-- Grant necessary privileges to the role
GRANT DATABASE ROLE SNOWFLAKE.CORTEX_USER TO ROLE AI_FUNCTIONS_USER_ROLE;

-- Grant usage on warehouse
GRANT USAGE ON WAREHOUSE AI_FUNCTIONS_WAREHOUSE TO ROLE AI_FUNCTIONS_USER_ROLE;
```

Show lessSee more

Scroll to top

Now, set up the access control table, which tracks which users have AI Function access, their individual spending
limits, and their revocation history. It serves as the source of truth for the automated monitoring and access
restoration processes.

CopyExpand

```
CREATE TABLE IF NOT EXISTS AI_FUNCTIONS_ACCESS_CONTROL (
    USER_NAME VARCHAR NOT NULL,
    USER_ID NUMBER,
    GRANTED_AT TIMESTAMP_LTZ DEFAULT CURRENT_TIMESTAMP(),
    MONTHLY_CREDIT_LIMIT NUMBER(38,6) DEFAULT 100,  -- adjust the limit accordingly
    IS_ACTIVE BOOLEAN DEFAULT TRUE,
    REVOKED_AT TIMESTAMP_LTZ,
    REVOCATION_REASON VARCHAR,
    PRIMARY KEY (USER_NAME)
);
```

Show lessSee more

Scroll to top

Next, create a stored procedure to grant AI Function access to a user and register them in the access control table with their spending limit.
The code looks up the user’s ID from the Account Usage view to enable efficient joins in monitoring queries.

CopyExpand

```
CREATE OR REPLACE PROCEDURE GRANT_AI_FUNCTIONS_ACCESS(
    P_USER_NAME VARCHAR,
    P_MONTHLY_LIMIT NUMBER(38,6) DEFAULT 100  -- adjust the limit accordingly
)
RETURNS VARCHAR
LANGUAGE SQL
AS
$$
DECLARE
    v_user_id NUMBER;
BEGIN
    -- Look up USER_ID from account usage
    SELECT USER_ID INTO :v_user_id
    FROM SNOWFLAKE.ACCOUNT_USAGE.USERS
    WHERE NAME = :P_USER_NAME
    LIMIT 1;

    -- Grant the AI Functions role to the user
    EXECUTE IMMEDIATE 'GRANT ROLE AI_FUNCTIONS_USER_ROLE TO USER ' || P_USER_NAME;

    -- Register or update the user in the access control table
    MERGE INTO AI_FUNCTIONS_ACCESS_CONTROL tgt
    USING (SELECT :P_USER_NAME AS USER_NAME) src
    ON tgt.USER_NAME = src.USER_NAME
    WHEN MATCHED THEN
        UPDATE SET
            USER_ID = :v_user_id,
            IS_ACTIVE = TRUE,
            MONTHLY_CREDIT_LIMIT = :P_MONTHLY_LIMIT,
            GRANTED_AT = CURRENT_TIMESTAMP(),
            REVOKED_AT = NULL,
            REVOCATION_REASON = NULL
    WHEN NOT MATCHED THEN
        INSERT (USER_NAME, USER_ID, MONTHLY_CREDIT_LIMIT, IS_ACTIVE)
        VALUES (:P_USER_NAME, :v_user_id, :P_MONTHLY_LIMIT, TRUE);

    RETURN 'Access granted to ' || P_USER_NAME || ' with monthly limit of ' || P_MONTHLY_LIMIT || ' credits';
END;
$$;
```

Show lessSee more

Scroll to top

Use this stored procedure to add users and their credit quotas to the access control table.

CopyExpand

```
CALL GRANT_AI_FUNCTIONS_ACCESS('ALICE', 1000);  -- grants access to user ALICE with a monthly limit of 100 credits
CALL GRANT_AI_FUNCTIONS_ACCESS('BOB', 2000);    -- grants access to user BOB with a monthly limit of 200 credits
```

Show lessSee more

Scroll to top

Create the monthly access refresh task next. This task runs on the first day of each month to restore AI Function access
for all entitled users. When a user’s access was revoked due to exceeding their limit in the previous month, this task
grants them a fresh budget for the new month.

CopyExpand

```
-- Create a procedure to re-grant access to all entitled users
CREATE OR REPLACE PROCEDURE GRANT_ALL_ENTITLED_USERS()
RETURNS TABLE (USER_NAME VARCHAR, CREDIT_LIMIT NUMBER, ACTION VARCHAR)
LANGUAGE SQL
AS
$$
DECLARE
    result RESULTSET;
BEGIN
    result := (
        SELECT
            USER_NAME,
            MONTHLY_CREDIT_LIMIT AS CREDIT_LIMIT,
            'GRANTED' AS ACTION
        FROM AI_FUNCTIONS_ACCESS_CONTROL
    );

    -- Re-grant access for each entitled user
    FOR rec IN result DO
        CALL GRANT_AI_FUNCTIONS_ACCESS(rec.USER_NAME, rec.CREDIT_LIMIT);
    END FOR;

    RETURN TABLE(result);
END;
$$;

-- Create a task to run on the 1st of each month at midnight UTC
CREATE OR REPLACE TASK MONTHLY_AI_FUNCTIONS_ACCESS_REFRESH
    WAREHOUSE = <your_warehouse>
    SCHEDULE = 'USING CRON 0 0 1 * * UTC'  -- 1st day of each month at 00:00 UTC
AS
    CALL GRANT_ALL_ENTITLED_USERS();

-- Enable the task
ALTER TASK MONTHLY_AI_FUNCTIONS_ACCESS_REFRESH RESUME;

-- Run once initially to populate grantees
CALL GRANT_ALL_ENTITLED_USERS();

-- Verify task status
SHOW TASKS LIKE 'MONTHLY_AI_FUNCTIONS_ACCESS_REFRESH';
```

Show lessSee more

Scroll to top

Finally, create an hourly task to monitor user spending and revoke access for any user who exceeds their monthly limit.

CopyExpand

```
-- Create a procedure to re-grant access to all entitled users
CREATE OR REPLACE PROCEDURE GRANT_ALL_ENTITLED_USERS()
RETURNS TABLE (USER_NAME VARCHAR, CREDIT_LIMIT NUMBER, ACTION VARCHAR)
LANGUAGE SQL
AS
$$
DECLARE
    result RESULTSET;
BEGIN
    result := (
        SELECT
            USER_NAME,
            MONTHLY_CREDIT_LIMIT AS CREDIT_LIMIT,
            'GRANTED' AS ACTION
        FROM AI_FUNCTIONS_ACCESS_CONTROL
    );

    -- Re-grant access for each entitled user
    FOR rec IN result DO
        CALL GRANT_AI_FUNCTIONS_ACCESS(rec.USER_NAME, rec.CREDIT_LIMIT);
    END FOR;

    RETURN TABLE(result);
END;
$$;

-- Create a task to run on the 1st of each month at midnight UTC
CREATE OR REPLACE TASK MONTHLY_AI_FUNCTIONS_ACCESS_REFRESH
    WAREHOUSE = <your_warehouse>
    SCHEDULE = 'USING CRON 0 0 1 * * UTC'  -- 1st day of each month at 00:00 UTC
AS
    CALL GRANT_ALL_ENTITLED_USERS();

-- Enable the task
ALTER TASK MONTHLY_AI_FUNCTIONS_ACCESS_REFRESH RESUME;

-- Run once initially to populate grantees
CALL GRANT_ALL_ENTITLED_USERS();

-- Verify task status
SHOW TASKS LIKE 'MONTHLY_AI_FUNCTIONS_ACCESS_REFRESH';
```

Show lessSee more

Scroll to top

### Runaway query detection and cancellation

Long-running AI Function queries can accumulate significant costs. This example implements an automated system to detect queries that exceed a credit threshold and cancel them before they consume even more resources.
An email alert is sent with full query details.

Note

When a query is cancelled, the client is still charged for all resources consumed up to the moment of cancellation. Cancelling a runaway query prevents further cost accumulation but does not refund credits already spent.

This procedure finds AI Function queries from the last 48 hours that have exceeded the credit threshold and are still running, cancels them, and reports them to an administrator.

CopyExpand

```
-- Create a procedure to detect and cancel expensive runaway queries
CREATE OR REPLACE PROCEDURE MONITOR_AND_CANCEL_RUNAWAY_QUERIES(
    P_CREDIT_THRESHOLD NUMBER DEFAULT 50  -- adjust the limit accordingly
)
RETURNS TABLE (
    QUERY_ID VARCHAR,
    USER_NAME VARCHAR,
    FUNCTION_NAME VARCHAR,
    MODEL_NAME VARCHAR,
    CREDITS NUMBER,
    START_TIME TIMESTAMP_LTZ,
    ACTION VARCHAR
)
LANGUAGE SQL
AS
$$
DECLARE
    result RESULTSET;
BEGIN
    -- Find queries from the last 48 hours that exceed the threshold and are still running
    result := (
        SELECT
            h.QUERY_ID,
            u.NAME AS USER_NAME,
            h.FUNCTION_NAME,
            h.MODEL_NAME,
            h.CREDITS,
            h.START_TIME,
            h.ROLE_NAMES,
            h.QUERY_TAG,
            h.WAREHOUSE_ID,
            'CANCELLED' AS ACTION
        FROM SNOWFLAKE.ACCOUNT_USAGE.CORTEX_AI_FUNCTIONS_USAGE_HISTORY h
        LEFT JOIN SNOWFLAKE.ACCOUNT_USAGE.USERS u
            ON h.USER_ID = u.USER_ID
        WHERE h.START_TIME >= DATEADD('hour', -48, CURRENT_TIMESTAMP())
        AND h.CREDITS > :P_CREDIT_THRESHOLD
        AND h.IS_COMPLETED = FALSE
    );

    -- Cancel each runaway query and send alert
    FOR rec IN result DO
        -- Attempt to cancel the query
        BEGIN
            EXECUTE IMMEDIATE 'SELECT SYSTEM$CANCEL_QUERY(''' || rec.QUERY_ID || ''')';
        EXCEPTION
            WHEN OTHER THEN
                NULL;  -- Query may have already completed
        END;

        -- Send alert with query details
        CALL SYSTEM$SEND_EMAIL(
            'ai_cost_alerts',
            'admin@company.com',
            'Runaway AI Query Cancelled - ' || rec.QUERY_ID,
            'A runaway AI Function query has been cancelled due to excessive cost.\n\n' ||
            'Query Details:\n' ||
            '- Query ID: ' || rec.QUERY_ID || '\n' ||
            '- User: ' || COALESCE(rec.USER_NAME, 'Unknown') || '\n' ||
            '- Function: ' || rec.FUNCTION_NAME || '\n' ||
            '- Model: ' || rec.MODEL_NAME || '\n' ||
            '- Credits Used: ' || rec.CREDITS::VARCHAR || '\n' ||
            '- Threshold: ' || :P_CREDIT_THRESHOLD::VARCHAR || '\n' ||
            '- Start Time: ' || rec.START_TIME::VARCHAR || '\n' ||
            '- Roles: ' || COALESCE(rec.ROLE_NAMES::VARCHAR, 'N/A') || '\n' ||
            '- Query Tag: ' || COALESCE(rec.QUERY_TAG, 'N/A') || '\n' ||
            '- Warehouse ID: ' || COALESCE(rec.WAREHOUSE_ID::VARCHAR, 'N/A') || '\n\n' ||
            'Please investigate this query and take appropriate action.'
        );
    END FOR;

    RETURN TABLE(result);
END;
$$;

-- Create a task to monitor and cancel runaway queries every hour
CREATE OR REPLACE TASK MONITOR_RUNAWAY_AI_QUERIES
    WAREHOUSE = <your_warehouse>
    SCHEDULE = 'USING CRON 0 * * * * UTC'  -- Every hour
AS
    CALL MONITOR_AND_CANCEL_RUNAWAY_QUERIES(50);  -- adjust the limit accordingly

-- Enable the task
ALTER TASK MONITOR_RUNAWAY_AI_QUERIES RESUME;

-- Verify task status
SHOW TASKS LIKE 'MONITOR_RUNAWAY_AI_QUERIES';

-- Check task execution history
SELECT *
FROM TABLE(INFORMATION_SCHEMA.TASK_HISTORY(
    SCHEDULED_TIME_RANGE_START => DATEADD('day', -1, CURRENT_TIMESTAMP()),
    TASK_NAME => 'MONITOR_RUNAWAY_AI_QUERIES'
))
ORDER BY SCHEDULED_TIME DESC;
```

Show lessSee more

Scroll to top

Tip

You may already know that some of your queries will run a long time. You can define a special role for these queries and exclude that role
from the cancellation logic in the procedure. For example, to create the role:

CopyExpand

```
CREATE ROLE AI_FUNCTIONS_USER_LONG_RUNNING_ROLE;
GRANT ROLE AI_FUNCTIONS_USER_ROLE TO ROLE AI_FUNCTIONS_USER_LONG_RUNNING_ROLE;
GRANT ROLE AI_FUNCTIONS_USER_LONG_RUNNING_ROLE TO USER LONG_RUNNING_USER;
```

Show lessSee more

Scroll to top

Add the following condition to the WHERE clause of the procedure to exclude queries run by users with this role from being cancelled.

,, code-block:: sqlexample

> AND NOT ARRAY\_CONTAINS(h.ROLE\_NAMES, ‘AI\_FUNCTIONS\_USER\_LONG\_RUNNING\_ROLE’)

Now the user can assume the role to run a long-running query without it being canceled:

CopyExpand

```
USE ROLE AI_FUNCTIONS_USER_LONG_RUNNING_ROLE;
-- then start the long-running query
```

Show lessSee more

Scroll to top

## Best practices

Keep the following best practices in mind when developing a cost management strategy for AI Function usage:

* **Start with monitoring:** Before implementing automated controls, establish baseline usage patterns using the queries in
  [Basic usage monitoring](#label-ai-functions-basic-usage-queries).
* **Set conservative initial limits:** Begin with lower thresholds and adjust upward based on actual usage patterns.
* **Use query tags:** Encourage teams to use QUERY\_TAG session parameters to enable cost attribution by project or team.
* **Review regularly:** Periodically review the access control table and adjust per-user limits based on legitimate needs.
* **Test alerts:** Verify that email notifications work correctly before relying on them for critical alerts.
* **Consider latency:** The ACCOUNT\_USAGE view has up to 60 minutes of latency; factor this into your monitoring strategy.
