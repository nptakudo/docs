---
title: "Use immutability constraints"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-performance-optimize-immutability"
---

# Use immutability constraints

To tell Snowflake that certain rows won’t change in a dynamic table,
use the `IMMUTABLE WHERE` clause in a [CREATE DYNAMIC TABLE](../sql-reference/sql/create-dynamic-table) or
[ALTER DYNAMIC TABLE](../sql-reference/sql/alter-dynamic-table) statement.

Immutability makes refreshes faster by skipping rows that don’t change.
*Backfill* with immutability provides both immediate and ongoing performance benefits:

* **Initial creation**: Backfill copies historical data instantly without computation costs. This
  makes tables with years of historical data immediately available instead of requiring expensive
  initial refreshes.
* **Ongoing refreshes**: Immutability constraints protect backfilled data from reprocessing during
  future refreshes. Only the mutable region gets refreshed, keeping refresh times fast even as the
  table grows.

For conceptual background, see
[Understanding immutability constraints](dynamic-tables-immutability-constraints).

## Basic examples

### Example: Prevent recomputation when a dimension table changes

When you update a row in a dimension table, reprocess only the facts from the mutable period:

CopyExpand

```
CREATE DYNAMIC TABLE joined_data
  TARGET_LAG = '1 minute'
  WAREHOUSE = mywh
  IMMUTABLE WHERE (timestamp_col < CURRENT_TIMESTAMP() - INTERVAL '1 day')
AS
  SELECT F.primary_key primary_key, F.timestamp_col timestamp_col, D.value dim_value
  FROM fact_table F
  LEFT OUTER JOIN dimension_table D USING (primary_key);
```

Show lessSee more

Scroll to top

### Example: Retain data longer than the source table

Create a dynamic table that retains parsed data longer than the staging table,
and delete old staging data with a task:

CopyExpand

```
CREATE TABLE staging_data (raw TEXT, ts TIMESTAMP);

CREATE DYNAMIC TABLE parsed_data
  TARGET_LAG = '1 minute'
  WAREHOUSE = mywh
  IMMUTABLE WHERE (ts < CURRENT_TIMESTAMP() - INTERVAL '7 days')
AS
  SELECT
    parse_json(raw):event_id::string event_id,
    parse_json(raw):name::string name,
    parse_json(raw):region::string region,
    ts
  FROM staging_data
  WHERE region = 'US';

CREATE TASK delete_old_staging_data
  WAREHOUSE = mywh
  SCHEDULE = '24 hours'
AS
  DELETE FROM staging_data WHERE ts < CURRENT_TIMESTAMP() - INTERVAL '30 days';
```

Show lessSee more

Scroll to top

### Example: Let downstream tables use incremental refresh from a full refresh table

Some query constructs (like Python user-defined table functions) require full refresh mode.
Immutability constraints let downstream tables still use incremental refresh:

CopyExpand

```
CREATE DYNAMIC TABLE udtf_dt
  TARGET_LAG = '1 hour'
  WAREHOUSE = mywh
  REFRESH_MODE = FULL
  IMMUTABLE WHERE (ts < current_timestamp() - interval '1 day')
AS
  SELECT ts, data, output, join_key
  FROM input_table, TABLE(my_udtf(data));

CREATE DYNAMIC TABLE incremental_join_dt
  TARGET_LAG = '1 hour'
  WAREHOUSE = mywh
  REFRESH_MODE = INCREMENTAL
  IMMUTABLE WHERE (ts < current_timestamp() - interval '1 day')
AS
  SELECT * FROM udtf_dt JOIN dim_table USING (join_key);
```

Show lessSee more

Scroll to top

## Backfill examples

The following examples show how to create new dynamic tables from tables with backfilled data.

The backfill table must contain matching columns with compatible data types in the same order as your dynamic table.
Snowflake doesn’t copy table properties or privileges from the backfill table.

If you specify the Time Travel parameters `AT | BEFORE`, Snowflake copies data from the backfill table at the specified time.

The following limitations apply when you work with [immutability constraints](dynamic-tables-immutability-constraints)
and backfilled data:

* Currently, only regular and dynamic tables can be used for backfilling.
* You can’t specify policies or tags in the new dynamic table because they are copied from the backfill table.
* Clustering keys in the new dynamic table and backfill table must be the same.

### Example: Backfill from a part of the table

The following example backfills the immutable region of `my_dynamic_table` from `my_backfill_table` and the mutable region from the dynamic
table’s definition.

When you reinitialize this dynamic table:

* **Incremental refresh mode**: Snowflake deletes all mutable rows and repopulates only the mutable region.
* **Full refresh mode**: Snowflake performs a full refresh with the same effect.

CopyExpand

```
CREATE DYNAMIC TABLE my_dynamic_table (day TIMESTAMP, totalSales NUMBER)
  IMMUTABLE WHERE (day < '2025-01-01')
  BACKFILL FROM my_backfill_table
  TARGET_LAG = '20 minutes'
  WAREHOUSE = 'mywh'
  AS SELECT DATE_TRUNC('day', ts) AS day, sum(price)
    FROM my_base_table
    GROUP BY day;
```

Show lessSee more

Scroll to top

### Example: Use backfill to recover or modify data in a dynamic table

You can’t directly edit a dynamic table’s data or definition. To recover or fix data, complete the following workaround steps:

1. Clone the dynamic table to a regular table.
2. Modify the cloned table as needed.
3. Backfill from the edited table into a new dynamic table.

In the following example, `my_dynamic_table` aggregates daily sales data from the `sales` base table:

CopyExpand

```
CREATE OR REPLACE TABLE sales(item_id INT, ts TIMESTAMP, sales_price FLOAT);

INSERT INTO sales VALUES (1, '2025-05-01 01:00:00', 10.0), (1, '2025-05-01 02:00:00', 15.0), (1, '2025-05-01 03:00:00', 11.0);
INSERT INTO sales VALUES (1, '2025-05-02 00:00:00', 11.0), (1, '2025-05-02 05:00:00', 13.0);

CREATE DYNAMIC TABLE my_dynamic_table
  TARGET_LAG = 'DOWNSTREAM'
  WAREHOUSE = mywh
  INITIALIZE = on_create
  IMMUTABLE WHERE (day <= '2025-05-01')
  AS
    SELECT item_id, date_trunc('DAY', ts) day, count(sales_price) AS sales_count FROM sales
    GROUP BY item_id, day;

SELECT item_id, to_char(day, 'YYYY-MM-DD') AS day, sales_count FROM my_dynamic_table;
```

Show lessSee more

Scroll to top

Expand

```
+---------+------------+-------------+
| ITEM_ID | DAY        | SALES_COUNT |
|---------+------------+-------------|
| 1       | 2025-05-01 | 3           |
| 1       | 2025-05-02 | 2           |
+---------+------------+-------------+
```

Show lessSee more

Scroll to top

Optionally, you can archive the old data to save storage cost:

CopyExpand

```
DELETE FROM sales WHERE ts < '2025-05-02';

ALTER DYNAMIC TABLE my_dynamic_table REFRESH;

SELECT item_id, to_char(day, 'YYYY-MM-DD') AS day, sales_count FROM my_dynamic_table;
```

Show lessSee more

Scroll to top

Later, you find a sales error on `2025-05-01`, where `sales_count` should be 2. To correct this:

1. Clone `my_dynamic_table` to a regular table:

   CopyExpand

   ```
   CREATE OR REPLACE TABLE my_dt_clone_table CLONE my_dynamic_table;
   ```

   Show lessSee more

   Scroll to top
2. Update the cloned table:

   CopyExpand

   ```
   UPDATE my_dt_clone_table SET
     sales_count = 2
     WHERE day = '2025-05-01';

   SELECT item_id, to_char(day, 'YYYY-MM-DD') AS day, sales_count FROM my_dt_clone_table;
   ```

   Show lessSee more

   Scroll to top

   Expand

   ```
   +---------+------------+-------------+
   | ITEM_ID | DAY        | SALES_COUNT |
   |---------+------------+-------------|
   | 1       | 2025-05-01 | 2           |
   | 1       | 2025-05-02 | 2           |
   +---------+------------+-------------+
   ```

   Show lessSee more

   Scroll to top
3. Recreate the dynamic table by using the edited clone as the backfill source.

   CopyExpand

   ```
   CREATE OR REPLACE DYNAMIC TABLE my_dynamic_table
     BACKFILL FROM my_dt_clone_table
     IMMUTABLE WHERE (day <= '2025-05-01')
     TARGET_LAG = 'DOWNSTREAM'
     WAREHOUSE = mywh
     INITIALIZE = on_create
     AS
       SELECT item_id, date_trunc('DAY', ts) day, count(sales_price) AS sales_count FROM sales
       GROUP BY item_id, day;
   ```

   Show lessSee more

   Scroll to top

   This method lets you recover or correct data in a dynamic table without modifying the base table:

   CopyExpand

   ```
   SELECT item_id, to_char(day, 'YYYY-MM-DD') AS day, sales_count FROM my_dynamic_table;
   ```

   Show lessSee more

   Scroll to top

   Expand

   ```
   +---------+------------+-------------+
   | ITEM_ID | DAY        | SALES_COUNT |
   |---------+------------+-------------|
   | 1       | 2025-05-01 | 2           |
   | 1       | 2025-05-02 | 2           |
   +---------+------------+-------------+
   ```

   Show lessSee more

   Scroll to top

### Example: Modify a dynamic table’s schema by using backfill

You can’t directly alter the schema of a dynamic table. To update the schema — for example, add a column — follow these steps:

1. Clone the dynamic table to a regular table. The following example uses `my_dynamic_table` created from `sales`
   ([earlier](#label-data-surgery-example)).

   CopyExpand

   ```
   CREATE OR REPLACE TABLE my_dt_clone_table CLONE my_dynamic_table;
   ```

   Show lessSee more

   Scroll to top
2. Modify the schema of the cloned table:

   CopyExpand

   ```
   ALTER TABLE my_dt_clone_table ADD COLUMN sales_avg FLOAT;

   SELECT item_id, to_char(day, 'YYYY-MM-DD') as DAY, SALES_COUNT, SALES_AVG FROM my_dt_clone_table;
   ```

   Show lessSee more

   Scroll to top
3. Optionally, add data to the new column.
4. Recreate the dynamic table by using the edited clone as the backfill source.

   CopyExpand

   ```
   CREATE OR REPLACE DYNAMIC TABLE my_dynamic_table
     BACKFILL FROM my_dt_clone_table
     IMMUTABLE WHERE (day <= '2025-05-01')
     TARGET_LAG = 'DOWNSTREAM'
     WAREHOUSE = mywh
     INITIALIZE = on_create
     AS
       SELECT item_id, date_trunc('DAY', ts) day, count(sales_price) AS sales_count, avg(sales_price) as sales_avg FROM sales
       GROUP BY item_id, day;
   ```

   Show lessSee more

   Scroll to top
5. Verify that the new column appears in the dynamic table:

   CopyExpand

   ```
   SELECT item_id, to_char(day, 'YYYY-MM-DD') as DAY, SALES_COUNT, SALES_AVG, metadata$is_immutable as IMMUTABLE from my_dynamic_table ORDER BY ITEM_ID, DAY;
   ```

   Show lessSee more

   Scroll to top

   Expand

   ```
   +---------+------------+-------------+-----------+-----------+
   | ITEM_ID | DAY        | SALES_COUNT | SALES_AVG | IMMUTABLE |
   |---------+------------+-------------|-----------|-----------|
   | 1       | 2025-05-01 | 3           | NULL      | TRUE      |
   | 1       | 2025-05-02 | 2           | 12        | FALSE     |
   +---------+-------------+------------+-----------+-----------+
   ```

   Show lessSee more

   Scroll to top

## Check immutability status

To check whether a row is mutable in a dynamic table, query the `METADATA$IS_IMMUTABLE` column:

CopyExpand

```
SELECT *, METADATA$IS_IMMUTABLE FROM my_dynamic_table;
```

Show lessSee more

Scroll to top

To view the immutability constraint on a dynamic table, run [SHOW DYNAMIC TABLES](../sql-reference/sql/show-dynamic-tables) and check the
`immutable_where` column.
