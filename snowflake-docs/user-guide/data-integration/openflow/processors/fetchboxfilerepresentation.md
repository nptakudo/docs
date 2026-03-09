---
title: "FetchBoxFileRepresentation 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/fetchboxfilerepresentation"
---

# FetchBoxFileRepresentation 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-box-nar

## Description

Fetches a Box file representation using a representation hint and writes it to the FlowFile content.

## Tags

box, cloud, content, download, file, representation, storage

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Box Client Service | Controller Service used to obtain a Box API connection. |
| File ID | The ID of the Box file to retrieve. |
| Representation Type | The type of representation to fetch. Common values include ‘pdf’, ‘text’, ‘jpg’, ‘png’, etc. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | FlowFiles that encounter errors during processing will be routed to this relationship. |
| file.not.found | FlowFiles for which the specified Box file was not found. |
| representation.not.found | FlowFiles for which the specified Box file’s requested representation was not found. |
| success | FlowFiles that are successfully processed will be routed to this relationship. |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| box.id | The ID of the Box file. |
| box.file.name | The name of the Box file. |
| box.file.size | The size of the Box file in bytes. |
| box.file.created.time | The timestamp when the file was created. |
| box.file.modified.time | The timestamp when the file was last modified. |
| box.file.mime.type | The MIME type of the file. |
| box.file.representation.type | The representation type that was fetched. |
| box.error.message | The error message returned by Box if the operation fails. |
| box.error.code | The error code returned by Box if the operation fails. |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.box.FetchBoxFile](fetchboxfile)
* [org.apache.nifi.processors.box.ListBoxFile](listboxfile)
