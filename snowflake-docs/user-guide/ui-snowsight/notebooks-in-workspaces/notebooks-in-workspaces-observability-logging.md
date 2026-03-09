---
title: "Observability and logging for Notebooks in Workspaces"
url: "https://docs.snowflake.com/en/user-guide/ui-snowsight/notebooks-in-workspaces/notebooks-in-workspaces-observability-logging"
---

# Observability and logging for Notebooks in Workspaces

Feature — Generally Available

Available to all AWS and Azure commercial regions. PrivateLink is supported.

## Overview

Snowflake writes notebook logs to the container’s local file system and ingests them into an [event table](../../../developer-guide/logging-tracing/event-table-setting-up), which you can query to troubleshoot notebook runs, review execution history, and perform long-term analysis.

You can use an event table to centralize operational data for Notebooks in Workspaces; for example, with the following tasks:

* Troubleshooting scheduled runs (errors, warnings, timestamps)
* Auditing who ran what and when (when emitted by the workload and configured for collection)
* Creating dashboards for notebook activity (success/failure counts, run duration, noisy errors)

Note

There is typically a delay of three to five minutes before logs appear in the event table.

## Enable logging in your notebook code

By default, Python logging is set to `WARNING`. To capture application events, you must set the logging level to `INFO` or
`DEBUG`.

* Add the following code to your Python notebook or script:

CopyExpand

```
import logging

# Set the root logger to INFO level
logging.getLogger().setLevel(logging.INFO)

# Generate a test log entry
logging.info("APPLICATION_EVENT: Service initialization complete.")
```

Show lessSee more

Scroll to top

## Query logs using Snowflake Trail

You can view log entries in Snowsight through Snowflake Trail.

Note

Before you can view log messages, you must [enable telemetry data collection](../../../developer-guide/logging-tracing/logging-tracing-enabling).

### Identify your event table

* To find the event table for your account, run the following command in a SQL file:

CopyExpand

```
SHOW PARAMETERS LIKE 'event_table' IN ACCOUNT;
```

Show lessSee more

Scroll to top

### Query and analyze logs

After your event table has started collecting events, you can query it like any other table to filter by time range, severity, and workload identifiers.
For more information on event table schema and column definitions, see [Event table columns](../../../developer-guide/logging-tracing/event-table-columns).

* To investigate recent log events, run the following code (replacing the placeholder values with your actual values):

  CopyExpand

  ```
  SELECT
      TIMESTAMP,
      VALUE AS LOG_MESSAGE,
      RESOURCE_ATTRIBUTES:"snow.service.name"::string AS SERVICE_NAME,
      RECORD:"severity_text"::string AS SEVERITY
  FROM <database_name>.<schema_name>.<event_table_name>
  WHERE RECORD_TYPE = 'LOG'
    AND RESOURCE_ATTRIBUTES:"snow.service.name" = '<your_service_name>'
    AND TIMESTAMP > DATEADD(hour, -1, CURRENT_TIMESTAMP())
  ORDER BY TIMESTAMP DESC
  LIMIT 100;
  ```

  Show lessSee more

  Scroll to top

## View logs for scheduled notebook runs in Snowsight

Each scheduled notebook uses a notebook project object that stores deployed code, execution history, and artifacts.

To view logs for scheduled runs in Snowsight:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Catalog » Database Explorer.
3. Search for the database and schema containing the notebook project object.
4. Select the notebook project object, and then select the Run history tab.
5. For the run you want to inspect, in the Logs column, select Logs .

After you enable logging in your notebook code, your custom log messages and infrastructure initialization logs appear in this log view.

## Troubleshooting

* If you don’t see expected events, verify that your event table is created and that event logging is enabled and configured for your account and
  workloads.
* If scheduled runs fail, cross-check [notebook scheduling](notebooks-in-workspaces-schedule)
  and look for correlated errors in the event table during the same time window.
