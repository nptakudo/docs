---
title: "CalculateRecordStats 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/calculaterecordstats"
---

# CalculateRecordStats 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-standard-nar

## Description

Counts the number of Records in a record set, optionally counting the number of elements per category, where the categories are defined by user-defined properties.

## Tags

metrics, record, stats

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| record-stats-limit | Limit the number of individual stats that are returned for each record path to the top N results. |
| record-stats-reader | A record reader to use for reading the records. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | If a FlowFile cannot be processed for any reason, it is routed to this Relationship. |
| success | All FlowFiles that are successfully processed, are routed to this Relationship. |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| record.count | A count of the records in the record set in the FlowFile. |
| recordStats.<User Defined Property Name>.count | A count of the records that contain a value for the user defined property. |
| recordStats.<User Defined Property Name>.<value>.count | Each value discovered for the user defined property will have its own count attribute. Total number of top N value counts to be added is defined by the limit configuration. |

See moreShow less

Expand
