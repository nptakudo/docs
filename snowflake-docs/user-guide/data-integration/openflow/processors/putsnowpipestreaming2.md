---
title: "PutSnowpipeStreaming2 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/putsnowpipestreaming2"
---

# PutSnowpipeStreaming2 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

com.snowflake.openflow.runtime | runtime-snowpipe-streaming-2-processors-nar

## Description

Send Records formatted as Newline Delimited JSON to Snowflake Database Pipes using Snowpipe Streaming Version 2.

## Tags

NDJSON, Preview, Snowflake, Snowpipe Streaming

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Account | Snowflake Account Identifier with Organization Name and Account Name formatted as [organization-name]-[account-name] |
| Authentication Strategy | Strategy for authenticating Snowflake connections |
| Channel Group | Group for managing distinct Snowpipe Streaming Channels with partitioning |
| Channel Insert Timeout | Maximum duration to retry inserting records before failing with an upper bound of 5 minutes |
| Database | Snowflake Database destination for processed records |
| File Fragment Count | Maximum number of File Fragments sent to object storage for Snowpipe Streaming ingestion from input FlowFiles. Must be between 1 and 100. |
| File Fragment Size | Maximum size in bytes for each File Fragment sent to object storage for Snowpipe Streaming ingestion. Must be between 1 KB and 256 MB |
| Offset Token End Expression | Expression Language definition to produce the highest offset token for a FlowFile as a monotonically increasing number |
| Offset Token Record Pointer | JSON Pointer to offset token in each record required when the last committed offset token is between start and end boundaries |
| Offset Token Start Expression | Expression Language definition to produce the lowest offset token for a FlowFile as a monotonically increasing number |
| Offset Tracking Timeout | Maximum duration to poll channel status for committed offset tokens |
| Pipe | Snowflake Pipe destination for processed records |
| Private Key Service | RSA Private Key Service for authenticating connections |
| Schema | Snowflake Schema destination for processed records |
| Transfer Strategy | Strategy for transferring records to Snowpipe Streaming |
| User | Snowflake User for authenticating connections |
| Web Client Service Provider | Web Client Service Provider supporting HTTP request and response handling |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | FlowFiles that failed to upload to Snowflake |
| invalid | FlowFiles that Snowflake identified as containing one or more invalid rows resulting in partial transmission |
| success | FlowFiles successfully uploaded to Snowflake |

See moreShow less

Expand
