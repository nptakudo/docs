---
title: "Event table monitoring and alerts for dynamic tables"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-monitor-event-table-alerts"
---

# Event table monitoring and alerts for dynamic tables

This topic discusses how to query an event table that provides information about your refresh status and how to set up alerts on new data in
an event table.

## Query an event table to monitor refreshes

When a dynamic table is refreshed, you can configure Snowflake to record an event that provides information about the status of the refresh
operation. The event is recorded in the [active event table](../developer-guide/logging-tracing/event-table-setting-up) associated with
the dynamic table.

For example, suppose that you have [associated an event table with a database](../developer-guide/logging-tracing/event-table-setting-up.html#label-logging-event-table-object). When a
dynamic table in that database is refreshed, Snowflake records an event to that event table.

You can query the events logged in this active event table to monitor your dynamic table refreshes.

For example, to get the timestamp, dynamic table name, query ID, and error message for errors with dynamic tables in the database `my_db`,
do the following:

CopyExpand

```
SELECT
    timestamp,
    resource_attributes:"snow.executable.name"::VARCHAR AS dt_name,
    resource_attributes:"snow.query.id"::VARCHAR AS query_id,
    value:message::VARCHAR AS error
  FROM my_event_table
  WHERE
    resource_attributes:"snow.executable.type" = 'DYNAMIC_TABLE' AND
    resource_attributes:"snow.database.name" = 'MY_DB' AND
    value:state = 'FAILED'
  ORDER BY timestamp DESC;
```

Show lessSee more

Scroll to top

Expand

```
+-------------------------+------------------+--------------------------------------+---------------------------------------------------------------------------------+
| TIMESTAMP               | DT_NAME          | QUERY_ID                             | ERROR                                                                           |
|-------------------------+------------------+--------------------------------------+---------------------------------------------------------------------------------|
| 2025-02-17 21:40:45.444 | MY_DYNAMIC_TABLE | 01ba7614-0107-e56c-0000-a995024f304a | SQL compilation error:                                                          |
|                         |                  |                                      | Failure during expansion of view 'MY_DYNAMIC_TABLE': SQL compilation error:     |
|                         |                  |                                      | Object 'MY_DB.MY_SCHEMA.MY_BASE_TABLE' does not exist or not authorized.        |
+-------------------------+------------------+--------------------------------------+---------------------------------------------------------------------------------+
```

Show lessSee more

Scroll to top

The following example retrieves all columns for upstream errors with dynamic tables in the schema `my_schema`:

CopyExpand

```
SELECT *
  FROM my_event_table
  WHERE
    resource_attributes:"snow.executable.type" = 'DYNAMIC_TABLE' AND
    resource_attributes:"snow.schema.name" = 'MY_SCHEMA' AND
    value:state = 'UPSTREAM_FAILURE'
  ORDER BY timestamp DESC;
```

Show lessSee more

Scroll to top

Expand

```
+-------------------------+-----------------+-------------------------+-------+----------+-------------------------------------------------+-------+------------------+-------------+-----------------------------+-------------------+-------------------------------+-----------+
| TIMESTAMP               | START_TIMESTAMP | OBSERVED_TIMESTAMP      | TRACE | RESOURCE | RESOURCE_ATTRIBUTES                             | SCOPE | SCOPE_ATTRIBUTES | RECORD_TYPE | RECORD                      | RECORD_ATTRIBUTES | VALUE                         | EXEMPLARS |
|-------------------------+-----------------+-------------------------+-------+----------+-------------------------------------------------+-------+------------------+-------------+-----------------------------+-------------------+-------------------------------+-----------|
| 2025-02-17 21:40:45.486 | NULL            | 2025-02-17 21:40:45.486 | NULL  | NULL     | {                                               | NULL  | NULL             | EVENT       | {                           | NULL              | {                             | NULL      |
|                         |                 |                         |       |          |   "snow.database.id": 49,                       |       |                  |             |   "name": "refresh.status", |                   |   "state": "UPSTREAM_FAILURE" |           |
|                         |                 |                         |       |          |   "snow.database.name": "MY_DB",                |       |                  |             |   "severity_text": "WARN"   |                   | }                             |           |
|                         |                 |                         |       |          |   "snow.executable.id": 487426,                 |       |                  |             | }                           |                   |                               |           |
|                         |                 |                         |       |          |   "snow.executable.name": "MY_DYNAMIC_TABLE_2", |       |                  |             |                             |                   |                               |           |
|                         |                 |                         |       |          |   "snow.executable.type": "DYNAMIC_TABLE",      |       |                  |             |                             |                   |                               |           |
|                         |                 |                         |       |          |   "snow.owner.id": 2601,                        |       |                  |             |                             |                   |                               |           |
|                         |                 |                         |       |          |   "snow.owner.name": "DATA_ADMIN",              |       |                  |             |                             |                   |                               |           |
|                         |                 |                         |       |          |   "snow.owner.type": "ROLE",                    |       |                  |             |                             |                   |                               |           |
|                         |                 |                         |       |          |   "snow.schema.id": 411,                        |       |                  |             |                             |                   |                               |           |
|                         |                 |                         |       |          |   "snow.schema.name": "MY_SCHEMA"               |       |                  |             |                             |                   |                               |           |
|                         |                 |                         |       |          | }                                               |       |                  |             |                             |                   |                               |           |
+-------------------------+-----------------+-------------------------+-------+----------+-------------------------------------------------+-------+------------------+-------------+-----------------------------+-------------------+-------------------------------+-----------+
```

Show lessSee more

Scroll to top

For information about the role that you need to use to query the event table and the conditions that you can use to filter the results, see
[Set up an alert on new data](#label-dynamic-tables-monitoring-sql-events-alert).

## Set up alerts on new data to monitor refreshes

[As mentioned earlier](#label-dynamic-tables-monitoring-sql-events), when a dynamic table is refreshed, an event is logged in the
event table to indicate whether the refresh succeeded or failed. You can set up an [alert on new data](alerts.html#label-alerts-type-streaming) to
monitor the event table. You can configure the alert to [send a notification](notifications/about-notifications) when a
refresh fails.

The next sections explain how to set up the event logging to capture the events, how to set up the alert, and how to interpret
the events recorded in the event table:

* [Set the severity level of the events to capture](#label-dynamic-tables-monitoring-sql-events-level)
* [Set up an alert on new data](#label-dynamic-tables-monitoring-sql-events-alert)
* [Information logged for dynamic table events](#label-dynamic-tables-monitoring-sql-events-table-reference)

Note

Logging events for dynamic tables incurs costs. See [Costs of telemetry data collection](../developer-guide/logging-tracing/logging-tracing-billing).

### Set the severity level of the events to capture

Note

If you do not set the severity level, no events will be captured.

To set up dynamic table events to be recorded to the event table,
[set the severity level of events](../developer-guide/logging-tracing/telemetry-levels) that you want captured in the event
table. Events are captured at the following levels:

* `ERROR`: Refresh failure events.
* `WARN`: Failures to refresh upstream dynamic tables and refresh failure events.
* `INFO`: Successful refresh events, failures to refresh upstream dynamic tables, and refresh failure events.

To set the level, set the [LOG\_LEVEL](../sql-reference/parameters.html#label-log-level) parameter for the account or object. You can set the level for:

* All objects in the account.
* All objects in a database or schema.
* A specific dynamic table.

For example:

* To capture ERROR-level events and messages for all objects in the account, execute
  [ALTER ACCOUNT SET LOG\_LEVEL](../sql-reference/sql/alter-account):

  CopyExpand

  ```
  ALTER ACCOUNT SET LOG_LEVEL = ERROR;
  ```

  Show lessSee more

  Scroll to top

  Note that this level affects all types of objects in the account, including UDFs, stored procedures, dynamic tables, and tasks.
* To capture INFO-level events and messages for all objects in the database `my_db`, execute
  [ALTER DATABASE … SET LOG\_LEVEL](../sql-reference/sql/alter-database):

  CopyExpand

  ```
  ALTER DATABASE my_db SET LOG_LEVEL = INFO;
  ```

  Show lessSee more

  Scroll to top

  Similar to the case of setting the level on the account, setting the level on the database affects all types of objects in the
  database, including UDFs, stored procedures, dynamic tables, and tasks.
* To capture WARN-level events for the dynamic table `my_dynamic_table`, execute
  [ALTER DYNAMIC TABLE … SET LOG\_LEVEL](../sql-reference/sql/alter-dynamic-table):

  CopyExpand

  ```
  ALTER DYNAMIC TABLE my_dynamic_table SET LOG_LEVEL = WARN;
  ```

  Show lessSee more

  Scroll to top

### Set up an alert on new data

After you set the severity level for logging events, you can set up an alert on new data to monitor the event table for new events
that indicate a failure in a dynamic table refresh. An alert on new data is triggered when new rows in the event table are
inserted and meet the condition specified in the alert.

Note

To create the alert on new data, you must use a role that has been granted the required privileges to query the event table.

* If the alert condition queries the default event table ([SNOWFLAKE.TELEMETRY.EVENTS](../developer-guide/logging-tracing/event-table-setting-up.html#label-logging-event-table-default))
  or the predefined view ([SNOWFLAKE.TELEMETRY.EVENTS\_VIEW view](../sql-reference/telemetry/events_view)),
  see [Roles for access to the default event table and EVENTS\_VIEW](../developer-guide/logging-tracing/event-table-setting-up.html#label-logging-event-table-default-roles).

  To manage access to the EVENTS\_VIEW view, see [Manage access to the EVENTS\_VIEW view](../developer-guide/logging-tracing/event-table-setting-up.html#label-logging-event-table-default-event-view-manage-access).
* If the alert condition queries a custom event table, see [Access control privileges for event tables](../developer-guide/logging-tracing/event-table-operations.html#label-event-table-access-control-privileges).

  To manage access to a custom event table, see [Managing access to event table data](../developer-guide/logging-tracing/event-table-operations.html#label-event-table-access-views).

In the alert condition, to query for dynamic table events, select rows where
`resource_attributes:"snow.executable.type" = 'DYNAMIC_TABLE'`. To narrow down the list of events, you can filter on the
following columns:

* To restrict the results to dynamic tables in a specific database, use `resource_attributes:"snow.database.name"`.
* To return events where the refresh failed due to an error with the dynamic table, use `value:state = 'FAILED'`.
* To return events where the refresh failed due to an error with an upstream dynamic table, use
  `value:state = 'UPSTREAM_FAILURE'`.

For information on the values logged for a dynamic table event, see
[Information logged for dynamic table events](#label-dynamic-tables-monitoring-sql-events-table-reference).

Note

The `timestamp` column in the event table stores values in UTC. If you use a scheduled alert with a timestamp filter
(for example, `timestamp > DATEADD('minute', -5, CURRENT_TIMESTAMP())`), convert the current timestamp to UTC to ensure
accurate comparisons:

CopyExpand

```
timestamp > DATEADD('minute', -5, CONVERT_TIMEZONE('UTC', CURRENT_TIMESTAMP()))
```

Show lessSee more

Scroll to top

For example, the following statement creates an alert on new data that performs an action when refreshes fail for dynamic tables
in the database `my_db`. The example assumes that:

* Your active event table is the [default event table](../developer-guide/logging-tracing/event-table-setting-up.html#label-logging-event-table-default) (SNOWFLAKE.TELEMETRY.EVENTS).
* You have [set up a webhook notification integration](notifications/webhook-notifications) for that Slack
  channel.

CopyExpand

```
CREATE ALERT my_alert_on_dt_refreshes
  IF( EXISTS(
    SELECT * FROM SNOWFLAKE.TELEMETRY.EVENT_TABLE
      WHERE resource_attributes:"snow.executable.type" = 'dynamic_table'
        AND resource_attributes:"snow.database.name" = 'my_db'
        AND record:"name" = 'refresh.status'
        AND record:"severity_text" = 'ERROR'
        AND value:"state" = 'FAILED'))
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

### Information logged for dynamic table events

When a dynamic table refreshes, an event is logged to the event table. The following sections describe the event table row that
represents the event:

* [Event table column values](#label-dynamic-tables-monitoring-sql-events-columns)
* [Key-value pairs in the resource\_attributes column](#label-dynamic-tables-monitoring-sql-events-resource-attributes)
* [Key-value pairs in the record column](#label-dynamic-tables-monitoring-sql-events-record)

#### Event table column values

When a dynamic table refreshes, a row with the following values is inserted into the event table.

Note

If a column is not listed below, the column value is NULL for the event.

| Column | Data type | Description |
| --- | --- | --- |
| `timestamp` | TIMESTAMP\_NTZ | The UTC timestamp when an event was created. |
| `observed_timestamp` | TIMESTAMP\_NTZ | A UTC time used for logs. Currently, this is the same value that is in the `timestamp` column. |
| `resource_attributes` | OBJECT | [Attributes](#label-dynamic-tables-monitoring-sql-events-resource-attributes) that identify the dynamic table that was refreshed. |
| `record_type` | STRING | The event type, which is `EVENT` for dynamic table refreshes. |
| `record` | OBJECT | [Details](#label-dynamic-tables-monitoring-sql-events-record) about the status of the dynamic table refresh. |
| `value` | VARIANT | [The status](#label-dynamic-tables-monitoring-sql-events-value) of the dynamic table refresh and, if the refresh failed, the error message for the failure. |

See moreShow less

Expand

#### Key-value pairs in the `resource_attributes` column

The `resource_attributes` column contains an [OBJECT](../sql-reference/data-types-semistructured.html#label-data-type-object) value with the following key-value pairs:

| Attribute name | Attribute type | Description | Example |
| --- | --- | --- | --- |
| `snow.database.id` | INTEGER | The internal/system-generated identifier of the database containing the dynamic table. | `12345` |
| `snow.database.name` | VARCHAR | The name of the database containing the dynamic table. | `MY_DATABASE` |
| `snow.executable.id` | INTEGER | The internal/system-generated identifier of the dynamic table that was refreshed. | `12345` |
| `snow.executable.name` | VARCHAR | The name of the dynamic table that was refreshed. | `MY_DYNAMIC_TABLE` |
| `snow.executable.type` | VARCHAR | The type of the object. The value is `DYNAMIC_TABLE` for dynamic table events. | `DYNAMIC_TABLE` |
| `snow.owner.id` | INTEGER | The internal/system-generated identifier of the role with the OWNERSHIP privilege on the dynamic table. | `12345` |
| `snow.owner.name` | VARCHAR | The name of the role with the OWNERSHIP privilege on the dynamic table. | `MY_ROLE` |
| `snow.owner.type` | VARCHAR | The type of role that owns the object, for example `ROLE`. . If a Snowflake Native App owns the object, the value is `APPLICATION`. . Snowflake returns NULL if you delete the object because a deleted object does not have an owner role. | `ROLE` |
| `snow.query.id` | VARCHAR | ID of the query that refreshed the dynamic table. | `01ba7614-0107-e56c-0000-a995024f304a` |
| `snow.schema.id` | INTEGER | The internal/system-generated identifier of the schema containing the dynamic table. | `12345` |
| `snow.schema.name` | VARCHAR | The name of the schema containing the dynamic table. | `MY_SCHEMA` |
| `snow.warehouse.id` | INTEGER | The internal/system-generated identifier of the warehouse used to refresh the dynamic table. | `12345` |
| `snow.warehouse.name` | VARCHAR | The name of the warehouse used to refresh the dynamic table. | `MY_WAREHOUSE` |

See moreShow less

Expand

#### Key-value pairs in the `record` column

The `record` column contains an [OBJECT](../sql-reference/data-types-semistructured.html#label-data-type-object) value with the following key-value pairs:

| Key | Type | Description | Example |
| --- | --- | --- | --- |
| `name` | VARCHAR | The name of the event. The value is `refresh_status` for dynamic table refreshes. | `refresh_status` |
| `severity_text` | VARCHAR | The severity level of the event, which is one of the following values:   * `INFO`: The refresh succeeded. * `ERROR`: The refresh failed. * `WARN`: The refresh of an upstream dynamic table failed. | `INFO` |

See moreShow less

Expand

#### Key-value pairs in the `value` column

The `value` column contains an [VARIANT](../sql-reference/data-types-semistructured.html#label-data-type-variant) value with the following key-value pairs:

| Key | Type | Description | Example |
| --- | --- | --- | --- |
| `state` | VARCHAR | The state of the refresh, which can be one of the following values:   * `SUCCEEDED`: The refresh succeeded. * `ERROR`: The refresh failed. * `UPSTREAM_FAILURE`: The refresh failed due to a failure to refresh a dynamic table that this dynamic table depends on. | `SUCCEEDED` |
| `message` | VARCHAR | If the value in `state` is `ERROR`, this column includes the error message. | `SQL compilation error:\nFailure during expansion of view 'MY_DYNAMIC_TABLE': SQL compilation error:\nObject 'MY_DB.MY_SCHEMA.MY_BASE_TABLE' does not exist or not authorized.` |

See moreShow less

Expand
