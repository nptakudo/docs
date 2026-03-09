---
title: "ListBoxFileMetadataTemplates 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/listboxfilemetadatatemplates"
---

# ListBoxFileMetadataTemplates 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-box-nar

## Description

Retrieves all metadata templates associated with a Box file.

## Tags

box, metadata, storage, templates

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Box Client Service | Controller Service used to obtain a Box API connection. |
| File ID | The ID of the file for which to fetch metadata. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | A FlowFile will be routed here if there is an error fetching metadata templates from the file. |
| not found | FlowFiles for which the specified Box file was not found will be routed to this relationship. |
| success | A FlowFile containing the metadata template records will be routed to this relationship upon successful processing. |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| box.file.id | The ID of the file from which metadata was fetched |
| record.count | The number of records in the FlowFile |
| mime.type | The MIME Type specified by the Record Writer |
| box.metadata.templates.names | Comma-separated list of template names |
| box.metadata.templates.count | Number of metadata templates found |
| error.code | The error code returned by Box |
| error.message | The error message returned by Box |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.box.FetchBoxFile](fetchboxfile)
* [org.apache.nifi.processors.box.FetchBoxFileInfo](fetchboxfileinfo)
* [org.apache.nifi.processors.box.ListBoxFile](listboxfile)
