---
title: "GetHubSpotObject 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/gethubspotobject"
---

# GetHubSpotObject 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

com.snowflake.openflow.runtime | runtime-hubspot-processors-nar

## Description

Get a HubSpot object and its associations by ID or unique value.

## Tags

Preview, hubspot

## Input Requirement

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| HubSpot Service | HubSpot Client Service. |
| Object ID Property | HubSpot property used to uniquely identify the object. |
| Object ID Value | Matching HubSpot property value to search for. |
| Object Type | HubSpot object type |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | HubSpot fail relationship |
| missing | HubSpot object does not exist. |
| retry | HubSpot retry relationship. FlowFiles that failed to process due to a server timeout or rate limit related error. FlowFiles routed here should be routed back into the processor. |
| success | HubSpot success relationship |

See moreShow less

Expand

## See also

* [com.snowflake.openflow.runtime.processors.hubspot.GetHubSpotSchema](gethubspotschema)
* [com.snowflake.openflow.runtime.processors.hubspot.ListArchivedHubSpotData](listarchivedhubspotdata)
* [com.snowflake.openflow.runtime.processors.hubspot.ListHubSpotObjects](listhubspotobjects)
* [com.snowflake.openflow.runtime.processors.hubspot.PutHubSpot](puthubspot)
