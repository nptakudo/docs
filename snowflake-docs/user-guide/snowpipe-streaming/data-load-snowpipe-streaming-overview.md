---
title: "Snowpipe Streaming"
url: "https://docs.snowflake.com/en/user-guide/snowpipe-streaming/data-load-snowpipe-streaming-overview"
---

# Snowpipe Streaming

Snowpipe Streaming is Snowflake’s service for continuous, low-latency loading of streaming data directly into Snowflake. It enables near real-time data ingestion and analysis, crucial for timely insights and immediate operational responses. High volumes of data from diverse streaming sources are made available for query and analysis within seconds.

## Value of Snowpipe Streaming

* Real-time data availability: Ingests data as it arrives, unlike traditional batch loading methods, supporting use cases like live dashboards, real-time analytics, and fraud detection.
* Efficient streaming workloads: Utilizes Snowpipe Streaming SDKs to write rows directly into tables, bypassing the need for staging data in intermediate cloud storage. This direct approach reduces latency and simplifies ingestion architecture.
* Simplified data pipelines: Offers a streamlined approach for continuous data pipelines from sources such as application events, IoT sensors, Change Data Capture (CDC) streams, and message queues (e.g., Apache Kafka).
* Serverless and scalable: As a serverless offering, it automatically scales compute resources based on ingestion load.
* Cost-effective for streaming: Billing is optimized for streaming ingestion, potentially offering more cost-effective solutions for high-volume, low-latency data feeds.

With Snowpipe Streaming, you can build real-time data applications on the Snowflake Data Cloud, so that you make decisions based on the freshest data available.

## Snowpipe Streaming implementations

Snowpipe Streaming offers two distinct implementations to cater to diverse data-ingestion needs and performance expectations: Snowpipe Streaming with high-performance architecture and Snowpipe Streaming with classic architecture:

* [Snowpipe Streaming with high-performance architecture](snowpipe-streaming-high-performance-overview)

  Snowflake has engineered this next-generation implementation to significantly enhance throughput, optimize streaming performance, and provide a predictable cost model, setting the stage for advanced data-streaming capabilities.

  Key Characteristics:

  + SDK: Utilizes the new [snowpipe-streaming SDK](https://repo1.maven.org/maven2/com/snowflake/snowpipe-streaming/).
  + Pricing: Features transparent, throughput-based pricing (credits per uncompressed GB).
  + Data flow management: Utilizes the PIPE object for managing data flow and enabling lightweight transformations at ingest time. Channels are opened against this PIPE object.
  + Ingestion: Offers a REST API for direct, lightweight data ingestion through the PIPE.
  + Schema validation: Performed on the server side during ingestion against the schema defined in the PIPE.
  + Performance: Engineered for significantly higher throughput and improved query efficiency on ingested data.

  We encourage you to explore this advanced architecture, especially for new streaming projects.
* [Snowpipe Streaming with classic architecture](snowpipe-streaming-classic-overview)

  > This is the original, generally available implementation, providing a reliable solution for established data pipelines.
  >
  > Key Characteristics:
  >
  > + SDK: Utilizes the [snowflake-ingest-sdk](https://mvnrepository.com/artifact/net.snowflake/snowflake-ingest-sdk).
  > + Data flow management: Does not use the PIPE object concept for streaming ingestion. Channels are configured and opened directly against target tables.
  > + Pricing: Based on a combination of serverless compute resources utilized for ingestion and the number of active client connections.

## Choosing your implementation

Consider your immediate needs and long-term data strategy when choosing an implementation:

* New streaming projects: We recommend evaluating the Snowpipe Streaming high-performance architecture for its forward-looking design, better performance, scalability, and cost predictability.
* Performance requirements: The high-performance architecture is built to maximize throughput and optimize real-time performance.
* Pricing preference: The high-performance architecture offers clear, throughput-based pricing, while the classic architecture bills based on serverless compute usage and client connections.
* Existing setups: Existing applications using classic architecture can continue to operate. For future expansions or redesigns, consider migrating to or incorporating the high-performance architecture.
* Feature set and management: The PIPE object in the high-performance architecture introduces enhanced management and transformation capabilities not present in the classic architecture.

## Snowpipe Streaming versus Snowpipe

Snowpipe Streaming is intended to complement Snowpipe, not replace it. Use the Snowpipe Streaming API in streaming scenarios where data is streamed with rows (for example, Apache Kafka topics) instead of written to files. The API fits into an ingest workflow that includes an existing custom Java application that produces or receives records. WIth the API, you don’t need to create files to load data into Snowflake tables and the API enables the automatic, continuous loading of data streams into Snowflake as the data becomes available.

> ![Snowpipe Streaming](../../_images/data-load-snowpipe-streaming.png)

The following table describes the differences between Snowpipe Streaming and Snowpipe:

| Category | Snowpipe Streaming | Snowpipe |
| --- | --- | --- |
| Form of data to load | Rows | Files. If your existing data pipeline generates files in blob storage, we recommend using Snowpipe instead of the API. |
| Third-party software requirements | Custom Java application code wrapper for the Snowflake Ingest SDK | None |
| Data ordering | Ordered insertions within each channel | Not supported. Snowpipe can load data from files in an order different from the file creation timestamps in cloud storage. |
| Load history | Load history recorded in [SNOWPIPE\_STREAMING\_FILE\_MIGRATION\_HISTORY view](../../sql-reference/account-usage/snowpipe_streaming_file_migration_history) (Account Usage) | Load history recorded in [COPY\_HISTORY](../../sql-reference/account-usage/copy_history) (Account Usage) and [COPY\_HISTORY function](../../sql-reference/functions/copy_history) (Information Schema) |
| Pipe object | The classic architecture does not require a pipe object: the API writes records directly to target tables. The high-performance architecture requires a pipe object. | Requires a pipe object that queues and loads staged file data into target tables. |

See moreShow less

Expand

## Channels

The API ingests rows through one or more channels. A channel represents a logical, named streaming connection to Snowflake for loading data into a table in an ordered manner. The ordering of rows and their corresponding offset tokens are preserved within a channel but not across channels that point to the same table.

In the classic architecture, a single channel maps to exactly one table in Snowflake; though multiple channels can point to the same table. The client SDK can open multiple channels to multiple tables; however the SDK can’t open channels across accounts. Channels are meant to be long lived when a client is actively inserting data, and should be reused across client process restarts as offset token information is retained. Data inside the channel is automatically flushed every 1 second by default and the channel doesn’t need to be closed. For more information, see [Latency recommendations](snowpipe-streaming-classic-recommendation.html#label-snowpipe-streaming-latency).

> ![Snowpipe streaming client channel table mapping](../../_images/data-load-snowpipe-streaming-client-channel.png)

You can permanently drop channels by using the `DropChannelRequest` API when you no longer need the channel and the associated offset metadata. There are two ways to drop a channel:

* Dropping a channel at closing. Data inside the channel is automatically flushed before the channel is dropped.
* Dropping a channel blindly. We don’t recommend this because dropping a channel blindly discards any pending data.

You can run the SHOW CHANNELS command to list the channels for which you have access privileges. For more information, see [SHOW CHANNELS](../../sql-reference/sql/show-channels).

Note

Inactive channels, along with their offset tokens, are deleted automatically after 30 days of inactivity.

## Offset tokens

An *offset token* is a string that a client can include in their row-submission method requests (for example, for single or multiple rows) to track ingestion progress on a per-channel basis. The specific methods used are `insertRow` or `insertRows` for the classic architecture, and `appendRow` or `appendRows` for the high-performance architecture. The token is initialized to NULL on channel creation and is updated when the rows with a provided offset token are committed to Snowflake through an asynchronous process. Clients can periodically make `getLatestCommittedOffsetToken` method requests to get the latest committed offset token for a particular channel and use that to reason about ingestion progress.Note that this token is *not* used by Snowflake to perform de-duplication; however, clients can use this token to perform de-duplication using your custom code.

When a client re-opens a channel, the latest persisted offset token is returned. The client can reset its position in the data source by using the token to avoid sending the same data twice. Note that when a channel re-open event occurs, any uncommitted data buffered in Snowflake is discarded to avoid committing it.

You can use the latest committed offset token to perform the following common use cases:

> * Tracking the ingestion progress
> * Checking whether a specific offset has been committed by comparing it with the latest committed offset token
> * Advancing the source offset and purging the data that has already been committed
> * Enabling de-duplication and ensuring exactly-once delivery of data

For example, the Kafka connector could read an offset token from a topic such as `<partition>:<offset>`, or simply `<offset>`, if the partition is encoded in the channel name. Consider the following scenario:

1. The Kafka connector comes online and opens a channel corresponding to `Partition 1` in Kafka topic `T` with the channel name `T:P1`.
2. The connector begins reading records from the Kafka partition.
3. The connector calls the API, making an `insertRows` method request, with the offset associated with the record as the offset token.

   For example, the offset token could be `10`, referring to the tenth record in the Kafka partition.
4. The connector periodically makes `getLatestCommittedOffsetToken` method requests to determine the ingest progress.

If the Kafka connector crashes, the following procedure could be completed to resume reading records from the correct offset for the Kafka partition:

1. The Kafka connector comes back online and re-opens the channel, using the same name as earlier.
2. The connector calls the API, making a `getLatestCommittedOffsetToken` method request to get the latest committed offset for the partition.

   For example, assume the latest persisted offset token is `20`.
3. The connector uses the Kafka read APIs to reset a cursor corresponding to the offset plus 1 (`21` in this example).
4. The connector resumes reading records. No duplicate data is retrieved after the read cursor is repositioned successfully.

In another example, an application reads logs from a directory and uses the Snowpipe Streaming Client SDK to export those logs to Snowflake. You could build a log export application that does the following:

1. List files in the log directory.

   Assume that the logging framework generates log files that can be ordered lexicographically and that new log files are positioned at the end of this ordering.
2. Reads a log file line by line and calls the API, making `insertRows` method requests with an offset token corresponding to the log file name and the line count or byte position.

   For example, an offset token could be `messages_1.log:20`, where `messages_1.log` is the name of the log file, and `20` is the line number.

If the application crashes or needs to be restarted, it would then call the API, making a `getLatestCommittedOffsetToken` method request to retrieve an offset token that corresponds to the last exported log file and line. Continuing with the example, this could be `messages_1.log:20`. The application would then open `messages_1.log` and seek line `21` to prevent the same log line from being ingested twice.

Note

The offset token information can get lost. The offset token is linked to a channel object, and a channel is automatically cleared if no new ingestion is performed using the channel for a period of 30 days. To prevent the loss of the offset token, consider maintaining a separate offset and resetting the channel’s offset token if required.

## Roles of `offsetToken` and `continuationToken`

Both `offsetToken` and `continuationToken` are used to ensure exactly-once data delivery, but they serve different purposes and are managed by different subsystems. The primary distinction is who controls the token’s value and the scope of its use.

* `continuationToken` (only applies to the high-performance architecture and is only used by direct REST API users):

  This token is managed by Snowflake and is essential for maintaining the state of a single, continuous streaming session. When a client sends data using the `Append Rows` API, Snowflake returns a `continuationToken`. The client must bounce back this token in its next AppendRows request to ensure the data is received by Snowflake in the correct order and without gaps. Snowflake uses the token to detect and prevent duplicate data or missing data in the event of an SDK retry.
* `offsetToken` (applies to both the classic and high-performance architectures):

  This token is a user-defined identifier that enables exactly-once delivery from an external source. Snowflake doesn’t use this token for its own internal operations or to prevent re-ingestion. Instead, Snowflake simply stores this value. It is the responsibility of the external system (like a Kafka connector) to read the offsetToken from Snowflake and use it to track its own ingestion progress and avoid sending duplicate data if the external stream needs to be replayed.

## Insert-only operations

The API is currently limited to inserting rows. To modify, delete, or combine data, write the “raw” records to one or more staging tables. Merge, join, or transform the data by using [continuous data pipelines](../data-pipelines-intro) to insert modified data into destination reporting tables.

## Supported Java data types

The following table summarizes which Java data types are supported for ingestion into Snowflake columns:

| Snowflake column type | Allowed Java data type |
| --- | --- |
| * CHAR * VARCHAR | * String * primitive data types (int, boolean, char, …) * BigInteger, BigDecimal |
| * BINARY | * byte[] * String (hex-encoded) |
| * NUMBER | * numeric types (BigInteger, BigDecimal, byte, int, double, …) * String |
| * FLOAT | * numeric types (BigInteger, BigDecimal, byte, int, double, …) * String |
| * BOOLEAN | * boolean * numeric types (BigInteger, BigDecimal, byte, int, double, …) * String   See [boolean conversion details](../../sql-reference/data-types-logical.html#label-boolean-conversion). |
| * TIME | * java.time.LocalTime * java.time.OffsetTime * String    + [Integer-stored time](../../sql-reference/date-time-input-output.html#label-date-time-input-output-auto-detection-integer-stored)   + `HH24:MI:SS.FFTZH:TZM` (for example, `20:57:01.123456789+07:00`)   + `HH24:MI:SS.FF` (for example, `20:57:01.123456789`)   + `HH24:MI:SS` (for example, `20:57:01`)   + `HH24:MI` (for example, `20:57`) |
| * DATE | * java.time.LocalDate * java.time.LocalDateTime * java.time.OffsetDateTime * java.time.ZonedDateTime * java.time.Instant * String    + [Integer-stored date](../../sql-reference/date-time-input-output.html#label-date-time-input-output-auto-detection-integer-stored)   + `YYYY-MM-DD` (for example, `2013-04-28`)   + `YYYY-MM-DDTHH24:MI:SS.FFTZH:TZM` (for example, `2013-04-28T20:57:01.123456789+07:00`)   + `YYYY-MM-DDTHH24:MI:SS.FF` (for example, `2013-04-28T20:57:01.123456`)   + `YYYY-MM-DDTHH24:MI:SS` (for example, `2013-04-28T20:57:01`)   + `YYYY-MM-DDTHH24:MI` (for example, `2013-04-28T20:57`)   + `YYYY-MM-DDTHH24:MI:SSTZH:TZM` (for example, `2013-04-28T20:57:01-07:00`)   + `YYYY-MM-DDTHH24:MITZH:TZM` (for example, `2013-04-28T20:57-07:00`) |
| * TIMESTAMP\_NTZ * TIMESTAMP\_LTZ * TIMESTAMP\_TZ | * java.time.LocalDate * java.time.LocalDateTime * java.time.OffsetDateTime * java.time.ZonedDateTime * java.time.Instant * String    + [Integer-stored timestamp](../../sql-reference/date-time-input-output.html#label-date-time-input-output-auto-detection-integer-stored)   + `YYYY-MM-DD` (for example, `2013-04-28`)   + `YYYY-MM-DDTHH24:MI:SS.FFTZH:TZM` (for example, `2013-04-28T20:57:01.123456789+07:00`)   + `YYYY-MM-DDTHH24:MI:SS.FF` (for example, `2013-04-28T20:57:01.123456`)   + `YYYY-MM-DDTHH24:MI:SS` (for example, `2013-04-28T20:57:01`)   + `YYYY-MM-DDTHH24:MI` (for example, `2013-04-28T20:57`)   + `YYYY-MM-DDTHH24:MI:SSTZH:TZM` (for example, `2013-04-28T20:57:01-07:00`)   + `YYYY-MM-DDTHH24:MITZH:TZM` (for example, `2013-04-28T20:57-07:00`) |
| * VARIANT * ARRAY | * String (must be a valid JSON) * primitive data types and their arrays * BigInteger, BigDecimal * java.time.LocalTime * java.time.OffsetTime * java.time.LocalDate * java.time.LocalDateTime * java.time.OffsetDateTime * java.time.ZonedDateTime * java.util.Map<String, T> where T is a valid VARIANT type * T[] where T is a valid VARIANT type * List<T> where T is a valid VARIANT type |
| * OBJECT | * String (must be a valid JSON object) * Map<String, T> where T is a valid variant type |
| * GEOGRAPHY | * Not supported in the classic architecture, but it’s supported in the high-performance architecture. |
| * GEOMETRY | * Not supported in the classic architecture, but it’s supported in the high-performance architecture. |

See moreShow less

Expand

## Required access privileges

Calling the Snowpipe Streaming API requires a role with the following privileges:

| Object | Privilege |
| --- | --- |
| Table | OWNERSHIP or a minimum of INSERT and EVOLVE SCHEMA (only required when using schema evolution for Kafka connector with Snowpipe Streaming) |
| Database | USAGE |
| Schema | USAGE |
| Pipe | OPERATE (Required only for the high-performance architecture) |

See moreShow less

Expand
