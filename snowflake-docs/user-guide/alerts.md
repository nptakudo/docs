---
title: "Setting up alerts based on data in Snowflake"
url: "https://docs.snowflake.com/en/user-guide/alerts"
---

# Setting up alerts based on data in Snowflake

This topic explains how to set up an alert that periodically performs an action under specific conditions, based on data within
Snowflake.

## Introduction

In some cases, you might want to be notified or take action when data in Snowflake meets certain conditions. For example, you
might want to receive a notification when:

* The warehouse credit usage increases by a specified percentage of your current quota.
* The resource consumption for your pipelines, tasks, materialized views, etc. increases beyond a specified amount.
* Your data fails to comply with a particular business rule that you have set up.

To do this, you can set up a Snowflake alert. A Snowflake alert is a schema-level object that specifies:

* A condition that triggers the alert (e.g. the presence of queries that take longer than a second to complete).
* The action to perform when the condition is met (e.g. send an email notification, capture some data in a table, etc.).
* When and how often the condition should be evaluated (e.g. every 24 hours, every Sunday at midnight, etc.).

For example, suppose that you want to send an email notification when the credit consumption exceeds a certain limit for a
warehouse. Suppose that you want to check for this every 30 minutes. You can create an alert with the following properties:

* Condition: The credit consumption for a warehouse (the sum of the `credits_used` column in the
  [WAREHOUSE\_METERING\_HISTORY](../sql-reference/account-usage/warehouse_metering_history) view in the
  [ACCOUNT\_USAGE](../sql-reference/account-usage)) schema exceeds a specified limit.
* Action: Email the administrator.
* Frequency / schedule: Check for this condition every 30 minutes.

## Choosing the type of alert

You can create the following types of alerts:

* [Alert on a schedule](#label-alerts-type-scheduled): Snowflake evaluates the condition against the existing data on a
  scheduled basis.

  For example, you can set up a alert on a schedule to check if any of the existing rows in a table has a column value that
  exceeds a specified amount.
* [Alert on new data](#label-alerts-type-streaming): Snowflake evaluates the condition against any new rows in a specified
  table or a view.

  For example, you can set up an alert on new data to notify you when new rows for error messages are inserted into the
  [event table](../developer-guide/logging-tracing/event-table-setting-up) for your account. Because dynamic table refreshes
  and task executions log events to the event table, you can set up an alert on new data to:

  + [Monitor dynamic table refreshes](dynamic-tables-monitor-event-table-alerts.html#label-dynamic-tables-streaming-alerts).
  + [Monitor task executions](tasks-events).

### Alerts on a schedule

With an alert on a schedule, you can set up an alert to execute every `n` minutes or on a schedule specified by a cron
expression.

The condition of the alert is evaluated on all of the data (as opposed to alerts on new data, where conditions are evaluated
against only the new rows that have been inserted).

### Alerts on new data

With an alert on new data, you can set up an alert to execute only when new rows are inserted in a table or are made available
in a view.

Whenever new rows are inserted, the alert executes, evaluating the condition against just the new rows, and performing the action
if the condition evaluates to TRUE.

If you want to evaluate a condition on newly inserted rows, use an alert on new data, rather than setting up an alert on a
schedule (which executes on a fixed schedule, regardless of whether or not data has been added).

Because the alert operates only on newly inserted rows in a table or view, there are restrictions on the condition that you can
specify:

* In the SELECT statement, the FROM clause can specify only one regular table, view, or event table.
* You must [enable change tracking](streams-manage.html#label-enabling-change-tracking-views) on that table or view.
* You cannot use:

  + [Common table expressions (CTEs)](queries-cte)
  + [Data Manipulation Language (DML) commands](../sql-reference/sql-dml)
  + Calls to stored procedures
  + Joins

Note

You cannot use the [EXECUTE ALERT](../sql-reference/sql/execute-alert) command to execute an alert on new data.

## Choosing the warehouse for the alerts

An alert requires a [warehouse](warehouses) for execution. You can either use
[the serverless compute model](#label-alerts-serverless-compute) or
[a virtual warehouse that you specify](#label-alerts-warehouse-user-managed).

### Using the serverless compute model (serverless alerts)

Alerts that use the serverless compute model called *serverless alerts*. If you use the serverless compute model, Snowflake
automatically resizes and scales the compute resources required for the alert. Snowflake determines the ideal size of the compute
resources for a given run based on a dynamic analysis of statistics for the most recent previous runs of the same alert. The
maximum size for a serverless alert run is equivalent to an XXLARGE warehouse. Multiple workloads in your account share a common
set of compute resources.

Billing is similar to other serverless features (such as serverless tasks). See [Understanding the costs of alerts](#label-alerts-costs).

Note

If you are creating an [alert on new data](#label-alerts-type-streaming) that is added infrequently, consider
configuring this as a serverless alert. If you configure the alert to use a warehouse instead, even a simple action that sends
an email notification incurs at least one minute of warehouse cost.

### Using a virtual warehouse that you specify

If you want to specify a virtual warehouse, you must choose a warehouse that is sized appropriately for the SQL actions that
are executed by the alert. For guidelines on choosing a warehouse, see [Warehouse considerations](warehouses-considerations).

## Understanding the costs of alerts

The costs associated with running an alert to execute SQL code differ depending on the compute resources used for the alert:

* For serverless alerts, Snowflake bills your account based on compute resource usage. Charges are calculated based on your
  total usage of the resources, including cloud service usage, measured in *compute-hours* credit usage. The compute-hours cost
  changes based on warehouse size and query runtime. For more information, see [Serverless credit usage](cost-understanding-compute.html#label-serverless-credit-usage).

  To learn how many credits are consumed by alerts, refer to the “Serverless Feature Credit Table” in
  the [Snowflake Service Consumption Table](https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf).

  To view the usage history of serverless alerts, you can:

  + Call the [SERVERLESS\_ALERT\_HISTORY](../sql-reference/functions/serverless_alert_history) function.
  + Query the [SERVERLESS\_ALERT\_HISTORY view](../sql-reference/account-usage/serverless_alert_history).
* For alerts that use a virtual warehouse that you specify, Snowflake bills your account for
  [credit usage](cost-understanding-compute.html#label-virtual-warehouse-credit-usage) based on the warehouse usage when an alert is running. This is
  similar to the warehouse usage for executing the same SQL statements in a client or Snowsight. Per-second credit
  billing and warehouse auto-suspend give you the flexibility to start with larger warehouse sizes and then adjust the size to
  match your alert workloads.

Tip

If you want to set up an alert that evaluates new rows added to a table or view, use an
[alert on new data](#label-alerts-type-streaming), rather than an alert on a schedule. An alert on a schedule will
execute at a scheduled time, regardless of whether or not new rows have been inserted.

## Granting the privileges to create alerts

In order to create an alert, you must use a role that has the following privileges:

* The EXECUTE ALERT privilege on the account.

  Note

  This privilege can only be granted by a user with the ACCOUNTADMIN role.
* One of the following privileges:

  + The EXECUTE MANAGED ALERT privilege on the account, if you are creating a serverless alert.
  + The USAGE privilege on the warehouse used to execute the alert, if you are specifying a virtual warehouse for the alert.
* The USAGE and CREATE ALERT privileges on the schema in which you want to create the alert.
* The USAGE privilege on the database containing the schema.
* The SELECT privilege on the table or view that you want to query in the alert condition (if you are creating an
  [alert on new data](#label-alerts-type-streaming)).

To grant these privileges to a role, use the [GRANT <privileges> … TO ROLE](../sql-reference/sql/grant-privilege) command.

For example, suppose that you want to create a custom role named `my_alert_role` that has the privileges to create an alert in
the schema named `my_schema`. You want the alert to use the warehouse `my_warehouse`.

To do this:

1. Have a user with the ACCOUNTADMIN role do the following:

   1. [Create the custom role](security-access-control-configure.html#label-security-custom-role).

      For example:

      CopyExpand

      ```
      USE ROLE ACCOUNTADMIN;

      CREATE ROLE my_alert_role;
      ```

      Show lessSee more

      Scroll to top
   2. Grant the EXECUTE ALERT global privilege to that custom role.

      For example:

      CopyExpand

      ```
      GRANT EXECUTE ALERT ON ACCOUNT TO ROLE my_alert_role;
      ```

      Show lessSee more

      Scroll to top
   3. If you want to create a serverless alert, grant the EXECUTE MANAGED ALERT global privilege to that custom role.

      For example:

      CopyExpand

      ```
      GRANT EXECUTE MANAGED ALERT ON ACCOUNT TO ROLE my_alert_role;
      ```

      Show lessSee more

      Scroll to top
   4. Grant the custom role to a user.

      For example:

      CopyExpand

      ```
      GRANT ROLE my_alert_role TO USER my_user;
      ```

      Show lessSee more

      Scroll to top
2. Have the owners of the database, schema, and warehouse grant the privileges needed for creating the alert to the custom role:

   * The owner of the schema must grant the CREATE ALERT and USAGE privileges on the schema:

     CopyExpand

     ```
     GRANT CREATE ALERT ON SCHEMA my_schema TO ROLE my_alert_role;
     GRANT USAGE ON SCHEMA my_schema TO ROLE my_alert_role;
     ```

     Show lessSee more

     Scroll to top
   * The owner of the database must grant the USAGE privilege on the database:

     CopyExpand

     ```
     GRANT USAGE ON DATABASE my_database TO ROLE my_alert_role;
     ```

     Show lessSee more

     Scroll to top
   * If you want to specify a warehouse for the alert, the owner of that warehouse must grant the USAGE privilege on the
     warehouse:

     CopyExpand

     ```
     GRANT USAGE ON WAREHOUSE my_warehouse TO ROLE my_alert_role;
     ```

     Show lessSee more

     Scroll to top

## Creating an alert

The following sections provide the basic steps and an example of creating different types of alerts:

* [Creating an alert on a schedule](#label-alerts-create-scheduled)
* [Creating an alert on new data](#label-alerts-create-streaming)

### Creating an alert on a schedule

Suppose that whenever one or more rows in a table named `gauge` has a value in the `gauge_value` column that exceeds 200,
you want to insert the current timestamp into a table named `gauge_value_exceeded_history`.

You can create an alert that:

* Evaluates the condition that `gauge_value` exceeds 200.
* Inserts the timestamp into `gauge_value_exceeded_history` if this condition evaluates to true.

To create an alert named `my_alert` that does this:

1. Verify that you are using a role that has [the privileges to create an alert](#label-alerts-privileges-granting).

   If you are not using that role, execute the [USE ROLE](../sql-reference/sql/use-role) command to use that role.
2. Verify that you are using the database and schema in which you plan to create the alert.

   If you are not using that database and schema, execute the [USE DATABASE](../sql-reference/sql/use-database) and
   [USE SCHEMA](../sql-reference/sql/use-schema) commands to use that database and schema.
3. Execute the [CREATE ALERT](../sql-reference/sql/create-alert) command to create the alert:

   CopyExpand

   ```
   CREATE OR REPLACE ALERT my_alert
     WAREHOUSE = mywarehouse
     SCHEDULE = '1 minute'
     IF( EXISTS(
       SELECT gauge_value FROM gauge WHERE gauge_value>200))
     THEN
       INSERT INTO gauge_value_exceeded_history VALUES (current_timestamp());
   ```

   Show lessSee more

   Scroll to top

   If you want to create a serverless alert, omit the WAREHOUSE parameter:

   CopyExpand

   ```
   CREATE OR REPLACE ALERT my_alert
     SCHEDULE = '1 minute'
     IF( EXISTS(
       SELECT gauge_value FROM gauge WHERE gauge_value>200))
     THEN
       INSERT INTO gauge_value_exceeded_history VALUES (current_timestamp());
   ```

   Show lessSee more

   Scroll to top

   For the full description of the CREATE ALERT command, refer to [CREATE ALERT](../sql-reference/sql/create-alert).

   Note

   When you create an alert, the alert is suspended by default. You must resume the newly created alert in order for the alert
   to execute.
4. Resume the alert by executing the [ALTER ALERT … RESUME](../sql-reference/sql/alter-alert) command. For example:

   CopyExpand

   ```
   ALTER ALERT my_alert RESUME;
   ```

   Show lessSee more

   Scroll to top

### Creating an alert on new data

Suppose that you want to receive an email notification when a stored procedure named `my_stored_proc` in the database and
schema `my_db.my_schema` logs a FATAL message to the
[active event table for your account](../developer-guide/logging-tracing/event-table-setting-up).

To create an alert named `my_alert` that does this:

1. Find the name of the active event table for your account:

   CopyExpand

   ```
   SHOW PARAMETERS LIKE 'EVENT_TABLE' IN ACCOUNT;
   ```

   Show lessSee more

   Scroll to top

   Expand

   ```
   +-------------+---------------------------+----------------------------+---------+-----------------------------------------+--------+
   | key         | value                     | default                    | level   | description                             | type   |
   |-------------+---------------------------+----------------------------+---------+-----------------------------------------+--------|
   | EVENT_TABLE | my_db.my_schema.my_events | snowflake.telemetry.events | ACCOUNT | Event destination for the given target. | STRING |
   +-------------+---------------------------+----------------------------+---------+-----------------------------------------+--------+
   ```

   Show lessSee more

   Scroll to top
2. [Enable change tracking](streams-manage.html#label-enabling-change-tracking-views) on the table or view that you plan to query in the alert
   condition.

   CopyExpand

   ```
   ALTER TABLE my_db.my_schema.my_events SET CHANGE_TRACKING = TRUE;
   ```

   Show lessSee more

   Scroll to top
3. [Set up a notification integration for sending email](notifications/email-notifications).
4. Verify that you are using a role that has [the privileges to create an alert](#label-alerts-privileges-granting).

   If you are not using that role, execute the [USE ROLE](../sql-reference/sql/use-role) command to use that role.
5. Verify that you are using database and schema in which you plan to create the alert.

   If you are not using that database and schema, execute the [USE DATABASE](../sql-reference/sql/use-database) and
   [USE SCHEMA](../sql-reference/sql/use-schema) commands to use that database and schema.
6. Execute the [CREATE ALERT](../sql-reference/sql/create-alert) command to create the alert, and omit the SCHEDULE parameter.

   For example, the following example creates an alert on new data that monitors the event table for errors in dynamic table
   refreshes and sends a notification to a Slack channel. The example assumes the following:

   * Your active event table is the [default event table](../developer-guide/logging-tracing/event-table-setting-up.html#label-logging-event-table-default)
     (SNOWFLAKE.TELEMETRY.EVENTS).
   * You have [set the severity level](dynamic-tables-monitor-event-table-alerts.html#label-dynamic-tables-monitoring-sql-events-level) to capture events for your dynamic
     table.
   * You have [set up a webhook notification integration](notifications/webhook-notifications) for that Slack
     channel.

   CopyExpand

   ```
   CREATE OR REPLACE ALERT my_alert
     WAREHOUSE = mywarehouse
     IF( EXISTS(
       SELECT * FROM SNOWFLAKE.TELEMETRY.EVENTS
         WHERE
           resource_attributes:"snow.executable.type" = 'DYNAMIC_TABLE' AND
           record_type='EVENT' AND
           value:"state"='ERROR'
     ))
     THEN
       BEGIN
         LET result_str VARCHAR;
         (SELECT ARRAY_TO_STRING(ARRAY_AGG(name)::ARRAY, ',') INTO :result_str
           FROM (
             SELECT resource_attributes:"snow.executable.name"::VARCHAR name
               FROM TABLE(RESULT_SCAN(SNOWFLAKE.ALERT.GET_CONDITION_QUERY_UUID()))
               LIMIT 10
           )
         );
         CALL SYSTEM$SEND_SNOWFLAKE_NOTIFICATION(
           SNOWFLAKE.NOTIFICATION.TEXT_PLAIN(:result_str),
           '{"my_slack_integration": {}}'
         );
       END;
   ```

   Show lessSee more

   Scroll to top

   If you want to create a serverless alert, omit the WAREHOUSE parameter:

   CopyExpand

   ```
   CREATE OR REPLACE ALERT my_alert
     IF( EXISTS(
       SELECT * FROM SNOWFLAKE.TELEMETRY.EVENTS
         WHERE
           resource_attributes:"snow.executable.type" = 'DYNAMIC_TABLE' AND
           record_type='EVENT' AND
           value:"state"='ERROR'
     ))
     THEN
       BEGIN
         LET result_str VARCHAR;
         (SELECT ARRAY_TO_STRING(ARRAY_AGG(name)::ARRAY, ',') INTO :result_str
           FROM (
             SELECT resource_attributes:"snow.executable.name"::VARCHAR name
               FROM TABLE(RESULT_SCAN(SNOWFLAKE.ALERT.GET_CONDITION_QUERY_UUID()))
               LIMIT 10
           )
         );
         CALL SYSTEM$SEND_SNOWFLAKE_NOTIFICATION(
           SNOWFLAKE.NOTIFICATION.TEXT_PLAIN(:result_str),
           '{"my_slack_integration": {}}'
         );
       END;
   ```

   Show lessSee more

   Scroll to top

   For the full description of the CREATE ALERT command, refer to [CREATE ALERT](../sql-reference/sql/create-alert).

   Note

   When you create an alert, the alert is suspended by default. You must resume the newly created alert in order for the alert
   to execute.
7. Resume the alert by executing the [ALTER ALERT … RESUME](../sql-reference/sql/alter-alert) command. For example:

   CopyExpand

   ```
   ALTER ALERT my_alert RESUME;
   ```

   Show lessSee more

   Scroll to top

## Specifying timestamps based on alert schedules

In some cases, you might need to define a condition or action based on the alert schedule.

For example, suppose that a table has a timestamp column that represents when a row was added, and you want to send an alert
if any new rows were added between the last alert that was successfully evaluated and the current scheduled alert. In other
words, you want to evaluate:

CopyExpand

```
<now> - <last_execution_of_the_alert>
```

Show lessSee more

Scroll to top

If you use [CURRENT\_TIMESTAMP](../sql-reference/functions/current_timestamp) and the scheduled time of the alert to calculate this range of
time, the calculated range does not account for latency between the time that the alert is scheduled and the time when the
alert condition is actually evaluated.

Instead, when you need the timestamps of the current schedule alert and the last alert that was successfully evaluated, use the
following functions:

* [SCHEDULED\_TIME](../sql-reference/functions/scheduled_time) returns the timestamp representing when the current alert was scheduled.
* [LAST\_SUCCESSFUL\_SCHEDULED\_TIME](../sql-reference/functions/last_successful_scheduled_time) returns the timestamp representing when the last successfully
  evaluated alert was scheduled.

These functions are defined in the [SNOWFLAKE.ALERT schema](../sql-reference/snowflake-db). To call these functions, you need
to use a role that has been granted the [SNOWFLAKE.ALERT\_VIEWER database role](../sql-reference/snowflake-db-roles.html#label-snowflake-db-roles-alert-schema). To
grant this role to another role, use the [GRANT DATABASE ROLE](../sql-reference/sql/grant-database-role) command. For example, to grant this role
to the custom role `alert_role`, execute:

CopyExpand

```
GRANT DATABASE ROLE SNOWFLAKE.ALERT_VIEWER TO ROLE alert_role;
```

Show lessSee more

Scroll to top

The following example sends an email message if any new rows were added to `my_table` between the time that the last
successfully evaluated alert was scheduled and the time when the current alert has been scheduled:

CopyExpand

```
CREATE OR REPLACE ALERT alert_new_rows
  WAREHOUSE = my_warehouse
  SCHEDULE = '1 MINUTE'
  IF (EXISTS (
      SELECT *
      FROM my_table
      WHERE row_timestamp BETWEEN SNOWFLAKE.ALERT.LAST_SUCCESSFUL_SCHEDULED_TIME()
       AND SNOWFLAKE.ALERT.SCHEDULED_TIME()
  ))
  THEN CALL SYSTEM$SEND_EMAIL(...);
```

Show lessSee more

Scroll to top

## Checking the results of the SQL statement for the condition in the alert action

Within the action of an alert, if you need to check the results of the SQL statement for the condition:

1. Call the [GET\_CONDITION\_QUERY\_UUID](../sql-reference/functions/get_condition_query_uuid) function to get the query ID for the SQL statement for the
   condition.
2. Pass the query ID to the [RESULT\_SCAN](../sql-reference/functions/result_scan) function to get the results of the execution of that SQL
   statement.

For example:

CopyExpand

```
CREATE ALERT my_alert
  WAREHOUSE = my_warehouse
  SCHEDULE = '1 MINUTE'
  IF (EXISTS (
    SELECT * FROM my_source_table))
  THEN
    BEGIN
      LET condition_result_set RESULTSET :=
        (SELECT * FROM TABLE(RESULT_SCAN(SNOWFLAKE.ALERT.GET_CONDITION_QUERY_UUID())));
      ...
    END;
```

Show lessSee more

Scroll to top

## Manually executing alerts

In some cases, you might need to execute an alert manually. For example:

* If you are creating a new alert, you might want to verify that the alert works as you would expect.
* You might want to execute the alert at a specific point in your data pipeline. For example, you might want to execute the
  alert at the end of a stored procedure call.

To execute an alert manually, run the [EXECUTE ALERT](../sql-reference/sql/execute-alert) command:

CopyExpand

```
EXECUTE ALERT my_alert;
```

Show lessSee more

Scroll to top

Note

You cannot use EXECUTE ALERT to execute an [alert on new data](#label-alerts-type-streaming).

The EXECUTE ALERT command manually triggers a single run of an alert, independent of the schedule defined for the alert.

You can execute this command interactively. You can also execute this command from within a stored procedure or a Snowflake
Scripting block.

For details on the privileges required to run this command and the effect of this command on suspended, running, and scheduled
alerts, see [EXECUTE ALERT](../sql-reference/sql/execute-alert).

## Suspending and resuming an alert

If you need to prevent an alert from executing temporarily, you can suspend the alert by executing the
[ALTER ALERT … SUSPEND](../sql-reference/sql/alter-alert) command. For example:

CopyExpand

```
ALTER ALERT my_alert SUSPEND;
```

Show lessSee more

Scroll to top

To resume a suspended alert, execute the [ALTER ALERT … RESUME](../sql-reference/sql/alter-alert) command. For example:

CopyExpand

```
ALTER ALERT my_alert RESUME;
```

Show lessSee more

Scroll to top

Note

If you are not the owner of the alert, you must have the OPERATE privilege on the alert to suspend or resume the alert.

## Modifying an alert

To modify the properties of an alert, execute the [ALTER ALERT](../sql-reference/sql/alter-alert) command.

Note

* You must be the owner of the alert to modify the properties of the alert.
* You cannot change an [alert on new data](#label-alerts-type-streaming) to an
  [alert on a schedule](#label-alerts-type-scheduled). Similarly, you cannot change an alert on a schedule to an alert
  on new data.

For example:

* To change the warehouse for the alert named `my_alert` to `my_other_warehouse`, execute:

  CopyExpand

  ```
  ALTER ALERT my_alert SET WAREHOUSE = my_other_warehouse;
  ```

  Show lessSee more

  Scroll to top
* To change the schedule for the alert named `my_alert` to be evaluated every 2 minutes, execute:

  CopyExpand

  ```
  ALTER ALERT my_alert SET SCHEDULE = '2 minutes';
  ```

  Show lessSee more

  Scroll to top
* To change the condition for the alert named `my_alert` so that you are alerted if any rows in the table named `gauge` have
  values greater than `300` in the `gauge_value` column, execute:

  CopyExpand

  ```
  ALTER ALERT my_alert MODIFY CONDITION EXISTS (SELECT gauge_value FROM gauge WHERE gauge_value>300);
  ```

  Show lessSee more

  Scroll to top
* To change the action for the alert named `my_alert` to `CALL my_procedure()`, execute:

  CopyExpand

  ```
  ALTER ALERT my_alert MODIFY ACTION CALL my_procedure();
  ```

  Show lessSee more

  Scroll to top

## Dropping an alert

To drop an alert, execute the [DROP ALERT](../sql-reference/sql/drop-alert) command. For example:

CopyExpand

```
DROP ALERT my_alert;
```

Show lessSee more

Scroll to top

To drop an alert without raising an error if the alert does not exist, execute:

CopyExpand

```
DROP ALERT IF EXISTS my_alert;
```

Show lessSee more

Scroll to top

Note

You must be the owner of the alert to drop the alert.

## Viewing details about an alert

To list the alerts that have been created in an account, database, or schema, execute the [SHOW ALERTS](../sql-reference/sql/show-alerts)
command. For example, to list the alerts that were created in the current schema, run the following command:

CopyExpand

```
SHOW ALERTS;
```

Show lessSee more

Scroll to top

This command lists the alerts that you own and the alerts that you have the MONITOR or OPERATE privilege on.

To view the details about a specific alert, execute the [DESCRIBE ALERT](../sql-reference/sql/desc-alert) command. For example:

CopyExpand

```
DESC ALERT my_alert;
```

Show lessSee more

Scroll to top

Note

If you are not the owner of the alert, you must have the MONITOR or OPERATE privilege on the alert to view the details of the
alert.

## Cloning an alert

You can clone an alert (either by using [CREATE ALERT … CLONE](../sql-reference/sql/create-alert) or by cloning the
database or schema containing the alert).

If you are cloning a serverless alert, you don’t need to use a role that has the global EXECUTE MANAGED ALERT privilege. However,
you will not be able to resume that alert until the role that owns the alert has been granted the EXECUTE MANAGED ALERT privilege.

## Monitoring the execution of alerts

To monitor the execution of the alerts, you can:

* Check the results of the action that was specified for the alert. For example, if the action inserted rows into a table, you can
  check the table for new rows.
* View the history of alert executions by using one of the following:

  + The [ALERT\_HISTORY](../sql-reference/functions/alert_history) table function in the INFORMATION\_SCHEMA schema.

    For example, to view the executions of alerts over the past hour, execute the following statement:

    CopyExpand

    ```
    SELECT *
    FROM
      TABLE(INFORMATION_SCHEMA.ALERT_HISTORY(
        SCHEDULED_TIME_RANGE_START
          =>dateadd('hour',-1,current_timestamp())))
    ORDER BY SCHEDULED_TIME DESC;
    ```

    Show lessSee more

    Scroll to top
  + The [ALERT\_HISTORY](../sql-reference/account-usage/alert_history) view in the ACCOUNT\_USAGE schema in the shared
    SNOWFLAKE database.

In the query history, the name of the user who executed the query will be SYSTEM. (The alerts are run by the
[system service](tasks-intro.html#label-system-service).)

## Viewing the query history of a serverless alert

To view the query history of a serverless alert, you must be the owner of the alert, or you must use a role that has the
MONITOR or OPERATE privilege on the alert itself. (This differs from alerts that use one your warehouses, which require the
MONITOR or OPERATOR privilege on the warehouse.)

For example, suppose that you want to use the `my_alert_role` role when viewing the query history of the alert `my_alert`.
If `my_alert_role` is not the owner of `my_alert`, you must [grant](../sql-reference/sql/grant-privilege) that role the
MONITOR or OPERATE privilege on the alert:

CopyExpand

```
GRANT MONITOR ON ALERT my_alert TO ROLE my_alert_role;
```

Show lessSee more

Scroll to top

After the role is granted this privilege, you can use the role to view the query history of the alert:

CopyExpand

```
USE ROLE my_alert_role;
```

Show lessSee more

Scroll to top

CopyExpand

```
SELECT query_text FROM TABLE(INFORMATION_SCHEMA.QUERY_HISTORY())
  WHERE query_text LIKE '%Some condition%'
    OR query_text LIKE '%Some action%'
  ORDER BY start_time DESC;
```

Show lessSee more

Scroll to top
