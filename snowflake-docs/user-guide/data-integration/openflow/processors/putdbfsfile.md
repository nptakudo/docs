---
title: "PutDBFSFile 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/putdbfsfile"
---

# PutDBFSFile 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

com.snowflake.openflow.runtime | runtime-databricks-processors-nar

## Description

Write FlowFile content to DBFS.

## Tags

databricks, dbfs, openflow

## Input Requirement

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| DBFS File Path | DBFS file path e.g. /directory/file.txt |
| Databricks Client | Databricks Client Service. |
| Overwrite Policy | What action to take if a file already exists at the destination path. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | Databricks failure relationship |
| success | Databricks success relationship |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| error.code | The error code for the SQL statement if an error occurred. |
| error.message | The error message for the SQL statement if an error occurred. |

See moreShow less

Expand
