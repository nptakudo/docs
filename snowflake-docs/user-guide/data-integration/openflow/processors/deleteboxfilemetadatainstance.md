---
title: "DeleteBoxFileMetadataInstance 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/deleteboxfilemetadatainstance"
---

# DeleteBoxFileMetadataInstance 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-box-nar

## Description

Deletes a metadata instance from a Box file using the specified template key

## Tags

box, delete, metadata, storage, templates

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Box Client Service | Controller Service used to obtain a Box API connection. |
| File ID | The ID of the file from which to delete metadata. |
| Template Key | The key of the metadata template instance to delete. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | A FlowFile is routed to this relationship if an error occurs during metadata deletion. |
| file not found | FlowFiles for which the specified Box file was not found will be routed to this relationship. |
| success | A FlowFile is routed to this relationship after metadata has been successfully deleted. |
| template not found | FlowFiles for which the specified metadata template was not found will be routed to this relationship. |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| box.id | The ID of the file from which metadata was deleted |
| box.template.key | The template key used for metadata deletion |
| error.code | The error code returned by Box |
| error.message | The error message returned by Box |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.box.CreateBoxFileMetadataInstance](createboxfilemetadatainstance)
* [org.apache.nifi.processors.box.FetchBoxFileMetadataInstance](fetchboxfilemetadatainstance)
* [org.apache.nifi.processors.box.UpdateBoxFileMetadataInstance](updateboxfilemetadatainstance)
