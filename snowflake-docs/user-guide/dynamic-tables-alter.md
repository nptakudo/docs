---
title: "Alter existing dynamic tables"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-alter"
---

# Alter existing dynamic tables

This section describes making changes to existing dynamic tables using the [ALTER DYNAMIC TABLE](../sql-reference/sql/alter-dynamic-table) command:

* Change the warehouse or target lag of your dynamic tables
* Rename, swap, or add clustering keys to your dynamic tables

## Alter the warehouse or target lag for dynamic tables

Adjust your dynamic tables’ warehouse for cost efficiency or performance boost. For more information, see
[Compute costs](dynamic-tables-cost.html#label-dt-costs-compute) and [Understand warehouse usage for dynamic tables](dynamic-tables-warehouses).

Adjust your dynamic table’s target lag to get fresher data in the following situations:

* **You need fresher data**: Reduce target lag to trigger more frequent refreshes.
* **You want to reduce cost**: Data that doesn’t need near real-time freshness can use a
  longer target lag. For example, a dynamic table that refreshes every 20 minutes but only
  needs to be within one hour of the source tables can use a one-hour target lag to reduce
  compute costs.
* **Your pipeline has misaligned schedules**: When your dynamic table depends on other tables
  with longer refresh intervals, align the target lag with those dependencies to avoid
  unnecessary refreshes.
* **You’re seeing skipped refreshes**: When refreshes take longer than your target lag,
  Snowflake skips some refreshes. Increase the target lag to match realistic refresh durations.

For more information, see [Understanding dynamic table target lag](dynamic-tables-target-lag).

To change the warehouse or target lag for a dynamic table, use the [ALTER DYNAMIC TABLE](../sql-reference/sql/alter-dynamic-table) command. For example:

CopyExpand

```
-- Change the warehouse for my_dynamic_table to my_other_wh:
ALTER DYNAMIC TABLE my_dynamic_table SET
  WAREHOUSE = my_other_wh;
```

Show lessSee more

Scroll to top

CopyExpand

```
-- Specify the downstream target lag for a dynamic table:
ALTER DYNAMIC TABLE my_dynamic_table SET
  TARGET_LAG = DOWNSTREAM;
```

Show lessSee more

Scroll to top

## Rename dynamic tables

Renaming a dynamic table can be useful in scenarios where you have scripts or applications that rely on a specific table name, and you want to
update the dynamic table without changing your existing script. For example, if you have a script that references a specific dynamic table
name, renaming the table allows you to swap out the underlying table while keeping the script unchanged. This ensures continuity and avoids
the hassle of updating multiple references across scripts or processes.

To rename a dynamic table, use the [ALTER DYNAMIC TABLE … RENAME TO](../sql-reference/sql/alter-dynamic-table) command. For example:

CopyExpand

```
ALTER DYNAMIC TABLE my_dynamic_table RENAME TO my_new_dynamic_table;
```

Show lessSee more

Scroll to top

## Swap dynamic tables

Swapping dynamic tables allows for a seamless transition between datasets or table versions without disrupting workflows or modifying dependent
scripts. For example, if you’re developing a new version of a table but want to keep the same name for ongoing processes, swapping lets you
replace the old table with the new one. This approach ensures continuity while enabling updates, testing, or upgrades with minimal downtime or
disruption.

To swap a dynamic table, use the [ALTER DYNAMIC TABLE … SWAP WITH](../sql-reference/sql/alter-dynamic-table) command. Note that you can
only swap a dynamic table with another dynamic table.

For example:

CopyExpand

```
-- Swap my_dynamic_table with the my_new_dynamic_table:
ALTER DYNAMIC TABLE my_dynamic_table SWAP WITH my_new_dynamic_table;
```

Show lessSee more

Scroll to top

## Add clustering keys to dynamic tables

Adding clustering keys to dynamic tables can enhance performance by improving query efficiency and refresh operations:

* Query efficiency: Clustering keys can help speed up queries, just like with regular tables, by clustering on common join keys or filter
  columns.
* Refresh operations: Clustering keys can help speed up refreshes if the clustering keys align with frequent change patterns; for example,
  clustering by user ID can be effective when you have updates where a handful of users change.

Clustering keys can be specified for a dynamic table with incremental or full refresh mode. In full refresh, the clustering is performed
during the refresh and background reclustering isn’t needed.

To cluster a dynamic table, use the [ALTER DYNAMIC TABLE … CLUSTER BY](../sql-reference/sql/alter-dynamic-table.html#label-alter-dynamic-table-clusteringaction) command:

CopyExpand

```
ALTER DYNAMIC TABLE my_dynamic_table CLUSTER BY (date);
```

Show lessSee more

Scroll to top
