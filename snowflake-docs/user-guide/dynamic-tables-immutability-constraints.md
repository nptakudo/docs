---
title: "Understanding immutability constraints"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-immutability-constraints"
---

# Understanding immutability constraints

Immutability constraints let you mark portions of a dynamic table as static. When you define
an immutability constraint, Snowflake skips those rows during refresh, which improves
performance, especially for tables that contain large amounts of historical data.

You define immutability constraints with the `IMMUTABLE WHERE` clause when you create or
alter a dynamic table. The clause specifies a condition, or predicate, that identifies which rows are
immutable.

Key behaviors:

* **Initial refresh**: Snowflake ignores the IMMUTABLE WHERE predicate during the initial refresh
  but applies to all subsequent refreshes.
* **Full refresh mode**: The predicate limits recomputation to only the rows that don’t match
  the condition.
* **Incremental refresh**: Streams and incremental refresh dynamic tables can read from full
  refresh dynamic tables that have immutability constraints.
* **Cloning and replication**: Snowflake copies IMMUTABLE WHERE constraints with no limitations.

For information about compute costs, see [Compute cost for immutability constraints](dynamic-tables-cost.html#label-dt-costs-immutability-constraints).

## When to use immutability constraints

Immutability constraints are useful in the following scenarios:

**Avoid reprocessing historical data**
:   When your dynamic table contains historical data that you don’t want to reprocess, mark older
    rows as immutable.

**Optimize full refresh mode**
:   Dynamic tables that use full [refresh mode](dynamic-tables-refresh.html#label-dynamic-tables-intro-refresh-modes)
    normally recompute all rows during each refresh. Immutability constraints limit recomputation
    to only the mutable rows, significantly reducing work when most data is historical.

**Facilitate incremental downstream refreshes**
:   Some query constructs, such as Python user-defined table functions, require a dynamic table to
    use full refresh mode. Normally, this prevents downstream tables from benefiting from
    incremental refresh. When the upstream table has immutability constraints, downstream
    tables can still benefit from incremental processing.

## Use backfill with immutability

Backfill extends immutability constraints. Backfill is a zero-copy operation that lets you instantly
copy existing data into a dynamic table
without recomputing it. Use it to migrate existing pipelines, change dynamic table definitions,
or avoid expensive initialization when creating tables
with years of historical data.

Backfilled data can’t change during future refreshes.

When you create a dynamic table with both `IMMUTABLE WHERE` and `BACKFILL FROM`:

* Backfill copies the **immutable region** from the source table. The immutable region consists of rows that match the
  `IMMUTABLE WHERE` condition.
* The query definition computes the **mutable region**. The mutable region consists of rows that don’t match the condition.

## Next steps

For implementation guidance and examples, see [Backfill examples](dynamic-tables-performance-optimize-immutability.html#label-create-dt-using-backfill) and [Immutability constraints](dynamic-tables-limitations.html#label-dts-immutability-constraints-limitations).
