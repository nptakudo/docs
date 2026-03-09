---
title: "Analyzing query workloads with Performance Explorer"
url: "https://docs.snowflake.com/en/user-guide/performance-explorer"
---

# Analyzing query workloads with Performance Explorer

You can use Performance Explorer in Snowsight to monitor interactive metrics for SQL workloads.
The metrics show the overall health of your Snowflake environment, query activity, changes to warehouses,
and changes to tables.

## Benefits of Performance Explorer

Performance Explorer can help you answer the following key questions about Snowflake activity:

* **Overall activity:** Are queries generally succeeding, and can Snowflake users get their
  work done?
* **Change over time:** If query activity or resources look different from what I expected, what has
  changed and when did the changes occur?
* **Hot spots:** When I look for opportunities to take action, where should I focus my attention?

## Common use cases for Performance Explorer

Performance Explorer can help with the following use cases:

* **Investigating problem reports about queries or workloads:** If a Snowflake workload has started to behave
  differently, determine what else might have changed recently, such as the resources that the workload depends
  on or neighboring workload activity.
* **Proactively identifying hotspots:** If a warehouse or table shows persistent errors or saturation, identify
  and address the hotspot before it affects critical workloads.
* **Identifying optimization opportunities:** Find warehouses and tables that might be mismatched to the query
  activity they support, and adjust workloads and resources to make them compatible.

## Required privileges

A user granted the [ACCOUNTADMIN role](security-access-control-overview.html#label-access-control-overview-roles-system) can access
Performance Explorer.

Users that aren’t granted the ACCOUNTADMIN role must be granted the SNOWFLAKE.PERFORMANCE\_EXPLORER\_USER
application role to access Performance Explorer.

For example, if you want the user `jdoe` to be able to access Performance Explorer, set up a custom
role that allows access to Performance Explorer, and grant that role to the user `jdoe`:

CopyExpand

```
USE ROLE ACCOUNTADMIN;

CREATE ROLE pe_viewer_role;
GRANT APPLICATION ROLE SNOWFLAKE.PERFORMANCE_EXPLORER_USER TO ROLE pe_viewer_role;
GRANT ROLE pe_viewer_role TO USER jdoe;
```

Show lessSee more

Scroll to top

## Open Performance Explorer

To open Performance Explorer, complete the following steps:

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Monitoring » Performance Explorer.

Performance Explorer contains charts that show metrics related to your workloads and the general health of
your Snowflake environment.

To leave feedback about Performance Explorer, select Feedback.

## Understanding the Performance Explorer dashboard

You can monitor interactive metrics for SQL workloads by using charts on the Performance Explorer dashboard, and
you can apply filters to show metrics about only the query activity and resources that you’re interested in.

### Performance Explorer filters

At the top of the Performance Explorer dashboard, you can apply the following filters:

* Period - Select a time period, such as the last week, the last two weeks, or a custom range. The
  dashboard shows metrics for the specified period.

  Performance Explorer displays metrics for one week by default. It supports a period of up to one month,
  going back from the current date.

  Several Performance Explorer charts show the percentage of change compared to the previous period. The
  range of the previous period corresponds with the current period range. For example, if the current
  period is two weeks, then the previous period is the two weeks before the current period started.
* Warehouse - Select a warehouse to view metrics only for query activity that ran using that warehouse.
  To limit the warehouses in the list, use the search field. To clear the filter, select `X`.
* Database - Select a database to view metrics only for query activity that accessed that database.
  To limit the databases in the list, use the search field. To clear the filter, select `X`.
* Role - Select a role to view metrics only for query activity initiated by that role. To limit the
  roles in the list, use the search field. To clear the filter, select `X`.

### Performance Explorer charts

Performance Explorer displays metrics in different types of charts. It is important to understand the components
in each type of chart and how to interpret them.

The [Query health](#label-performance-explorer-monitoring) and
[Query activity](#label-performance-explorer-query-activities) sections
have line charts that are similar to the following image:

![Performance Explorer query line chart](../_images/performance-explorer-line-chart.png)

The following table describes the callouts in the image:

| Callout | Description |
| --- | --- |
| **1** | Select View details > to open the side panel. View details > appears when you hover over a chart. |
| **2** | Shows the average or median in the period. |
| **3** | Shows the percentage increased or decreased compared to the previous period. |
| **4** | Represents the value for one hour. The values are shown for an amount of time at the start of the interval. For example, if the interval is one hour, the value shown at 9 AM is for the interval from 9 AM to 10 AM. |

See moreShow less

Expand

Some charts include a large average or median value and the percentage of change for the period.
When there is more than one line, there is a key to the lines above the chart.

Some charts have an information icon next to the title. Hover over the icon for information about the metrics
in the chart.

You can hover over a point in the line chart to see the value for a specific hour:

![Performance Explorer line chart details](../_images/performance-explorer-line-details.png)

The [Top warehouses](#label-performance-explorer-top-warehouses) and
[Top tables](#label-performance-explorer-top-tables) sections have bar charts that are similar to
the following image:

![Performance Explorer bar chart](../_images/performance-explorer-bar-chart.png)

The following table describes the callouts in the image:

| Callout | Description |
| --- | --- |
| **1** | Select View details > to open the side panel. View details > appears when you hover over a chart. |
| **2** | Select a tab to show the metrics on the tab. |
| **3** | Shows the value of this metric for the current period. |
| **4** | Shows the percentage increased or decreased compared to the previous period. |
| **5** | Indicates that there is no data from the previous period for comparison. |

See moreShow less

Expand

On both line charts and bar charts, select View details > to open a side panel that displays more detailed information
about the metrics on the chart. The detailed information varies based on the metrics shown in the chart. Most side
panels present sortable tables that you can use to view metrics for specific warehouses, roles, databases, and queries in
the period.

You can select a custom period of time in a side panel by clicking where the custom period starts
and dragging to where the custom period ends. The following image shows an example of a side panel with a
custom period selected for Jan 22, 3 AM - Jan 24, 6 AM:

![Performance Explorer side panel](../_images/performance-explorer-side-panel.png)

In a side panel, you can select one of the following tabs:

* By warehouse - Shows the activity by warehouses in the period.
* By database - Shows the activity by databases in the period.
* By role - Shows the activity by roles in the period.
* By grouped queries - Shows the queries that were run in the period. Some queries are redacted for security
  reasons. For information about how queries are grouped, see [Use the Grouped Query History view in Snowsight](ui-snowsight-activity.html#label-snowsight-grouped-query-history).

  Note

  The By grouped queries tab is currently in preview.

If you select a custom period, these tabs refresh to show the metrics only for the selected custom period.

The [Top warehouses](#label-performance-explorer-top-warehouses) and
[Top tables](#label-performance-explorer-top-tables) sections also have events charts that are similar to the
following image:

![Performance Explorer events chart](../_images/performance-explorer-events-chart.png)

An events chart shows a sortable table of events for the type of object. You can examine the data for unexpected events.
For more information about warehouse events, see [WAREHOUSE\_EVENTS\_HISTORY view](../sql-reference/organization-usage/warehouse_events_history).
For more information about table events, see [TABLES view](../sql-reference/organization-usage/tables).

## Monitoring query health

This section of Performance Explorer includes metrics about the overall health of your Snowflake environment.
You can monitor these metrics to ensure that your users can run queries successfully and complete required tasks.

This section includes the following metrics:

| Metric | Unit | Description | Notes | More information |
| --- | --- | --- | --- | --- |
| Query failures/1K | Failures per 1000 | The number of queries that failed for every 1,000 queries that ran, including the following metrics:   * The large number above the line graph is the average number of failures for every 1,000 queries in the period. * The percentage value is the percentage of change in the rate of failures since the last period. * The line chart shows the number of failures for every 1,000 queries for each hour in the period. | This metric should be low or zero. If queries are failing, review the query history and errors, and then modify your queries to resolve the issues. | [Monitor query activity with Query History](ui-snowsight-activity) . . [QUERY\_HISTORY view](../sql-reference/account-usage/query_history) |
| Query retries/1K | Retries per 1000 | The number of queries that were retried for every 1,000 queries that ran, including the following metrics:   * The large number above the line graph is the average number of retries for every 1,000 queries in the period. * The percentage value is the percentage of change in the rate of retries since the last period. * The line chart shows the number of retries for every 1,000 queries for each hour in the period. | This metric should be low or zero. If queries are retrying, review the causes, and then take actions to prevent query retries. For example, if a query is retried because of an out-of-memory error, modifying warehouse settings might resolve the issue. | [QUERY\_HISTORY view](../sql-reference/account-usage/query_history) |
| Query overload % | Percent | The percentage of total run time that queries waited in a queue for warehouse resources, including the following metrics:   * The large number above the line graph is the median percentage of time that queries waited in a queue for   warehouse resources in the period. * The percentage value is the percentage of change in the number of queries that waited since the last period. * The line chart shows the percentage of time that queries waited in a queue for warehouse resources for each   hour in the period. | This metric should be low or zero. If queries are waiting before running, warehouse resources might be exhausted, causing queries to be queued until resources become available. | [Reducing queues](performance-query-warehouse-queue) |
| Query blocked % | Percent | The percentage of total run time that queries spent blocked waiting for a transaction lock on a resource, including the following metrics:   * The large number above the line graph is the median percentage of time spent blocked waiting for a lock   in the period. * The percentage value is the percentage of change in the amount of time that queries spent blocked since the last   period. * The line chart shows the percentage of time queries spent blocked waiting for a lock for each hour in   the period. | This metric should be low or zero. If queries were blocked, review the query history and errors, and then modify your queries to resolve the issues. | [Resource locking](../sql-reference/transactions.html#label-txn-locking) . . [Best practices for transactions](../sql-reference/transactions.html#label-transaction-best-practices) . . [LOCK\_WAIT\_HISTORY view](../sql-reference/organization-usage/lock_wait_history) . . [Monitor query activity with Query History](ui-snowsight-activity) . . [QUERY\_HISTORY view](../sql-reference/account-usage/query_history) |

See moreShow less

Expand

## Monitoring query activities

This section of Performance Explorer includes metrics about the query activity for a period. You can
monitor these metrics to track any large-scale changes in query activity that might affect resource usage or
the ability of users to run queries successfully.

This section includes the following metrics:

| Metric | Unit | Description | Notes | More information |
| --- | --- | --- | --- | --- |
| Query duration | Seconds | The amount of time it took for queries to complete for each hour of the period. The line chart shows the median amount of time for all queries, the amount of time for queries in the ninetieth percentile, and the amount of time for queries in the ninety-ninth percentile. | This metric varies widely depending on your data and the types of queries you are running. Queries with durations that change over time might be candidates for investigation and optimization. | [Exploring execution times](performance-query-exploring) . . [Optimizing query performance](performance-query-options) |
| Query throughput | Queries | The number of queries that ran each hour. | This metric can reveal changes in query activity, which might indicate new trends or changes in your workloads. | [Optimizing warehouses for performance](performance-query-warehouse) |
| Query wait time | Seconds | The amount of time that queries waited for warehouse resources or because of a lock on a resource. For information about the states (Overload, Provisioning, Repair, and Blocked), see [QUERY\_HISTORY view](../sql-reference/account-usage/query_history). | This metric should be low or zero. If queries are waiting before running, warehouse resources might be exhausted, causing queries to be queued until resources become available. | [Reducing queues](performance-query-warehouse-queue) . . [Resource locking](../sql-reference/transactions.html#label-txn-locking) |
| Query failures | Failures | The number of queries that failed for each hour in the period. | This metric should be low or zero. If queries are failing, review the query history and errors, and then modify your queries to resolve the issues. | [Monitor query activity with Query History](ui-snowsight-activity) . . [QUERY\_HISTORY view](../sql-reference/account-usage/query_history) |

See moreShow less

Expand

## Monitoring top warehouses

This section of Performance Explorer includes metrics about the warehouses in your Snowflake environment that
experienced the most changes in the period. You can monitor these metrics to ensure that your warehouses
are functioning as expected to support query activity. The metrics can also show whether any warehouses are
associated with trends in query activity that are unusual when compared to other warehouses. You can also determine
whether the composition of the workloads that warehouses run have changed.

All metrics in this section show the metric value and the percentage of change since the last period. The percentage of
change can be positive or negative, with positive change represented by an up arrow and negative change represented
by a down arrow. For each metric, Performance Explorer shows the 10 warehouses with the most changes. To view metrics
for more warehouses, select View details > on a chart to open the side panel. If this metric has no value from the last period
for a warehouse, — is shown instead of the percentage of change. There might be no value because the warehouse is
new, or because the event being measured is infrequent.

This section includes the following metrics:

| Metric | Tab | Unit | Description | Notes | More information |
| --- | --- | --- | --- | --- | --- |
| Warehouses with errors | Query failures/1K | Failures per 1000 | For each warehouse, the number of queries that failed for every 1,000 queries that ran. | This metric should be low or zero. If queries are failing, review the query history and errors, and then modify your queries to resolve the issues. | [Monitor query activity with Query History](ui-snowsight-activity) . . [QUERY\_HISTORY view](../sql-reference/account-usage/query_history) |
|  | Query OOM errors/1K | Errors per 1000 | For each warehouse, the number of queries that returned “out of memory” errors for every 1,000 queries that ran. | This metric should be low or zero. If queries are failing with “out of memory” errors, review the query history to determine which queries are failing for the warehouses, and then modify the warehouses that run the queries to avoid the errors. | [Monitor query activity with Query History](ui-snowsight-activity) . . [QUERY\_HISTORY view](../sql-reference/account-usage/query_history) . . [Queries too large to fit in memory](performance-query-warehouse-memory) |
|  | Query retries/1K | Retries per 1000 | For each warehouse, the number of queries that were retried for every 1,000 queries that ran. | This metric should be low or zero. If queries are retrying because warehouses are running out of memory, review the query history to determine which queries are retrying for the warehouses, and then modify the warehouses that run the queries to avoid the errors. | [Monitor query activity with Query History](ui-snowsight-activity) . . [QUERY\_HISTORY view](../sql-reference/account-usage/query_history) . . [Optimizing warehouses for performance](performance-query-warehouse) |
| Warehouses with spillage | % queries with bytes spilled | Percent | For each warehouse, the percentage of queries that spilled to local disk or remote cloud storage when they ran. | This metric should be low or zero. If queries are spilling to disk because warehouses are running out of memory, review the query history to determine which queries are spilling for the warehouses, and then modify the warehouses that run the queries to avoid the errors. | [Monitor query activity with Query History](ui-snowsight-activity) . . [QUERY\_HISTORY view](../sql-reference/account-usage/query_history) . . [Queries too large to fit in memory](performance-query-warehouse-memory) |
|  | % bytes spilled of total | Percent | For each warehouse, the percentage of bytes that spilled to local disk or remote cloud storage when they ran compared with the number of bytes read. | This metric should be low or zero. If queries are spilling to disk because warehouses are running out of memory, review the query history to determine which queries are spilling for the warehouses, and then modify the warehouses that run the queries to avoid the errors. | [Monitor query activity with Query History](ui-snowsight-activity) . . [QUERY\_HISTORY view](../sql-reference/account-usage/query_history) . . [Queries too large to fit in memory](performance-query-warehouse-memory) |
| Query wait time % | Overload % | Percent | For each warehouse, the proportion of total run time that queries waited because the warehouse was overloaded by the query workload. | This metric should be low or zero. If queries are waiting before running, warehouse resources might be exhausted, causing the warehouse to queue queries until resources become available. | [Reducing queues](performance-query-warehouse-queue) |
|  | Provisioning % | Percent | For each warehouse, the average proportion of total run time that queries waited for warehouse compute resources to provision, due to warehouse creation, resume, or resize. | This metric should be low or zero. If queries are waiting before running, warehouse resources might be exhausted, causing it to queue queries until resources become available. | [Reducing queues](performance-query-warehouse-queue) |
| Warehouse query performance | Median query duration | Seconds | For each warehouse, the median amount of time for queries to run. | This metric varies widely depending on your data and the types of queries you are running. If the median query duration shows unusual changes, the workload that this warehouse supports might have changed, or the warehouse configuration might have changed. | [Exploring execution times](performance-query-exploring) . . [Optimizing query performance](performance-query-options) |
|  | Query throughput | Queries | For each warehouse, the number of queries processed. | This metric can reveal changes in query activity, which might require modifications to the warehouses that run the queries. | [Optimizing warehouses for performance](performance-query-warehouse) |
| Warehouse events | **–** | None | A sortable table of warehouse events. | This metric shows which warehouses changed in the period. Examine the data for unexpected events. | [WAREHOUSE\_EVENTS\_HISTORY view](../sql-reference/organization-usage/warehouse_events_history) |

See moreShow less

Expand

## Monitoring top tables

This section of Performance Explorer includes metrics about the tables in your Snowflake environment that
experienced the most changes in the period. You can monitor these metrics to ensure that your tables
can support query activity and return data as expected. The metrics can also show whether any tables are
associated with trends in query activity that are unusual when compared to other tables. You can also determine
whether any tables have changed recently and how they have changed.

All metrics in this section show the metric value and the percentage of change since the last period. The percentage of
change can be positive or negative, with positive change represented by an up arrow and negative change represented
by a down arrow. For each metric, Performance Explorer shows the 10 tables with the most changes. To view metrics
for more tables, select View details > on a chart to open the side panel. If this metric has no value from the last period
for a table, — is shown instead of the percentage of change. There might be no value because the table is new or
because the event being measured is infrequent.

This section includes the following metrics:

| Metric | Tab | Unit | Description | Notes | More information |
| --- | --- | --- | --- | --- | --- |
| Table query failures/1K | **–** | Failures per 1000 | For each table, the number of queries that failed for every 1,000 queries that ran. | This metric should be low or zero. If queries are failing, review the query history and errors, and then modify your queries to resolve the issues. | [Monitor query activity with Query History](ui-snowsight-activity) . . [QUERY\_HISTORY view](../sql-reference/account-usage/query_history) |
| Table queries blocked/1K | **–** | Blocked per 1000 | For each table, the number of queries that were blocked for every 1,000 queries that ran. | This metric should be low or zero. If queries were blocked, review the query history and errors, and then modify your queries to resolve the issues. | [Resource locking](../sql-reference/transactions.html#label-txn-locking) . . [Best practices for transactions](../sql-reference/transactions.html#label-transaction-best-practices) . . [LOCK\_WAIT\_HISTORY view](../sql-reference/organization-usage/lock_wait_history) . . [Monitor query activity with Query History](ui-snowsight-activity) . . [QUERY\_HISTORY view](../sql-reference/account-usage/query_history) |
| Table read performance | Median read query duration | Seconds | For each table, the median amount of time for queries to run. | This metric varies widely depending on your data and the types of queries you are running. Queries with durations that change over time might be candidates for investigation and optimization. | [Exploring execution times](performance-query-exploring) . . [Optimizing query performance](performance-query-options) |
|  | Read query throughput | Queries | For each table, the number of queries processed. | This metric can reveal changes in query activity for tables. If there is an increase in the number of queries for a table, you might want to modify the table to optimize query performance. For example, you might enable search optimization on the table. | [Table Design Considerations](table-considerations) . . [Optimizing query performance](performance-query-options) |
| Table write performance | Median write query duration | Seconds | For each table, the median amount of time for Data Manipulation Language (DML) operations to run. | This metric varies widely depending on your data and the types of DML operations you are running. DML operations with durations that change over time might be candidates for investigation and optimization. | [Exploring execution times](performance-query-exploring) . . [Optimizing query performance](performance-query-options) |
|  | Write query throughput | Queries | For each table, the number of DML operations processed. If there is an increase in the number of DML operations for a table, you might want to modify the table to optimize performance. | This metric can reveal changes in the number of DML operations. | [Table Design Considerations](table-considerations) |
| Table change events | **–** | None | A sortable table of table events. | This metric shows which tables changed in the period. Examine the data for unexpected events. | [TABLES view](../sql-reference/organization-usage/tables) |

See moreShow less

Expand
