---
title: "Troubleshooting skipped or failed dynamic table refreshes"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-troubleshoot-refresh"
---

# Troubleshooting skipped or failed dynamic table refreshes

This topic helps you troubleshoot skipped or failed refreshes. For slow refresh diagnostics,
see [Monitor dynamic table performance](dynamic-tables-performance-monitor).

When [monitoring your dynamic table refreshes](dynamic-tables-monitor), note the following:

* If you see many SKIPPED entries, see [Skipped refreshes](#label-dynamic-tables-monitoring-skipped-refreshes).
* If you see consistent FAILED entries, see [Failed refreshes](#label-dynamic-tables-monitoring-failed-refreshes).
* If you see a SCHEDULED or EXECUTING entry stuck for a long time, see
  [Monitor dynamic table performance](dynamic-tables-performance-monitor).

## Skipped refreshes

Dynamic tables refresh on a schedule. When a scheduled refresh starts, the following situations might cause the refresh to skip:

* If the dynamic table being refreshed has another dynamic table upstream, and the refresh for the upstream failed or was skipped.
* If a previous refresh for the dynamic table is still running.
* If the dynamic table’s refresh often takes longer than the target lag or there’s a significant difference between the target and actual lag,
  Snowflake might skip a refresh to reduce the rate of future skips.

  For instance, if a dynamic table has a 1-minute target lag but typically takes one hour to refresh, the system adjusts the “actual lag”
  accordingly.

  To improve refresh performance, see [Optimize dynamic table performance](dynamic-tables-performance-optimize).

Manual refreshes are never skipped but they can cause other scheduled refreshes to skip, especially if you perform frequent manual refreshes
on a dynamic table. Doing so can prevent downstream dynamic tables from refreshing. For this reason, Snowflake recommends that you avoid
frequently performing manual refreshes on a dynamic table with downstream dynamic tables that are expected to refresh according to target lag.

## Failed refreshes

Refresh failures are typically caused by issues with the dynamic table’s query definition, input
data (for example, parsing errors), or upstream failures.

### Find failed refreshes

To find failed refreshes, query the refresh history:

CopyExpand

```
SELECT
  name,
  data_timestamp,
  state,
  state_code,
  state_message
FROM TABLE(INFORMATION_SCHEMA.DYNAMIC_TABLE_REFRESH_HISTORY(
  NAME_PREFIX => 'MY_DB.MY_SCHEMA',
  ERROR_ONLY => TRUE
));
```

Show lessSee more

Scroll to top

You can also use the Refresh History page in Snowsight to view failed refreshes.
The Source Data Timestamp column shows the time of the last successful refresh. A failed
refresh doesn’t advance this value. If it’s far behind the target lag, your dynamic table is
lagging.

### Diagnose failed refreshes

Use the Query Profile to troubleshoot by selecting Show query profile next to
each refresh. This shows the execution graph of the query.

![Highlighting show query profile option in Snowsight.](../_images/dynamic-tables-snowsight-show-query-profile.png)

Use the Graph view in Snowsight to visualize dependencies. A failed or suspended
upstream dynamic table causes its downstream tables to fail. For more information, see
[View the graph of tables connected to your dynamic tables](dynamic-tables-monitor.html#label-dts-monitor-graph-view).

### Query event tables for failures

You can query an event table to find refresh failures across your dynamic tables:

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

For more information about configuring event tables and setting up alerts, see
[Event table monitoring and alerts for dynamic tables](dynamic-tables-monitor-event-table-alerts).
