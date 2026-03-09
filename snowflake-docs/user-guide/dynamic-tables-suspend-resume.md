---
title: "Suspend or resume dynamic tables"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-suspend-resume"
---

# Suspend or resume dynamic tables

This topic discusses why dynamic tables automatically suspend and how to manually suspend or resume your dynamic tables.

Suspended dynamic tables aren’t automatically refreshed; you can [manually refresh them](dynamic-tables-manual-refresh).

## Automatic dynamic table suspension

Dynamic tables are automatically suspended after five consecutive scheduled refresh errors. A successful refresh, including a manual refresh,
resets the error count to zero. For example, if a table fails two consecutive scheduled refreshes, then succeeds on the next, the error count
resets to zero.

Errors from manually triggered refreshes don’t count toward this limit.

Any dynamic tables dependent on a suspended table are also suspended.

You can view the current state (ACTIVE or SUSPENDED) of your dynamic tables using one of the following options:

SQLSnowsight

Execute the [DYNAMIC\_TABLE\_GRAPH\_HISTORY](../sql-reference/functions/dynamic_table_graph_history) table function:

CopyExpand

```
SELECT name, scheduling_state
  FROM TABLE (INFORMATION_SCHEMA.DYNAMIC_TABLE_GRAPH_HISTORY());
```

Show lessSee more

Scroll to top

In the output, the `SCHEDULING_STATE` column shows the state of your dynamic table (ACTIVE or SUSPENDED):

Expand

```
+-------------------+---------------------------------------------------------------------------------+
  | NAME              | SCHEDULING_STATE                                                                |
  |-------------------+---------------------------------------------------------------------------------|
  | DTSIMPLE          | {                                                                               |
  |                   |   "reason_code": "SUSPENDED_DUE_TO_ERRORS",                                     |
  |                   |   "reason_message": "The DT was suspended due to 5 consecutive refresh errors", |
  |                   |   "state": "SUSPENDED",                                                         |
  |                   |   "suspended_on": "2023-06-06 19:27:29.142 -0700"                               |
  |                   | }                                                                               |
  | DT_TEST           | {                                                                               |
  |                   |   "state": "ACTIVE"                                                             |
  |                   | }                                                                               |
  +-------------------+---------------------------------------------------------------------------------+
```

Show lessSee more

Scroll to top

To view the state of your dynamic tables, sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in). In the navigation menu, select Transformation » Dynamic tables.

You can view the state and last refresh status for your dynamic tables on this page. You can also filter by database or schema to narrow
the results.

## Manually suspend dynamic tables

Manually suspend a dynamic table when you don’t need it now but want to avoid refresh costs without dropping it, keeping it available for
future use. Suspension can also give you better control over refresh frequency, for example, if skips occur and you need time for
troubleshooting.

If you want to ensure refreshes at a specific time or occurrence, you can use a task or script that runs regularly to execute a manual
refresh because dynamic tables don’t guarantee exact refresh timing. This allows precise control over when your table refreshes.

You can use either the ALTER DYNAMIC TABLE … SUSPEND command or Snowsight to manually suspend dynamic tables, with the following limitations:

* Suspending a dynamic table also suspends the dynamic tables that are [downstream](dynamic-tables-target-lag.html#label-dts-upstream-downstream-relationships) from it.
* Suspending a dynamic table with incremental refresh beyond the Time Travel retention period of its base tables will cause it to fail on the
  next refresh after the dynamic table resumes.

SQLSnowsight

CopyExpand

```
ALTER DYNAMIC TABLE my_dynamic_table SUSPEND;
```

Show lessSee more

Scroll to top

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Transformation » Dynamic tables.
3. Find your dynamic table in the list and then select [![More options](../_images/snowsight-worksheet-explorer-ellipsis.png)](../_images/snowsight-worksheet-explorer-ellipsis.png) » Suspend.
4. In the popup, confirm that you want to suspend your dynamic table.

## Resume dynamic tables

To resume your dynamic tables, use either the ALTER DYNAMIC TABLE … RESUME command or Snowsight.

SQLSnowsight

CopyExpand

```
ALTER DYNAMIC TABLE my_dynamic_table RESUME;
```

Show lessSee more

Scroll to top

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Transformation » Dynamic tables.
3. Find your dynamic table in the list and then select [![More options](../_images/snowsight-worksheet-explorer-ellipsis.png)](../_images/snowsight-worksheet-explorer-ellipsis.png) » Resume.
4. In the popup, confirm that you want to resume your dynamic table.
