---
title: "FetchSnowflakeTableProperties 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/fetchsnowflaketableproperties"
---

# FetchSnowflakeTableProperties 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

com.snowflake.openflow.runtime | runtime-snowflake-processors-nar

## Description

Reads properties from a table and stores them as flow file attributes.

## Tags

database, jdbc, openflow, snowflake

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Connection Pool | The connection pool to use to connect to Snowflake |
| Schema Name | The name of the schema |
| Table Metadata Cache Expiration Time | The time in seconds after which the cache entry will be removed |
| Table Name | The name of the table |
| Use Table Metadata Cache | Whether to cache table’s metadata instead of reading it directly from Snowflake. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | The incoming FlowFile is routed to this relationship if the properties cannot be read |
| success | The incoming FlowFile is routed to this relationship after the table properties has been successfully read |
| table not found | The incoming FlowFile is routed to this relationship if the specified table does not exist. |

See moreShow less

Expand
