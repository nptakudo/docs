---
title: "FetchBoxFileMetadataInstance 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/fetchboxfilemetadatainstance"
---

# FetchBoxFileMetadataInstance 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-box-nar

## Description

Retrieves specific metadata instance associated with a Box file using template key and scope.

## Tags

box, instance, metadata, storage, template

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Box Client Service | Controller Service used to obtain a Box API connection. |
| File ID | The ID of the file for which to fetch metadata. |
| Template Key | The metadata template key to retrieve. |
| Template Scope | The metadata template scope (e.g., ‘enterprise’, ‘global’). |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | A FlowFile will be routed here if there is an error fetching metadata instance from the file. |
| file not found | FlowFiles for which the specified Box file was not found will be routed to this relationship. |
| success | A FlowFile containing the metadata instance will be routed to this relationship upon successful processing. |
| template not found | FlowFiles for which the specified metadata template was not found will be routed to this relationship. |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| box.id | The ID of the file from which metadata was fetched |
| box.metadata.template.key | The metadata template key |
| box.metadata.template.scope | The metadata template scope |
| mime.type | The MIME Type of the FlowFile content |
| error.code | The error code returned by Box |
| error.message | The error message returned by Box |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.box.FetchBoxFile](fetchboxfile)
* [org.apache.nifi.processors.box.FetchBoxFileInfo](fetchboxfileinfo)
* [org.apache.nifi.processors.box.ListBoxFile](listboxfile)
* [org.apache.nifi.processors.box.ListBoxFileMetadataInstances](listboxfilemetadatainstances)
