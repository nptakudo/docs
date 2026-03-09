---
title: "Understanding dynamic table initialization and refresh"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-refresh"
---

# Understanding dynamic table initialization and refresh

A dynamic table’s content is defined by a query and automatically updates — called a refresh — when the underlying data changes.
This process analyzes the query to keep the table current.

The following sections explain dynamic table refresh in more detail:

| Section | Description |
| --- | --- |
| [Understanding dynamic table initialization](#label-dynamic-tables-initialization) | Introduces initialization, or in other words, the initial data population when you create a dynamic table. You can specify when the initial refresh occurs. |
| [Understanding manual and scheduled refresh options](#label-dynamic-tables-scheduled-refresh) | An overview of dynamic table refresh. Dynamic tables refresh on a schedule unless manually refreshed. |
| [Dynamic table refresh modes](#label-dynamic-tables-intro-refresh-modes) | Dynamic tables support different refresh modes: incremental, full, and AUTO. |
| [How data is refreshed when a dynamic table depends on other dynamic tables](#label-dynamic-tables-intro-refresh-dependencies) | Learn how dynamic tables refresh in relation to their dependencies. |
| [Understanding the effects of changes to columns in base tables](#label-dynamic-tables-changes-to-base-tables) |  |

See moreShow less

Expand

## Understanding dynamic table initialization

When you [create a dynamic table](dynamic-tables-create), its initial refresh takes place either synchronously at creation
or at a scheduled time. The initial data population, or initialization, depends on when this initial refresh occurs.

Dynamic tables refresh based on the specified [target lag](dynamic-tables-target-lag), which sets the maximum allowed delay
between updates to the base tables and the dynamic table’s content. If you set `INITIALIZE = ON_CREATE` (default), the table is initialized
immediately. If you set `INITIALIZE = ON_SCHEDULE`, initialization happens within the specified target lag timeframe.

For example, consider a dynamic table, `DT1`, with a target lag of 30 minutes. The initial data population for `DT1` can occur as follows:

* If `DT1` is set to refresh synchronously at creation (`ON_CREATE`), it initializes at creation.
* If `DT1` is set to refresh at a scheduled time (`ON_SCHEDULE`), it initializes within 30 minutes.

In scenarios with downstream dependencies, refresh behavior depends on the dependencies. For example, if dynamic table `DT1` has a
[downstream](dynamic-tables-target-lag) target lag and `DT2`, which depends on `DT1`, has a 30-minute target lag, `DT1`
refreshes only when `DT2` refreshes.

For `DT1`:

* If set to refresh synchronously at creation, it initializes immediately. If initialization fails, the creation process stops, providing
  immediate feedback on any errors.
* If set to refresh at a scheduled time, initialization depends on when `DT2` refreshes.

Initialization can take some time, depending on how much data is scanned. To track progress, see [Troubleshoot dynamic table creation](dynamic-tables-create.html#label-dynamic-tables-troubleshoot-create).

## Understanding manual and scheduled refresh options

Dynamic tables are refreshed on a schedule that’s determined by the [target lag](dynamic-tables-target-lag). Every time a
dynamic table is read, the data freshness is within the time period defined by the target lag.

You can manually refresh your dynamic tables to get the latest data using the ALTER DYNAMIC TABLE … REFRESH command or Snowsight.
For more information, see [Manually refresh dynamic tables](dynamic-tables-manual-refresh).

Dynamic table refresh timeouts are controlled by the [STATEMENT\_TIMEOUT\_IN\_SECONDS](../sql-reference/parameters.html#label-statement-timeout-in-seconds) parameter, which sets the maximum allowed
duration at the account or warehouse level before a refresh is automatically canceled.

### How target lag affects scheduled refreshes

Target lag controls the frequency of scheduled refreshes. To manually manage refreshes, set your dynamic table’s target lag to DOWNSTREAM and
ensure that all downstream dynamic tables are also set to DOWNSTREAM.

Setting the entire Directed Acyclic Graph (DAG)’s target lag to DOWNSTREAM essentially disables scheduled refreshes because the final dynamic
table controls the refresh schedule. If no dynamic table has a time-based target lag, the pipeline is suspended for scheduled refreshes. In
this case, manually refreshing the most downstream table automatically refreshes any upstream dependencies.

Setting the target lag to DOWNSTREAM doesn’t specify exact times. Instead, Snowflake picks a refresh cadence to attempt to keep the lag under
the target value. For example, a dynamic table with a target lag of 4 hours might refresh every 3.5 hours.

To specify exact times, you can use a task with a CRON schedule. For more information, see [Manually refresh dynamic tables](dynamic-tables-manual-refresh).

## Dynamic table refresh modes

Dynamic tables support three refresh modes: auto, incremental, and full.
You can either set the refresh mode to [AUTO](../sql-reference/sql/create-dynamic-table.html#label-create-dt-refresh-mode)
or set it explicitly:

* **AUTO refresh mode:** When using the `AUTO` parameter, Snowflake automatically selects the most cost- and time-effective refresh mode
  based on query complexity, supported constructs, operators, functions, and expected performance. This decision is made only once at the time
  of table creation. If incremental refresh is [unsupported](dynamic-tables-supported-queries) or
  [inefficient](dynamic-tables-performance-optimize-query), Snowflake chooses full refresh instead.

  For example, if a dynamic table references a view and the view’s definition changes asynchronously, the refresh mode remains unchanged. If
  the original decision was incremental but becomes unsupported (for example, due to an upstream view change), the refresh will fail with an
  error like `Dynamic table can no longer be refreshed incrementally because an upstream view changed.`

  To change the refresh mode, recreate the dynamic table using the CREATE OR REPLACE DYNAMIC TABLE command.
* **Incremental refresh mode:** This mode analyzes the dynamic table’s query and calculates changes since the last refresh. It then merges these
  changes into the table.
* **Full refresh mode:** This mode executes the dynamic table’s query and completely replaces the previously materialized results.

For guidance on when to use incremental refresh versus full refresh, see [Choose a refresh mode](dynamic-tables-performance-optimize.html#label-dt-optimize-refresh).
To check which refresh mode an existing dynamic table uses, see
[Refresh mode](dynamic-tables-performance-monitor.html#label-dynamic-tables-monitoring-refresh-mode).

Important

Dynamic tables in incremental refresh mode can’t be downstream from dynamic tables with full refresh mode. This is because
incremental refresh mode is incompatible with the complete row changes that occur during each refresh of an upstream full
refresh table.

## How data is refreshed when a dynamic table depends on other dynamic tables

When a dynamic table’s lag is set as a time measure, the automated refresh process schedules refreshes to best meet the target lag times.

In order to keep data consistent in cases when [one dynamic table depends on another](dynamic-tables-create.html#label-dynamic-tables-intro-dependencies), the
process refreshes all dynamic tables in an account at compatible times. The timing of less frequent refreshes coincides with the timing of
more frequent refreshes. If refreshes take too long, the scheduler may skip refreshes to try to stay up to date. However, snapshot isolation
is preserved.

For example, suppose that dynamic table `DT1` has a target lag of two minutes and queries dynamic table `DT2`, which has a target lag of
one minute. The process might determine that `DT1` should be refreshed every 96 seconds, and `DT2` every 48 seconds. As a result, the
process might apply the following schedule:

| Specific Point in Time | Dynamic Tables Refreshed |
| --- | --- |
| 2022-12-01 00:00:00 | DT1, DT2 |
| 2022-12-01 00:00:48 | DT2 |
| 2022-12-01 00:01:36 | DT1, DT2 |
| 2022-12-01 00:02:24 | DT2 |

See moreShow less

Expand

The target lag of a dynamic table can’t be shorter than the target lag of the dynamic tables it depends on. For example, suppose that:

* `DT1` queries dynamic tables `DT2` and `DT3`.
* `DT2` has a target lag of five minutes.
* `DT3` has a target lag of one minute.

This means that the target lag time for `DT1` must not be shorter than five minutes (that is, not shorter than the longer of the lag times
for `DT2` and `DT3`).

If you set the lag for `DT1` to five minutes, the process sets up a refresh schedule with these goals:

* Refresh `DT3` often enough to keep its lag below one minute.
* Refresh `DT1` and `DT2` together and often enough to keep their lags below five minutes.
* Ensure that the refresh for `DT1` and `DT2` coincides with a refresh of `DT3` to ensure snapshot isolation.

Important

Dynamic tables in incremental refresh mode can’t be downstream from dynamic tables with full refresh mode. This is because
incremental refresh mode is incompatible with the complete row changes that occur during each refresh of an upstream full
refresh table.

### Snapshot isolation

When a dynamic table refreshes, it ensures a consistent state by Time Traveling to the same data timestamp across all upstream dependencies.

For non-dynamic base tables, Time Travel works as usual, where it looks at the “wall-clock” commit time. This means that the contents of a
dynamic table are always consistent with a “snapshot” of the data in the base tables.

For upstream dynamic tables, Snowflake looks up the specific table version tagged with that data timestamp. This ensures that downstream tables
are always consistent with their ancestors. You don’t need to coordinate refresh schedules or worry about different lags; Snowflake automatically
aligns the snapshots to ensure data integrity across the pipeline.

Snapshot isolation isn’t guaranteed when you join multiple dynamic tables using a manual SELECT statement because ad hoc queries use the current
version of each table. Because each dynamic table commits its refresh independently, a manual join might capture different refresh states, even
if the dynamic tables share the same target lag or an upstream refresh is delayed. This means the results might not reflect a single, consistent
snapshot of the base data.

## Understanding the effects of changes to columns in base tables

When the underlying objects associated with a dynamic table change, the following behaviors apply:

| Change | Impact |
| --- | --- |
| * New column added to the base table. * Existing unused column removed in the base table. | None. If a new column is added to the base table or an unused column is deleted, no action occurs and refreshes continue as before. |
| * Underlying base table is recreated with identical column names and types. * Underlying base table column is recreated with the same name and type. * Changes to the policies on underlying base tables of dynamic tables with incremental refresh. | Reinitialization: The first refresh after recreation is [initialization](#label-dynamic-tables-initialization). |
| * Changes to underlying base table for dynamic tables created with `SELECT *` from base table. | The dynamic table fails to refresh and must be recreated to respond to the change. |
| * Changes to underlying base table for dynamic tables created with a column definition. | No impact to the dynamic table. |

See moreShow less

Expand
