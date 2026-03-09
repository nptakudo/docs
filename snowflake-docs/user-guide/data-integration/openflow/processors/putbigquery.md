---
title: "PutBigQuery 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/putbigquery"
---

# PutBigQuery 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-gcp-nar

## Description

Writes the contents of a FlowFile to a Google BigQuery table. The processor is record based so the schema that is used is driven by the RecordReader. Attributes that are not matched to the target schema are skipped. Exactly once delivery semantics are achieved via stream offsets.

## Tags

bigquery, bq, google, google cloud

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| GCP Credentials Provider Service | The Controller Service used to obtain Google Cloud Platform credentials. |
| bigquery-api-endpoint | Can be used to override the default BigQuery endpoint. Default is bigquerystorage.googleapis.com:443. Format must be hostname:port. |
| bq.append.record.count | The number of records to be appended to the write stream at once. Applicable for both batch and stream types |
| bq.dataset | BigQuery dataset name (Note - The dataset must exist in GCP) |
| bq.record.reader | Specifies the Controller Service to use for parsing incoming data. |
| bq.skip.invalid.rows | Sets whether to insert all valid rows of a request, even if invalid rows exist. If not set the entire insert request will fail if it contains an invalid row. |
| bq.table.name | BigQuery table name |
| bq.transfer.type | Defines the preferred transfer type streaming or batching |
| gcp-project-id | Google Cloud Project ID |
| gcp-retry-count | How many retry attempts should be made before routing to the failure relationship. |
| proxy-configuration-service | Specifies the Proxy Configuration Controller Service to proxy network requests. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | FlowFiles are routed to this relationship if the Google BigQuery operation fails. |
| success | FlowFiles are routed to this relationship after a successful Google BigQuery operation. |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| bq.records.count | Number of records successfully inserted |

See moreShow less

Expand
