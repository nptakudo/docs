---
title: "Limitations and considerations for Snowpipe Streaming with high-performance architecture"
url: "https://docs.snowflake.com/en/user-guide/snowpipe-streaming/snowpipe-streaming-high-performance-limitations"
---

# Limitations and considerations for Snowpipe Streaming with high-performance architecture

This document outlines the known limitations and key considerations for Snowpipe Streaming with high-performance architecture.

## General and service-level limitations

* The service is available in all Amazon Web Services (AWS), Microsoft Azure, and Google Cloud Platform (GCP) regions except for government-specific regions and regions in China.

## Table limits

* Maximum throughput: A table can achieve an aggregate throughput of 10 GBps uncompressed.

## Pipe limits

* Channels per pipe: By default, a single pipe can have up to 2,000 active channels. Contact Snowflake Support if you require more channels for your use case.
* Pipes for Snowpipe Streaming: The maximum number of PIPE objects configured for Snowpipe Streaming is limited to 1,000 per account and 10 per table. If you require more pipes, contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

## Channel limits

Each channel has the following soft limits. If your application requires higher throughput per channel, contact Snowflake Support to discuss increasing these limits.

* SDK throughput: 12 MBps (uncompressed)
* REST endpoint throughput: 1 MBps (observed size)
* REST payload limit: 4 MB per request (observed size). To ingest more data per request, use compression (Gzip or ZSTD). This lets you fit a larger uncompressed data volume into the 4 MB limit.
* Request rate: 10 requests per second (RPS).

## Ingestion and data-specific limitations

* The ON\_ERROR option in Snowpipe Streaming with high-performance architecture only supports CONTINUE.
* Sudden spikes in data throughput might experience brief increases in end-to-end latency because the service is elastically scaling to support the new throughput level.
* Partitioned Iceberg tables aren’t supported.
* MATCH\_BY\_COLUMN\_NAME is not supported with default, auto-increment, or identity columns:

  The MATCH\_BY\_COLUMN\_NAME option isn’t supported when you load data into tables that contain columns that are defined with the DEFAULT, AUTOINCREMENT, or IDENTITY properties. When you use this option, the streaming ingestion process explicitly inserts NULL values for these columns, overriding the intended default value or the auto-generation mechanism.

  Workaround: To use these column properties, you must omit MATCH\_BY\_COLUMN\_NAME. Instead, you define the pipe by using a COPY INTO statement that explicitly lists only the columns for which the source data provides values. The columns with the auto-generation properties must be omitted from the target column list to ensure that the table engine applies the defined value generation logic.

## SDK and architectural limitations

* Supported architectures (Rust Core): ARM64 Mac, Windows, ARM64-Linux, and x86\_64-Linux.
* Linux requirements: If you use the SDK on Linux, your system must have glibc version 2.26 or later.
* Timezone: The SDK automatically uses UTC, and this setting can’t be changed by the user.
* Authentication: RSA Key-Pair authentication is required. OAuth and Personal Access Tokens (PATs) aren’t supported.
* Snowpark Container Services (SPCS) isn’t supported.
