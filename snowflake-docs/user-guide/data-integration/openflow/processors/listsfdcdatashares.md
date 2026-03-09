---
title: "ListSFDCDataShares 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/listsfdcdatashares"
---

# ListSFDCDataShares 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

com.snowflake.openflow.runtime | runtime-salesforce-processors-nar

## Description

List the available data shares in the organization that are available to the identified user.

## Tags

list, objects, preview, salesforce, sfdc

## Input Requirement

FORBIDDEN

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Salesforce Data Cloud Client | Salesforce Data Cloud Client to interact with the APIs |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| success | FlowFile containing the list of available objects will be routed to this relationship |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| nbObjects | The number of data shares listed in the organization that are available to the identified user. |

See moreShow less

Expand

## See also

* [com.snowflake.openflow.runtime.processors.salesforce.DeleteQueryJob](deletequeryjob)
* [com.snowflake.openflow.runtime.processors.salesforce.DescribeSFDCObject](describesfdcobject)
* [com.snowflake.openflow.runtime.processors.salesforce.GetQueryJobResult](getqueryjobresult)
* [com.snowflake.openflow.runtime.processors.salesforce.SubmitQueryJob](submitqueryjob)
