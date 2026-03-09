---
title: "Optimize queries for incremental refresh"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-performance-optimize-query"
---

# Optimize queries for incremental refresh

Use this page when you design a new dynamic table query or want to optimize an existing one
for incremental refresh. This guide shows which operators perform well, which need careful
handling, and how to restructure queries for better performance.

For a complete list of which query constructs are *supported* for incremental refresh, see
[Supported queries for dynamic tables](dynamic-tables-supported-queries).

## Performance expectations by operator

Before you optimize a dynamic table query, understand which operators benefit from incremental refresh and which can
cause problems.

Note

Short queries (less than 10 seconds) might see smaller performance gains because of fixed
overheads like query optimization and warehouse scheduling.

### Operators that perform consistently well

These operators work efficiently with incremental refresh:

* `SELECT`
* `WHERE`
* `FROM` <base table>
* `UNION ALL`
* `QUALIFY` [ `RANK` | `ROW_NUMBER` | `DENSE_RANK` ] … = 1

For details on how Snowflake processes each operator, see the [operator reference table](#label-dt-incremental-operators-reference).

### Operators affected by data locality

For these operators, performance depends on [data locality](dynamic-tables-performance-optimize.html#label-dt-optimize-locality), which is
how you organize your data and where changes occur relative to your keys:

* `INNER JOIN`
* `OUTER JOIN`
* `GROUP BY`
* `DISTINCT`
* `OVER` (window functions)

When changes affect only a small portion of grouping or
partition keys, these operators perform well. Poor
data locality or changes spread across many keys can
make incremental refresh *slower* than full refresh.

For details on how Snowflake processes each operator, see the [operator reference table](#label-dt-incremental-operators-reference).

## Common optimization patterns

The following sections show common patterns to optimize queries that use locality-sensitive operators.

### Optimize aggregations

When you use [GROUP BY](../sql-reference/constructs/group-by), Snowflake recomputes aggregates for every grouping key that contains
changes. Performance depends on the following factors:

* **Data clustering**: Source data clustered by grouping keys performs best.
* **Change distribution**: Aim for changes that affect fewer than five percent of grouping keys.
* **Key complexity**: Simple column references outperform compound expressions.

#### Problem: Compound expressions in grouping keys

This query performs poorly because the grouping key is an expression:

CopyExpand

```
CREATE DYNAMIC TABLE hourly_sums
  TARGET_LAG = '1 hour'
  WAREHOUSE = my_warehouse
AS
  SELECT DATE_TRUNC('minute', ts), SUM(amount)
  FROM transactions
  GROUP BY 1;
```

Show lessSee more

Scroll to top

#### Solution: Materialize the expression

Split into two dynamic tables to expose a simple grouping key:

CopyExpand

```
CREATE DYNAMIC TABLE transactions_with_minute
  TARGET_LAG = DOWNSTREAM
  WAREHOUSE = my_warehouse
AS
  SELECT DATE_TRUNC('minute', ts) AS ts_minute, amount
  FROM transactions;

CREATE DYNAMIC TABLE hourly_sums
  TARGET_LAG = '1 hour'
  WAREHOUSE = my_warehouse
AS
  SELECT ts_minute, SUM(amount)
  FROM transactions_with_minute
  GROUP BY 1;
```

Show lessSee more

Scroll to top

Now `GROUP BY` operates on a simple column, and the intermediate table benefits from
better [data locality](dynamic-tables-performance-optimize.html#label-dt-optimize-locality).

### Optimize joins

Join performance depends on which side changes and how you cluster data.

**INNER JOIN**: Snowflake joins changes from the left side with the right table, then joins
changes from the right side with the left table. Joins perform well when one side is small
or changes infrequently.

**OUTER JOIN**: Snowflake must also compute NULL values for non-matching rows. Which side
changes significantly affects performance.

#### Problem: Large table on both sides with poor clustering

Neither source table is clustered by join key:

CopyExpand

```
CREATE DYNAMIC TABLE order_details
  TARGET_LAG = '1 hour'
  WAREHOUSE = my_warehouse
AS
  SELECT o.order_id, o.customer_id, p.product_name, o.quantity
  FROM orders o
  JOIN products p ON o.product_id = p.product_id;
```

Show lessSee more

Scroll to top

#### Solution: Cluster the table that changes less often

Cluster the dimension table by the join key. Then, the join benefits from better locality:

CopyExpand

```
ALTER TABLE products CLUSTER BY (product_id);

CREATE DYNAMIC TABLE order_details
  TARGET_LAG = '1 hour'
  WAREHOUSE = my_warehouse
AS
  SELECT o.order_id, o.customer_id, p.product_name, o.quantity
  FROM orders o
  JOIN products p ON o.product_id = p.product_id;
```

Show lessSee more

Scroll to top

For OUTER JOINs:

* Put the table that changes more often on the LEFT side.
* Minimize changes on the side opposite the OUTER keyword.
* For FULL OUTER JOINs, good locality is critical on both sides.

### Optimize window functions

Snowflake recomputes [window functions](../sql-reference/functions-window) for every partition key that contains changes. Optimize
them similarly to `GROUP BY`.

Key requirements:

* Always include a PARTITION BY clause. Window functions without PARTITION BY result in a full
  recomputation.
* Cluster source data by partition keys.
* Keep changes to fewer than five percent of partitions.

#### Problem: Window function without partition clustering

The source table isn’t clustered by the partition key:

CopyExpand

```
CREATE DYNAMIC TABLE ranked_sales
  TARGET_LAG = '1 hour'
  WAREHOUSE = my_warehouse
AS
  SELECT
    region,
    salesperson,
    amount,
    RANK() OVER (PARTITION BY region ORDER BY amount DESC) as sales_rank
  FROM daily_sales;
```

Show lessSee more

Scroll to top

#### Solution: Cluster by the partition key

Cluster the source table by the partition key so that the window function benefits from locality:

CopyExpand

```
ALTER TABLE daily_sales CLUSTER BY (region);

CREATE DYNAMIC TABLE ranked_sales
  TARGET_LAG = '1 hour'
  WAREHOUSE = my_warehouse
AS
  SELECT
    region,
    salesperson,
    amount,
    RANK() OVER (PARTITION BY region ORDER BY amount DESC) as sales_rank
  FROM daily_sales;
```

Show lessSee more

Scroll to top

### Remove duplicates efficiently (DISTINCT vs QUALIFY)

Both [DISTINCT](../sql-reference/sql/select) and [QUALIFY](../sql-reference/constructs/qualify) can remove duplicates,
but they perform differently.

**DISTINCT**: Equivalent to `GROUP BY ALL`. Locality directly affects performance; poor
locality causes slow refreshes.

**QUALIFY with ROW\_NUMBER = 1**: Snowflake optimizes the pattern `QUALIFY ROW_NUMBER() ... = 1`
when it’s in the top-level projection of the dynamic table. This pattern consistently performs
faster than full refresh.

The optimization works best when all PARTITION BY and ORDER BY keys in the OVER() clause are
persisted in the dynamic table (included in the top-level projection).

#### Recommendation: Use QUALIFY instead of DISTINCT when possible

The following example uses DISTINCT:

CopyExpand

```
CREATE DYNAMIC TABLE unique_customers
  TARGET_LAG = '1 hour'
  WAREHOUSE = my_warehouse
AS
  SELECT DISTINCT customer_id, customer_name, email
  FROM customer_events;
```

Show lessSee more

Scroll to top

The following example uses QUALIFY:

CopyExpand

```
CREATE DYNAMIC TABLE unique_customers
  TARGET_LAG = '1 hour'
  WAREHOUSE = my_warehouse
AS
  SELECT customer_id, customer_name, email, event_time
  FROM customer_events
  QUALIFY ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY event_time DESC) = 1;
```

Show lessSee more

Scroll to top

The QUALIFY version is more explicit about which duplicate to keep (the most recent) and
performs consistently well.

#### Remove redundant DISTINCT operations

Each DISTINCT consumes resources on every refresh. When your data is already unique or you
eliminate duplicates upstream, remove unnecessary DISTINCT clauses.

## Operator reference

The following table explains how Snowflake processes each SQL operator during incremental
refresh:

| Operator | How Snowflake processes it | Performance notes |
| --- | --- | --- |
| SELECT | Applies expressions to changed rows only. | Performs well. No special considerations. |
| WHERE | Evaluates the predicate on changed rows only. | Performs well. Cost scales linearly with changes. Note: A highly selective WHERE might require warehouse uptime even when the output doesn’t change. |
| FROM <table> | Scans micro-partitions that Snowflake added or removed since the last refresh. | Cost scales with the volume of changed partitions. Limit changes to about five percent of the source table. |
| UNION ALL | Takes the union of changes from each side. | Performs well. No special considerations. |
| WITH (CTEs) | Computes changes for each Common Table Expression. | Performs well, but avoid overly complex single-table definitions. Consider splitting into multiple dynamic tables. |
| Scalar aggregates | Fully recomputes the aggregate when input changes. | Avoid in performance-critical tables. Consider grouping by a constant instead. |
| GROUP BY | Recomputes aggregates for changed grouping keys. | Cluster source by grouping keys. Avoid compound expressions in keys. See [Optimize aggregations](#label-dt-optimize-aggregations). |
| DISTINCT | Equivalent to GROUP BY ALL. | Locality-sensitive. Consider using QUALIFY instead. See [Remove duplicates efficiently (DISTINCT vs QUALIFY)](#label-dt-optimize-deduplication). |
| Window functions | Recomputes for changed partition keys. | Always include PARTITION BY. Cluster source by partition keys. See [Optimize window functions](#label-dt-optimize-windows). |
| INNER JOIN | Joins changes from each side with the other table. | Performs well when one side is small. Cluster the less-frequently-changing side. See [Optimize joins](#label-dt-optimize-joins). |
| OUTER JOIN | Combines inner join with NOT EXISTS queries for NULL computation. | Most locality-sensitive operator. See [Optimize joins](#label-dt-optimize-joins). |
| LATERAL FLATTEN | Applies flatten to changed rows only. | Performs well. Cost scales linearly with changes. |
| QUALIFY with ranking | Uses an optimized path for ROW\_NUMBER/RANK/DENSE\_RANK … = 1. | Highly efficient. Place QUALIFY at the top-level projection of the dynamic table. |

See moreShow less

Expand
