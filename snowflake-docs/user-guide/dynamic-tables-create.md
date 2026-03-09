---
title: "Create dynamic tables"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-create"
---

# Create dynamic tables

This topic outlines the key concepts for creating dynamic tables.

Before you begin, ensure you have the [privileges for creating dynamic tables](dynamic-tables-privileges.html#label-dynamic-tables-privileges), and all objects used
by the dynamic table query have [change tracking](#label-dynamic-tables-and-change-tracking) enabled.

Some limitations apply to creating dynamic tables. For a complete list, see [Dynamic table limitations](dynamic-tables-limitations).

Note

For guidance on writing queries that work efficiently with incremental refresh, see
[Optimize queries for incremental refresh](dynamic-tables-performance-optimize-query).

## Enable change tracking

When creating a dynamic table with incremental refresh mode, if change tracking is not already enabled on the tables that it queries, Snowflake
automatically attempts to enable change tracking on them. In order to support incremental refreshes, change tracking must be enabled with
[non-zero time travel retention](../sql-reference/parameters.html#label-data-retention-time-in-days) on all underlying objects used by a dynamic table.

As base objects change, so does the dynamic table. If you recreate a base object, you must re-enable change tracking.

Note

Snowflake doesn’t automatically attempt to enable change tracking on dynamic tables created with full refresh mode.

To enable change tracking on a specific database object, use [ALTER TABLE](../sql-reference/sql/alter-table), [ALTER VIEW](../sql-reference/sql/alter-view), and
similar commands on that object. The user creating the dynamic table must have the OWNERSHIP privilege to enable change tracking on all
underlying objects.

To check if change tracking is enabled, use [SHOW VIEWS](../sql-reference/sql/show-views), [SHOW TABLES](../sql-reference/sql/show-tables), and similar commands
on the underlying objects, and inspect the `change_tracking` column.

## Supported base objects

Dynamic tables support the following base objects:

* Tables
* Snowflake-managed Apache Iceberg™ tables
* Externally managed Apache Iceberg™ tables

## Example: Create a simple dynamic table

Suppose that you want to create a dynamic table that contains the `product_id` and `product_name` columns from a table named
`staging_table`, and you decide:

* You want the data in your dynamic table to be at most 20 minutes behind the data in `staging_table`.
* You want to use the warehouse `mywh` for the compute resources needed for the [refresh](dynamic-tables-refresh.html#label-dynamic-tables-intro-refresh-modes).
* You want the refresh mode to be automatically chosen.

  + Snowflake recommends using the automatic refresh mode only during development. For more information,
    see [Choose a refresh mode](dynamic-tables-performance-optimize.html#label-dt-optimize-refresh).
* You want the dynamic table to refresh synchronously at creation.
* You want the refresh mode to be automatically chosen, and you want the dynamic table to refresh synchronously at creation.

To create this dynamic table, you would execute the following [CREATE DYNAMIC TABLE](../sql-reference/sql/create-dynamic-table) SQL
statement:

CopyExpand

```
CREATE OR REPLACE DYNAMIC TABLE my_dynamic_table
  TARGET_LAG = '20 minutes'
  WAREHOUSE = mywh
  REFRESH_MODE = auto
  INITIALIZE = on_create
  AS
    SELECT product_id, product_name FROM staging_table;
```

Show lessSee more

Scroll to top

For a complete list of parameters and variant syntax, see the [CREATE DYNAMIC TABLE](../sql-reference/sql/create-dynamic-table) reference.

## Create dynamic tables that read from Snowflake-managed or externally managed Apache Iceberg™ tables

Creating a dynamic table from an Iceberg table is similar to creating one from a regular table. Execute the [CREATE DYNAMIC TABLE](../sql-reference/sql/create-dynamic-table)
SQL statement as you would for a regular table, using either a Snowflake-managed table or a table managed by an external catalog as the base
object.

Dynamic tables that read from a Snowflake-managed Iceberg table as the base table are useful if you want your pipelines to operate on data in
a Snowflake-managed Iceberg table or if you want your pipelines to operate on Iceberg tables written by other engines. Note that external
engines cannot write to Snowflake-managed Iceberg tables; they are read-write for Snowflake and read-only for external engines.

Dynamic tables that read from Iceberg tables managed by [external (non-Snowflake) catalogs](tables-iceberg.html#label-tables-iceberg-catalog-integration),
such as AWS Glue and written by engines like Apache Spark, are useful for processing data from external data lakes. You can create dynamic
tables on top of externally managed data, continuously processing it in Snowflake without duplicating or ingesting the data.

### Limitations and considerations for using Iceberg tables

All limitations for [regular dynamic tables](dynamic-tables-limitations) and
[dynamic Iceberg tables](dynamic-tables-create-iceberg.html#label-dynamic-tables-tasks-create-iceberg-limitations) still apply.

Additionally:

* All limitations for Iceberg base tables apply. For more information, see [Considerations and limitations](tables-iceberg.html#label-tables-iceberg-considerations).
* You can create a dynamic table that reads from Snowflake native tables, Snowflake-managed Iceberg tables, and externally managed Iceberg
  tables.
* Dynamic tables track changes at the file level for externally managed Iceberg base tables, unlike other base tables that track changes at
  the row level. Frequent copy-on-write operations (for example, updates or deletes) on externally managed Iceberg tables may impact the
  performance of incremental refreshes.

## Create dynamic tables with immutability and backfill

[Immutability constraints](dynamic-tables-immutability-constraints) let you mark portions of a dynamic table as static.
When you define an `IMMUTABLE WHERE` clause, Snowflake skips those rows during refresh,
which improves performance for tables with large amounts of historical data.

Backfill extends immutability constraints by letting you copy existing data into a dynamic table without computing it.
This operation makes historical data immediately available while you define a custom refresh query for future updates.

For more information and examples, see [Use immutability constraints](dynamic-tables-performance-optimize-immutability).

## Best practices for creating dynamic tables

### Chain together pipelines of dynamic tables

When defining a new dynamic table, rather than defining a large dynamic table with many nested statements, use small dynamic tables with
pipelines instead.

You can set up a dynamic table to query other dynamic tables. For instance, imagine a scenario where your data pipeline extracts data from a
staging table to update various dimension tables (e.g., `customer`, `product`, `date` and `time`). Additionally, your
pipeline updates an aggregate `sales` table based on the information from these dimension tables. By configuring the dimension tables to
query the staging table and the aggregate `sales` table to query the dimension tables, you create a cascade effect similar to a task
graph.

In this setup, the refresh for the aggregate `sales` table executes only after the refreshes for the dimension tables have successfully
completed. This ensures data consistency and meets lag targets. Through an automated refresh process, any changes in the source tables trigger
refreshes in all dependent tables at the appropriate times.

![Comparison between task graphs and dynamic tables DAGs](../_images/dynamic-tables-dag.png)

### Use a “controller” dynamic table for complex task graphs

When you have a complex graph of dynamic tables with many roots and leaves and you want to perform operations (e.g. changing lag, manual
refresh, suspension) on the full task graph with a single command, do the following:

1. Set the value for the `TARGET_LAG` of all of your dynamic tables to `DOWNSTREAM`.
2. Create a “controller” dynamic table that reads from all of the leaves in your task graph.

   * A leaf dynamic table is a node in your task graph with no downstream dependencies. No other dynamic tables read from it, so it is not a
     dependency of any other dynamic table.
   * Replace `<leaf1>`, `<leaf2>`, …, `<leafN>` with actual leaf dynamic table names.
   * To ensure this controller doesn’t consume resources, create a cartesian join with `LIMIT 0`.
   > CopyExpand
   >
   > ```
   > CREATE DYNAMIC TABLE controller
   >   TARGET_LAG = <target_lag>
   >   WAREHOUSE = <warehouse>
   >   AS
   >     SELECT 1 A FROM <leaf1>, …, <leafN> LIMIT 0;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
3. Use the controller to control the whole graph. For example:

> * Set a new target lag for the task graph.
>
>   CopyExpand
>
>   ```
>   ALTER DYNAMIC TABLE controller SET
>     TARGET_LAG = <new_target_lag>;
>   ```
>
>   Show lessSee more
>
>   Scroll to top
> * Manually refresh the task graph.
>
>   CopyExpand
>
>   ```
>   ALTER DYNAMIC TABLE controller REFRESH;
>   ```
>
>   Show lessSee more
>
>   Scroll to top

### Use transient dynamic tables to reduce storage cost

[Transient](tables-temp-transient) dynamic tables maintain data reliably over time and support Time Travel within the data
retention period, but don’t retain data beyond the fail-safe period. By default, dynamic table data is retained for seven days in
[fail-safe](data-failsafe) storage.

For dynamic tables with high refresh throughput, this can significantly increase storage consumption. Therefore, you should make a dynamic
table transient only if its data doesn’t need the same level of data protection and recovery provided by permanent tables.

You can create a transient dynamic table or clone existing dynamic tables to transient dynamic tables using
the [CREATE DYNAMIC TABLE](../sql-reference/sql/create-dynamic-table) statement.

## Troubleshoot dynamic table creation

When you create a dynamic table, the initial refresh happens either on a schedule (`ON_SCHEDULE`) or immediately at creation
(`ON_CREATE`). The initial data population, or [initialization](dynamic-tables-refresh.html#label-dynamic-tables-initialization), depends on when this initial
refresh occurs. For example, for `ON_CREATE`, initialization might take longer if it triggers refreshes of upstream dynamic tables.

Initialization can take some time, depending on how much data is scanned. To view progress, do the following:

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Monitoring » Query History.
3. In the Filters dropdown, enter CREATE DYNAMIC TABLE in the SQL Text filter and enter your warehouse name in the Warehouse
   filter.
4. Select the query with your dynamic table under SQL text and use the Query Details and Query Profile tabs to track progress.
