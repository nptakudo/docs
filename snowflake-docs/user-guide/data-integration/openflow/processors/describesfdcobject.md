---
title: "DescribeSFDCObject 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/describesfdcobject"
---

# DescribeSFDCObject 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

com.snowflake.openflow.runtime | runtime-salesforce-processors-nar

## Description

Describe the specified object metadata in Salesforce.

## Tags

describe, object, preview, salesforce, sfdc

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Object Fields Filter JSON | JSON representation describing which fields to include or exclude for Salesforce objects. |
| Object Name | The name of the object to describe. |
| Salesforce Client | Salesforce Client to interact with the APIs |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| comms.failure | A FlowFile is routed to this relationship if the object metadata could not be retrieved but the operation might be retried |
| failure | A FlowFile is routed to this relationship if the object metadata could not be retrieved |
| success | FlowFile containing the object metadata will be routed to this relationship |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| sObjectFields | Comma-separated list of the fields of the object (without non-queryable fields). |
| sObjectExcludedFields | Comma-separated list of the non-queryable fields of the object. |
| sObjectSchema | The schema associated to the object based on its fields (without non-queryable fields). |

See moreShow less

Expand

## See also

* [com.snowflake.openflow.runtime.processors.salesforce.AbortQueryJob](abortqueryjob)
* [com.snowflake.openflow.runtime.processors.salesforce.DeleteQueryJob](deletequeryjob)
* [com.snowflake.openflow.runtime.processors.salesforce.GetQueryJobResult](getqueryjobresult)
* [com.snowflake.openflow.runtime.processors.salesforce.ListSFDCObjects](listsfdcobjects)
