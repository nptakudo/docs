---
title: "EnrichCdcStream 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/enrichcdcstream"
---

# EnrichCdcStream 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

com.snowflake.openflow.runtime | runtime-database-cdc-processors-nar

## Description

Enriches incoming FlowFiles that come from CaptureChangePostgreSQL, etc. with information pertaining to which Journal Table to write to and relevant schema information. This Processor manages the schema versions for each table being processed in order to ensure that the correct Journal Table is used for each FlowFile.

## Tags

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| CDC Schema Registry | Specifies the CDC Schema Registry to use for managing the schemas of the CDC data |
| Record Reader | Specifies the Record Reader to use for reading the incoming data |
| Record Writer | Specifies the Record Writer to use for writing the outgoing data |
| Table State Service | Holds the state of replicated tables |

See moreShow less

Expand

## State management

| Scopes | Description |
| --- | --- |
| CLUSTER | Tracks the current journal table version for each table being processed. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | If any FlowFile is unable to be read, it will be routed to this Relationship. |
| schema update | If any schema update is required in order to handle incoming Records, a FlowFile is routed to this relationship. The FlowFile will include the schema information to indicate what changes are required. |
| skipped ddl event | This Relationship will be used for any DDL / Schema Change events that do not result in a change to the destination table’s schema. |
| success | Rows to be inserted into the Snowflake table will be routed to this Relationship. |
| table not in state | Used when a FlowFile references a table that does not exist in the state of replicated tables, probably after it was removed from replication. |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| table.schema.generation | The index of the journal table for incremental processing. |
| table.schema.initial | Marks the initial generation of a journal table. |
| destination.table.schema | The updated schema for the destination table. This attribute is only written for DDL events. |

See moreShow less

Expand

## See also

* [com.snowflake.openflow.runtime.processors.database.CaptureChangePostgreSQL](capturechangepostgresql)
