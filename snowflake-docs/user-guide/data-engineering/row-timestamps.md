---
title: "Use row timestamps to measure latency in your pipelines"
url: "https://docs.snowflake.com/en/user-guide/data-engineering/row-timestamps"
---

# Use row timestamps to measure latency in your pipelines

Row timestamps provide a precise, chronological record of when each row in a table was last updated. Rows modified in the
same transaction share the exact same timestamp and rows modified in different transactions are ordered by when they were
committed.

Key use cases include the following:

* **Pipeline observability:** Measure end-to-end latency and data freshness for streaming ingest, CDC, and ETL workloads
  with higher accuracy than client-side timestamps.
* **Reliable incremental processing:** Capture delayed or backfilled records that event timestamps might skip by using
  definitive commit times.
* **Definitive audit trails:** Establish a chronological order of events for regulatory compliance or SCD2-style
  milestoning.

To set row timestamps on your tables, choose one of the following options:

* **Set row timestamps on a table:** Using a role that has the OWNERSHIP privilege on the table, set the ROW\_TIMESTAMP property to TRUE when
  executing the [CREATE TABLE](../../sql-reference/sql/create-table) or [ALTER TABLE](../../sql-reference/sql/alter-table) command.

  For example, `CREATE TABLE … ROW_TIMESTAMP = TRUE` or `ALTER TABLE … SET ROW_TIMESTAMP = TRUE`.
* **Set row timestamps by default for new tables in a container:** Set the ROW\_TIMESTAMP\_DEFAULT property to TRUE on the container.

  For example, `ALTER SCHEMA … SET ROW_TIMESTAMP_DEFAULT = TRUE` means that every new table created in the schema after setting the
  parameter will have row timestamps on by default.
* **Bulk enable row timestamps on existing tables:** Use the system function SELECT SYSTEM$SET\_ROW\_TIMESTAMP\_ON\_ALL\_SUPPORTED\_TABLES.

  For example, `SELECT SYSTEM$SET_ROW_TIMESTAMP_ON_ALL_SUPPORTED_TABLES('schema', '{my_db}.my_schema')`.

  + The first argument is level: one of `schema`, `database`, or `account`.
  + The second argument is the fully qualified name of the container.

  This function adds the row timestamp column to all existing eligible tables within the container and ensures newly created tables automatically
  have row timestamp enabled.

  To successfully execute the function, you need MODIFY privileges on the container you’re invoking the function on.

After row timestamps are enabled, tables expose the METADATA$ROW\_LAST\_COMMIT\_TIME column, which returns the timestamp when each row was last
modified. This enables change tracking, incremental processing, and time-travel queries based on row modification time.

Note

In a data sharing scenario, consumers can’t select METADATA$ROW\_LAST\_COMMIT\_TIME even if the producer table has row timestamp enabled. Producers
must create a view that selects METADATA$ROW\_LAST\_COMMIT\_TIME and then share the view if they want to share row timestamps with consumers.

The following statements demonstrate how to create a table that supports row timestamps. The statements insert data into the table and retrieve
the timestamp of each row.

CopyExpand

```
CREATE OR REPLACE TABLE table1(value1 STRING)
  ROW_TIMESTAMP = TRUE;

INSERT INTO table1 VALUES('some-value-a');

INSERT INTO table1 VALUES('some-value-b');

SELECT METADATA$ROW_LAST_COMMIT_TIME AS row_timestamp, *
  FROM table1
  ORDER BY 1;
```

Show lessSee more

Scroll to top

## Primary use cases

The METADATA$ROW\_LAST\_COMMIT\_TIME metadata column helps track latency. For example, if you aim for a five-second total latency, this column
helps you determine Snowflake’s contribution to that latency.

Key use cases include:

* **Measuring ingestion latency**: Track the time between when a row is created on the client and when it becomes visible in Snowflake,
  allowing users to calculate data ingestion time.
* **Measuring end-to-end latency**: Combine ingestion latency and pipeline latency to measure the total time from data generation to its
  final state.
* **Measuring pipeline latency**: Tracks timestamps as data moves through a pipeline. By comparing the timestamp of the initial table to the
  final table, users can measure how long the pipeline takes to process data.

  + Supported for pipelines based on streams, dynamic tables, and tasks.

### Example: measure ingestion latency

To measure ingestion latency using the METADATA$ROW\_LAST\_COMMIT\_TIME metadata column, do the following:

1. Create an ingestion pipeline that sends data to Snowflake using one of the following methods:

   * [Snowpipe Streaming Ingest SDK](../snowpipe-streaming/data-load-snowpipe-streaming-overview). For a simple example that shows how the client
     SDK could be used to build a Snowpipe Streaming application, see
     [this Java file](https://github.com/snowflakedb/snowflake-ingest-java/blob/master/src/main/java/net/snowflake/ingest/streaming/example/SnowflakeStreamingIngestExample.java) (GitHub).
   * [Snowpipe](../data-load-snowpipe-intro)
   * [COPY INTO <table>](../../sql-reference/sql/copy-into-table) command
2. Execute the following:

   CopyExpand

   ```
   ALTER SESSION SET TIMESTAMP_TZ_OUTPUT_FORMAT = 'YYYY-MM-DDTHH:MI:SS.FF3 TZH';

   ALTER SESSION SET TIMEZONE = 'UTC';

   CREATE OR REPLACE DATABASE mydb;

   CREATE OR ALTER SCHEMA myschema;

   CREATE OR REPLACE TABLE table1(record_id STRING, client_timestamp TIMESTAMP_LTZ);

   -- The rows inserted from server-side-insert-1 up to this point will not have a valid METADATA$ROW_LAST_COMMIT_TIME timestamp.
   INSERT INTO table1 VALUES('server-side-insert-1', current_timestamp());
   ```

   Show lessSee more

   Scroll to top
3. Modify the table to enable the METADATA$ROW\_LAST\_COMMIT\_TIME feature.

   CopyExpand

   ```
   ALTER TABLE table1 SET ROW_TIMESTAMP = TRUE;
   ```

   Show lessSee more

   Scroll to top
4. Ingest data that includes the `record_id` and `client_timestamp` columns to your Snowflake table using the ingestion pipeline defined in Step 1.
5. Insert a new row as an immediate example if not using an ingestion pipeline. Unlike the insert in Step 2, this insert will have a valid METADATA$ROW\_LAST\_COMMIT\_TIME timestamp because the table property is enabled.

   CopyExpand

   ```
   INSERT INTO table1 VALUES('server-side-insert-2', current_timestamp());
   ```

   Show lessSee more

   Scroll to top
6. Run your client-side program again, and then do the following:

   CopyExpand

   ```
   SELECT *, METADATA$ROW_LAST_COMMIT_TIME AS ROW_TIMESTAMP, TIMESTAMPDIFF(ms, CLIENT_TIMESTAMP, ROW_TIMESTAMP)
     AS INGEST_LATENCY FROM table1 ORDER BY 2;
   ```

   Show lessSee more

   Scroll to top

## Secondary use cases

Row timestamps can also be used in the following cases:

* **Data retention**: Row timestamps can help delete old records to save on storage costs.
* **Event ordering and change tracking**: You can use row timestamps to track changes. The row with the largest timestamp represents the
  latest change.
* **Append-only data**: If rows are only appended, row timestamps can help filter for table states from specific points in time, enabling
  you to use [Time Travel](../data-time-travel) regardless of data retention policy.

## Limitations and considerations

* Row timestamps are only guaranteed to maintain chronological order within the same table, except in the event of failover where ordering
  isn’t guaranteed. Ordering across tables, different regions, or other time sources isn’t guaranteed. You shouldn’t compare row timestamps
  across tables or other sources because doing so can lead to inconsistencies.
* Row timestamps reflect the last updated time, not the creation time. For instance, if the data row is updated after it has been committed,
  the row timestamp reflects the last updated time, not the creation time of the data.
* Timestamps on rows created before the row timestamps were enabled for a table are set to NULL.
* Row timestamps are stored as long as the rows are stored.
* Setting the ROW\_TIMESTAMP property to FALSE permanently deletes all stored METADATA$ROW\_LAST\_COMMIT\_TIME values. Re-enabling
  it will not restore them and Time Travel queries will return nothing.
* Row timestamps are not supported for Apache Iceberg™ tables, external tables, hybrid tables, streams, or views.
* The metadata column METADATA$ROW\_LAST\_COMMIT\_TIME can’t be referenced in the following:

  + [CHANGES](../../sql-reference/constructs/changes) clause
  + Policies, including row or column access policies and [storage lifecycle policies](../storage-management/storage-lifecycle-policies)
  + Constraints
  + CLUSTER BY expressions
* Row timestamps can’t be restored by archive table restore. As a workaround, you can materialize METADATA$ROW\_LAST\_COMMIT\_TIME as a persisted
  column of another table to use in archive restore.

### Cloning considerations for row timestamps

Cloning a table preserves row timestamps exactly. Operations that create a physical copy of data, such as CREATE TABLE AS SELECT (CTAS) and
INSERT INTO … SELECT, assign fresh row timestamps reflecting when the copy was made. The original row timestamps from the source table aren’t
preserved. If you would like to keep a record of them, then select them explicitly into a persisted column, as shown in the
following example:

CopyExpand

```
CREATE TABLE my_archive AS
 SELECT *, METADATA$ROW_LAST_COMMIT_TIME AS original_commit_time
 FROM my_source_table;
```

Show lessSee more

Scroll to top
