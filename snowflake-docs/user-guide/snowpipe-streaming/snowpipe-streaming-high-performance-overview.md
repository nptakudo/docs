---
title: "Snowpipe Streaming high-performance architecture"
url: "https://docs.snowflake.com/en/user-guide/snowpipe-streaming/snowpipe-streaming-high-performance-overview"
---

# Snowpipe Streaming high-performance architecture

The high-performance architecture for Snowpipe Streaming is engineered for modern, data-intensive organizations requiring near real-time insights. This next-generation architecture significantly advances throughput, efficiency, and flexibility for real-time ingestion into Snowflake.

For information about the classic architecture, see [Snowpipe Streaming classic architecture](snowpipe-streaming-classic-overview). For differences between the classic SDK and the high-performance SDK, see [Comparison between Snowpipe Streaming high-performance and classic SDKs](snowpipe-streaming-high-performance-comparison).

## Software requirements

**Java**

Requires Java 11 or later.

* SDK maven repository: [snowpipe-streaming Java SDK](https://central.sonatype.com/artifact/com.snowflake/snowpipe-streaming)
* API reference: [Java SDK reference](https://docs.snowflake.com/user-guide/snowpipe-streaming-sdk/reference/java/com/snowflake/ingest/streaming/package-summary.html)

**Python**

Requires Python version 3.9 or later.

* SDK PyPI repository: [snowpipe-streaming Python SDK](https://pypi.org/project/snowpipe-streaming/)
* API reference: [Python SDK reference](https://docs.snowflake.com/en/user-guide/snowpipe-streaming-sdk-python/reference/latest/index)

## Key features

* Throughput and latency:

  + High throughput: Designed to support ingest speeds of up to 10 GB/s per table.
  + Near-real-time insights: Achieves end-to-end ingest to query latencies within 5 to 10 seconds.
* Billing:

  + Simplified, transparent, throughput-based billing. For more information, see [Snowpipe Streaming high-performance architecture: Understand your costs](snowpipe-streaming-high-performance-cost).
* Flexible ingestion:

  + Java SDK and Python SDK: Utilize the new `snowpipe-streaming` SDK, with a Rust-based client core for improved client-side performance and lower resource usage.
  + [REST API](snowpipe-streaming-high-performance-rest-api): Provides a direct ingestion path, simplifying integration for lightweight workloads, IoT device data, and edge deployments.

  Note

  We recommend that you begin with the Snowpipe Streaming SDK over the REST API to benefit from the improved performance and getting-started experience.
* Optimized data handling:

  + In-flight transformations: Supports data cleansing and reshaping during ingestion using COPY command syntax within the PIPE object.
  + Enhanced channel visibility: Improved insight into ingestion status primarily through the [channel history](../../sql-reference/account-usage/snowpipe_streaming_channel_history) view in Snowsight and a new `GET_CHANNEL_STATUS` API.

This architecture is recommended for:

* Consistent ingestion of high-volume streaming workloads.
* Powering real-time analytics and dashboards for time-sensitive decision-making.
* Efficient integration of data from IoT devices and edge deployments.
* Organizations seeking transparent, predictable, and throughput-based pricing for streaming ingestion.

## New concepts: The PIPE object

While inheriting core concepts like channels and offset tokens from Snowpipe Streaming Classic, this architecture introduces the PIPE object as a central component.

The PIPE object is a named Snowflake object that acts as the entry point and definition layer for all ingested streaming data. It provides the following:

* Data processing definition: Defines how streaming data is processed before being committed to the target table, including server-side buffering for transformations or schema mapping.
* Enabling transformations: Allows for in-flight data manipulation (e.g., filtering, column reordering, simple expressions) by incorporating COPY command transformation syntax.
* Table features support: Handles ingestion into tables with defined clustering keys, DEFAULT value columns, and AUTOINCREMENT (or IDENTITY) columns.
* Schema management: Helps define the expected schema of incoming streaming data and its mapping to target table columns, enabling server-side schema validation.

  ![PIPE object for Snowpipe Streaming with high-performance architecture](../../_images/data-load-snowpipe-streaming-pipe.png)

## Default pipe

To simplify the setup process for Snowpipe Streaming, Snowflake provides a default pipe for every target table. This lets you start streaming data immediately without needing to manually execute CREATE PIPE DDL statements.

The default pipe is implicitly available for any table and offers a simplified, fully managed experience:

* On-demand creation: The default pipe is created on demand only after the first successful pipe-info or open-channel call is made against the target table. Customers can only view or describe the pipe (using [SHOW PIPES](../../sql-reference/sql/show-pipes) or [DESCRIBE PIPE](../../sql-reference/sql/desc-pipe)) after it has been instantiated by one of these calls.
* Naming convention: The default pipe follows a specific, predictable naming convention:

  + Format: `<TABLE_NAME>-STREAMING`
  + Example: If your target table is named `MY_TABLE`, the default pipe is named `MY_TABLE-STREAMING`.
* Fully Snowflake managed: This default pipe is fully managed by Snowflake. Customers can’t perform any changes to it, such as CREATE, ALTER, or DROP the default pipe.
* Visibility: Despite being automatically managed, customers can inspect the default pipe as they would a normal pipe. Customers can view it by using the [SHOW PIPES](../../sql-reference/sql/show-pipes), [DESCRIBE PIPE](../../sql-reference/sql/desc-pipe), [SHOW CHANNELS](../../sql-reference/sql/show-channels) commands, and is also included in the Account Usage metadata views: [ACCOUNT\_USAGE.PIPES](../../sql-reference/account-usage/pipes), [ACCOUNT\_USAGE.METERING\_HISTORY](../../sql-reference/account-usage/metering_history), or [ORGANIZATION\_USAGE.PIPES](../../sql-reference/organization-usage/pipes).

The default pipe is designed for simplicity and has certain limitations:

* No transformations: The internal mechanism for the default pipe uses `MATCH_BY_COLUMN_NAME` in the underlying copy statement. It doesn’t support specific data transformations.
* No pre-clustering: The default pipe doesn’t support pre-clustering for the target table.

If your streaming workflow requires specific transformations — for example, casting, filtering, or complex logic — or you need to utilize pre-clustering, you must manually create your own named pipe. For more information, see [CREATE PIPE](../../sql-reference/sql/create-pipe).

When you configure the Snowpipe Streaming SDK or REST API, you can reference the default pipe name in your client configuration to begin streaming. For more information, see [Tutorial: Get started with Snowpipe Streaming high-performance architecture SDK](snowpipe-streaming-high-performance-getting-started) and [Tutorial: Get started with Snowpipe Streaming REST API using cURL and a JWT](snowpipe-streaming-high-performance-rest-tutorial).

## Pre-clustering data during ingestion

Snowpipe Streaming can cluster in-flight data during ingestion, which improves query performance on your target tables. This feature sorts your data directly during ingestion before it’s committed. Sorting your data this way optimizes organization for faster queries.

To leverage pre-clustering, your target table must have clustering keys defined. You can then enable this feature by setting the parameter `CLUSTER_AT_INGEST_TIME` to `TRUE` in your COPY INTO statement when creating or replacing your Snowpipe Streaming pipe.

For more information, see [CLUSTER\_AT\_INGEST\_TIME](../../sql-reference/sql/copy-into-table.html#label-copy-into-table-copyoptions). This feature is only available on the high-performance architecture.

Important

When you use the pre-clustering feature, ensure that you don’t disable the auto-clustering feature on the destination table. Disabling auto-clustering can lead to degraded query performance over time.

## Schema evolution support

Snowpipe Streaming supports automatic table schema evolution. With this feature, your data pipelines can adapt seamlessly to changing data structures. When enabled, Snowflake can automatically expand the destination table by adding new columns that are detected in the incoming stream and dropping NOT NULL constraints to accommodate new data patterns. For more information, see [Table schema evolution](../data-load-schema-evolution).

### Limitations

* Native tables only: Schema evolution is supported exclusively for standard Snowflake tables. External tables and Iceberg tables aren’t supported.
* No column widening: The precision, scale, or length of existing columns can’t be increased automatically.
* No structured type support: Schema evolution isn’t supported for structured data types; for example, structured OBJECT or ARRAY columns. However, new columns that contain structured types are inferred as VARIANT, which enables JSON objects and arrays to be supported.

## Differences from Snowpipe Streaming Classic

For users familiar with the classic architecture, the high-performance architecture introduces the following changes:

* New SDK and APIs: Requires the new `snowpipe-streaming` SDK (Java SDK and REST API), necessitating client code updates for migration.
* PIPE object requirement: All data ingestion, configuration (for example, transformations), and schema definitions are managed through the server-side PIPE object, a shift from Classic’s more client-driven configuration.
* Channel association: Client applications open channels against a specific PIPE object, not directly against a target table.
* Schema validation: Moves from primarily client-side (Classic SDK) to server-side enforcement by Snowflake, based on the PIPE object.
* Migration requirements: Requires modifying client application code for the new SDK and defining PIPE objects in Snowflake.
