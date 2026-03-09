---
title: "Dynamic tables"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-about"
---

# Dynamic tables

Dynamic tables are tables that automatically refresh based on a defined query and target freshness, simplifying data transformation and
pipeline management without requiring manual updates or custom scheduling.

When you create a dynamic table, you define a query that specifies how data should be transformed from base objects. Snowflake handles the
refresh schedule of the dynamic table and updates the table automatically to reflect the changes made to the base objects based on the query.

## Key considerations and general best practices

**Immutability constraints**: Use immutability constraints to let you control dynamic table updates. The constraints keep specific rows static
while enabling incremental updates to the rest of the table. They prevent unwanted changes to marked data while they let normal refreshes occur
for other parts of the table. For more information, see [Understanding immutability constraints](dynamic-tables-immutability-constraints).

**Performance considerations:** Dynamic tables use incremental processing for workloads that support it,
which can improve performance by processing only changed data instead of recomputing entire tables.
Performance depends on your query patterns and data organization. For guidance on optimizing dynamic table
performance, see [Dynamic table performance and optimization](dynamic-tables-performance).

**Break down complex dynamic tables:** Break your pipeline into smaller, focused dynamic tables to improve performance and simplify
troubleshooting. For more information, see [Best practices for creating dynamic tables](dynamic-tables-create.html#label-best-practices-create).

## How dynamic tables work

Snowflake runs the definition query specified in your CREATE DYNAMIC TABLE statement and your dynamic tables are updated through an automated
refresh process.

The following diagram shows how this process computes the changes made to the base objects and merges them into the dynamic table by using
compute resources associated with the table.

![Visual representation of automated refresh process between base objects and dynamic tables](../_images/dynamic-tables.png)

### Target lag

Use *target lag* to set how fresh you want your data to be. Usually, the table data freshness won’t be more than that far behind the base table
data freshness. With target lag, you control how often the table refreshes and how up-to-date the data stays. Target lag affects
refresh frequency and compute costs.

For more information, see [Understanding dynamic table target lag](dynamic-tables-target-lag). For guidance on balancing data freshness with
performance, see [Optimize dynamic table performance](dynamic-tables-performance-optimize).

### Dynamic table refresh

Dynamic tables aim to refresh within the target lag you specify. For example, a target lag of five minutes ensures that the data in the dynamic
table is no more than five minutes behind data updates to the base table. You set the refresh mode when you create the table and, afterward,
refreshes can happen on a schedule or manually.

For more information, see [Understanding dynamic table initialization and refresh](dynamic-tables-refresh) and [Manually refresh dynamic tables](dynamic-tables-manual-refresh).

## When to use dynamic tables

Dynamic tables are ideal for the following scenarios:

* You want to materialize query results without writing custom code.
* You want to avoid manually tracking data dependencies and managing refresh schedules. Dynamic tables enable you to define pipeline outcomes
  declaratively, without managing transformation steps manually.
* You want to chain together multiple tables for data transformations in a pipeline.
* You don’t need fine-grained control over refresh schedules, and you only need to specify a target freshness for the pipeline. Snowflake
  handles the orchestration of data refreshes, including scheduling and execution, based on your target freshness requirements.

### Example use cases

* **Slowly changing dimensions (SCDs):** Dynamic tables can be used to implement Type 1 and Type 2 SCDs by reading from a change stream and
  using window functions over per-record keys ordered by a change timestamp. This method handles insertions, deletions, and updates that occur
  out of order, simplifying the creation of SCDs. For more information, see
  [Slowly Changing Dimensions with Dynamic Tables](https://medium.com/snowflake/slowly-changing-dimensions-with-dynamic-tables-d0d76582ff31).
* **Joins and aggregations:** To enable fast queries, you can use dynamic tables to incrementally precompute slow joins and aggregations.
  For guidance on optimizing these operators for incremental refresh, see
  [Optimize queries for incremental refresh](dynamic-tables-performance-optimize-query).
* **Batch to streaming transitions**: Dynamic tables support seamless transitions from batch to streaming with a single ALTER DYNAMIC TABLE
  command. You can control the refresh frequency in your pipeline to balance cost and data freshness.
