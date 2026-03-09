---
title: "StandardKustoIngestService"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/controllers/standardkustoingestservice"
---

# StandardKustoIngestService

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Description

Sends batches of flowfile content or stream flowfile content to an Azure ADX cluster.

## Tags

ADX, Azure, Data, Explorer, Kusto, azure, ingest

## Properties

In the list below required Properties are shown with an asterisk (\*).
Other properties are considered optional. The table also indicates any default values, and whether a property supports the NiFi Expression Language.

| Display Name | API Name | Default Value | Allowable Values | Description |
| --- | --- | --- | --- | --- |
| Application Client ID \* | Application Client ID |  |  | Azure Data Explorer Application Client Identifier for Authentication |
| Application Key \* | Application Key |  |  | Azure Data Explorer Application Key for Authentication |
| Application Tenant ID \* | Application Tenant ID |  |  | Azure Data Explorer Application Tenant Identifier for Authentication |
| Authentication Strategy \* | Authentication Strategy | MANAGED\_IDENTITY | * Application Credentials * Managed Identity * Azure CLI (Dev Only) | Authentication method for access to Azure Data Explorer |
| Cluster URI \* | Cluster URI |  |  | Azure Data Explorer Cluster URI |

See moreShow less

Expand

## State management

This component does not store state.

## Restricted

This component is not restricted.

## System Resource Considerations

This component does not specify system resource considerations.
