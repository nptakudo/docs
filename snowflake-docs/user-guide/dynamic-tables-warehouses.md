---
title: "Understand warehouse usage for dynamic tables"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-warehouses"
---

# Understand warehouse usage for dynamic tables

Every dynamic table requires a warehouse to run its refreshes. You specify this warehouse when you
create the dynamic table, and Snowflake uses it automatically for all scheduled refreshes.

For guidance on configuring warehouses for your dynamic tables, see
[Adjust your warehouse configuration](dynamic-tables-performance-optimize.html#label-dt-optimize-warehouse).

## How warehouse size affects refresh performance

A larger warehouse doesn’t always result in higher costs. In many cases, doubling the warehouse size
doubles the per-second cost but halves the runtime. This results in similar total cost with faster
refreshes. Larger warehouses improve performance in two ways:

* **Memory**: When a refresh needs more memory than the warehouse provides, data spills to local storage.
  This spillage increases the total compute work and slows the refresh process. A larger warehouse has more memory and can
  avoid spills entirely.
* **Parallelism**: Larger warehouses run more tasks simultaneously. Refreshes that scan large amounts
  of data across many partitions benefit the most. Small data sets and sequential operations see
  diminishing returns when you use a larger warehouse.

For more information about warehouse sizing, see [Warehouse size](warehouses-overview.html#label-warehouse-size).

## Dual warehouse support

Dynamic tables support separate warehouses for different refresh types:

* **WAREHOUSE**: Runs regular incremental refreshes.
* **INITIALIZATION\_WAREHOUSE**: Runs [initializations and full refreshes](dynamic-tables-refresh.html#label-dynamic-tables-initialization), which perform full data scans and are typically more
  resource-intensive.

  Important

  When you [refresh manually](dynamic-tables-manual-refresh) by running
  [ALTER DYNAMIC TABLE REFRESH COPY SESSION](../sql-reference/sql/alter-dynamic-table.html#label-alter-dynamic-table-refresh), the
  command uses the current session’s warehouse. Snowflake ignores the INITIALIZATION\_WAREHOUSE in this scenarios,
  even for initializations.

This separation lets you use a larger warehouse for resource-intensive initializations without
paying for that capacity during regular incremental refreshes. Dual warehouse support is useful in the following common scenarios:

* You want to enable faster recovery when you promote a secondary dynamic table to primary and must reinitialize the table.
* You must meet strict RTO/RPO requirements, but don’t want to increase costs for day-to-day operations.

When you don’t set the INITIALIZATION\_WAREHOUSE parameter, Snowflake runs all refreshes on the warehouse specified by WAREHOUSE.
