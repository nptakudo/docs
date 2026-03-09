---
title: "UpdateBulkJobState 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/updatebulkjobstate"
---

# UpdateBulkJobState 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

com.snowflake.openflow.runtime | runtime-salesforce-processors-nar

## Description

Updates the status of a Salesforce Bulk Job in the shared state service for a specific object type

## Tags

bulk, preview, salesforce, state

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Object Type | Salesforce object type whose state should be updated |
| Salesforce Bulk Job State Service | Controller Service managing Bulk Jobs state |
| Status | Status to set for the object type |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | Incoming FlowFile is routed here if update fails |
| success | Incoming FlowFile is routed here after state update |

See moreShow less

Expand
