---
title: "GetUnityCatalogFile 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/getunitycatalogfile"
---

# GetUnityCatalogFile 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

com.snowflake.openflow.runtime | runtime-databricks-processors-nar

## Description

Read a Unity Catalog file up to 5 GiB.

## Tags

databricks, openflow, unity catalog

## Input Requirement

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Databricks Client | Databricks Client Service. |
| Unity Catalog File Path | Unity Catalog file path e.g. /Volumes/catalog/schema/volume\_name/file.txt |

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
