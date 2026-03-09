---
title: "AbortQueryJob 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/abortqueryjob"
---

# AbortQueryJob 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

com.snowflake.openflow.runtime | runtime-salesforce-processors-nar

## Description

Aborts a Query Job in Salesforce using the Bulk API 2.0.

## Tags

abort, bulk, job, preview, query, salesforce

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Job ID | The ID of the job for which the status is checked. |
| Salesforce Client | Salesforce Client to interact with the APIs |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| comms.failure | A FlowFile is routed to this relationship if the Query Job could not be aborted but the operation might be retried |
| failure | A FlowFile is routed to this relationship if the Query Job could not be aborted |
| success | If the Query Job has been successfully aborted, the FlowFile is routed to this relationship |

See moreShow less

Expand

## See also

* [com.snowflake.openflow.runtime.processors.salesforce.DeleteQueryJob](deletequeryjob)
* [com.snowflake.openflow.runtime.processors.salesforce.GetQueryJobResult](getqueryjobresult)
* [com.snowflake.openflow.runtime.processors.salesforce.GetQueryJobStatus](getqueryjobstatus)
* [com.snowflake.openflow.runtime.processors.salesforce.SubmitQueryJob](submitqueryjob)
