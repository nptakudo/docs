---
title: "GetDataShareCredentials 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/getdatasharecredentials"
---

# GetDataShareCredentials 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

com.snowflake.openflow.runtime | runtime-salesforce-processors-nar

## Description

Describe the specified data share metadata in Salesforce Data Cloud.

## Tags

daas, data cloud, describe, object, preview, salesforce, sfdc

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Calculated Insights Objects | Comma separated list of Calculated Insight Object names to describe. |
| Connection Pooling Service | The Connection Pooling Service that is used to create the Snowflake volumes holding the credentials. |
| Data Lake Objects | Comma separated list of Data Lake Object names to describe. |
| Data Model Objects | Comma separated list of Data Model Object names to describe. |
| Data Share Name | The name of the Data Share to describe. |
| Salesforce Data Cloud Client | Salesforce Data Cloud Client to interact with the APIs |

See moreShow less

Expand

## State management

| Scopes | Description |
| --- | --- |
| CLUSTER | Provides information about the last time an external volume has been created/updated for credentials. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| comms.failure | A FlowFile is routed to this relationship if the data share credentials metadata could not be retrieved but the operation might be retried |
| failure | A FlowFile is routed to this relationship if the data share credentials cannot be retrieved or volumes cannot be created |
| success | FlowFile containing the data share metadata after successful creation of the volumes will be routed to this relationship |

See moreShow less

Expand

## See also

* [com.snowflake.openflow.runtime.processors.salesforce.ListSFDCDataShares](listsfdcdatashares)
