---
title: "Dynamic tables compared to streams and tasks, and materialized views"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-comparison"
---

# Dynamic tables compared to streams and tasks, and materialized views

Like streams and tasks, dynamic tables provide a way to transform data in your pipeline.

## Dynamic tables compared to streams and tasks

Although dynamic tables serve a similar purpose compared to streams and tasks, there are important differences.

### Create a stream on a dynamic table

You can use a dynamic table as the source of a stream, like streams on regular tables, with the following limitations:

* Refresh mode: Streams can be created only on dynamic tables that refresh [incrementally](dynamic-tables-refresh.html#label-dynamic-tables-intro-refresh-modes).
  Full refresh dynamic tables aren’t supported because they completely rewrite the table on every refresh.
* Stream type: Dynamic tables support only standard (that is, delta) streams. For more information, see
  [Types of streams](streams-intro.html#label-stream-and-tasks-types-of-streams).

The following example shows how to create a stream on a dynamic table:

CopyExpand

```
-- Create the dynamic table, for reference only
CREATE OR REPLACE DYNAMIC TABLE product ...;

-- Create the stream.
CREATE OR REPLACE STREAM deltaStream ON DYNAMIC TABLE product;
```

Show lessSee more

Scroll to top

### Comparison between streams and tasks and dynamic tables

| Key Characteristics | Streams and Tasks | Dynamic Tables |
| --- | --- | --- |
| Data transformation | Tasks use an imperative approach: You write procedural code to transform data from base tables. | Dynamic tables use a declarative approach: You write a query that specifies the result you want, and data is retrieved and transformed from the base tables used in the query. Except for [Supported non-deterministic functions in incremental and full refresh modes](dynamic-tables-supported-queries.html#label-dynamic-tables-intro-supported-nondeterministic-functions), the query can’t contain non-deterministic functions. |
| Refresh timing | You define a schedule for executing the code that transforms the data. | An automated refresh process determines the schedule for performing refreshes. The process schedules these refreshes to meet the specified target level of freshness (lag). |
| Orchestration | The procedural code can contain calls to non-deterministic code, stored procedures, and other tasks. The procedural code can contain calls to UDFs and external functions. | Although the SELECT statement for a dynamic table can contain joins, aggregations, window functions, and other SQL functions and constructions, the statement cannot contain calls to stored procedures and tasks. Currently, the SELECT statement also cannot contain calls to external functions.  This limitation is due to the way in which dynamic tables are refreshed. To refresh the data, an automated process analyzes the SELECT statement for the dynamic table in order to determine the best approach to refresh the data. The automated process cannot determine this for certain types of queries.  For the complete list of restrictions on the SELECT statement, see [Supported queries in incremental and full refresh modes](dynamic-tables-supported-queries.html#label-dynamic-tables-intro-refresh-queries) and [General limitations](dynamic-tables-limitations.html#label-dynamic-tables-limitations). |
| Data freshness | Tasks can use streams to refresh data in target tables incrementally. You can schedule these tasks to run on a regular basis. | An automated refresh process performs incremental refreshes of dynamic tables on a regular basis. The process determines the schedule based on the target “freshness” of the data that you specify. |

See moreShow less

Expand

### Example: Comparison of data transformation between streams and tasks and dynamic tables

The example in [Transform loaded JSON data on a schedule](data-pipelines-examples.html#label-data-pipeline-examples-json) uses streams and tasks to transform and insert new data into a target
table (`names`) as the data is streamed into a landing table (`raw`).

The following examples demonstrate how to perform the same transformation using dynamic tables. When creating a dynamic table,
you specify the query for the results that you want to see. For the incremental refresh of the data, you don’t need to create a
stream to track changes and write a task to examine those changes and apply the changes to the target table. The automated refresh
process does this for you based on the query that you specify.

| SQL Statements for Streams and Tasks | SQL Statements for Dynamic Tables |
| --- | --- |
| CopyExpand  ``` -- Create a landing table to store -- raw JSON data. CREATE OR REPLACE TABLE raw   (var VARIANT);  -- Create a stream to capture inserts -- to the landing table. CREATE OR REPLACE STREAM rawstream1   ON TABLE raw;  -- Create a table that stores the -- names of office visitors from the -- raw data. CREATE OR REPLACE TABLE names   (id INT,    first_name STRING,    last_name STRING);  -- Create a task that inserts new name -- records from the rawstream1 stream -- into the names table. -- Execute the task every minute when -- the stream contains records. CREATE OR REPLACE TASK raw_to_names   WAREHOUSE = mywh   SCHEDULE = '1 minute'   WHEN     SYSTEM$STREAM_HAS_DATA('rawstream1')   AS     MERGE INTO names n       USING (         SELECT var:id id, var:fname fname, var:lname lname,                 metadata$action, metadata$isupdate                   FROM rawstream1       ) r1 ON n.id = TO_NUMBER(r1.id)       WHEN MATCHED AND metadata$action = 'DELETE'             AND NOT metadata$isupdate THEN           DELETE       WHEN MATCHED AND metadata$action = 'INSERT' THEN         UPDATE SET n.first_name = r1.fname, n.last_name = r1.lname       WHEN NOT MATCHED AND metadata$action = 'INSERT' THEN         INSERT (id, first_name, last_name)           VALUES (r1.id, r1.fname, r1.lname);  -- Start the task ALTER TASK raw_to_names RESUME; ```  Show lessSee more  Scroll to top | CopyExpand  ``` -- Create a landing table to store -- raw JSON data. CREATE OR REPLACE TABLE raw   (var VARIANT);  -- Create a dynamic table containing the -- names of office visitors from -- the raw data. -- Try to keep the data up to date within -- 1 minute of real time. CREATE OR REPLACE DYNAMIC TABLE names   TARGET_LAG = '1 minute'   WAREHOUSE = mywh   AS     SELECT var:id::int id, var:fname::string first_name,     var:lname::string last_name FROM raw; ```  Show lessSee more  Scroll to top |

See moreShow less

Expand

## Dynamic tables compared to materialized views

Dynamic tables have some similarities to materialized views in that both materialize the results of a query.
However, there are important differences:

| Key Characteristics | Materialized Views | Dynamic Tables |
| --- | --- | --- |
| Query performance | Materialized views are designed to improve query performance transparently.  For example, if you query the base table, the query optimizer in Snowflake can rewrite the query automatically to query the materialized view instead. | Dynamic tables are designed to build multi-level data pipelines.  Although dynamic tables can improve query performance, the query optimizer in Snowflake does not automatically rewrite queries to use dynamic tables. A dynamic table is used in a query only if you specify the dynamic table in the query. |
| Query complexity | A materialized view can only use a single base table. A materialized view cannot be based on a complex query (that is, a query with joins or nested views). | A dynamic table can be based on a complex query, including one with joins and unions. |
| Data freshness | Data accessed through materialized views is [always current](views-materialized.html#label-materialized-views-advantages). If a DML operation changes the data in the base table, Snowflake either updates the materialized view or uses the updated data from the base table. | The data is current up to the target lag time for the dynamic table.  Dynamic table maintenance and refresh is automatically managed by a separate compute service, including refresh logic, along with the compute for any updates, typically at additional cost. For more information, see [Understanding costs for dynamic tables](dynamic-tables-cost). |

See moreShow less

Expand
