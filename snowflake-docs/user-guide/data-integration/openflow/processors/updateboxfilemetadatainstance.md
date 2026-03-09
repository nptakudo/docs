---
title: "UpdateBoxFileMetadataInstance 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/updateboxfilemetadatainstance"
---

# UpdateBoxFileMetadataInstance 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-box-nar

## Description

Updates metadata template values for a Box file using the record in the given flowFile. This record represents the desired end state of the template after the update. The processor will calculate the necessary changes (add/replace/remove) to transform the current metadata to the desired state. The input record should be a flat key-value object.

## Tags

box, metadata, storage, templates, update

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Box Client Service | Controller Service used to obtain a Box API connection. |
| File ID | The ID of the file for which to update metadata. |
| Record Reader | The Record Reader to use for parsing the incoming data |
| Template Key | The key of the metadata template to update. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | A FlowFile is routed to this relationship if an error occurs during metadata update. |
| file not found | FlowFiles for which the specified Box file was not found will be routed to this relationship. |
| success | A FlowFile is routed to this relationship after metadata has been successfully updated. |
| template not found | FlowFiles for which the specified metadata template was not found will be routed to this relationship. |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| box.id | The ID of the file whose metadata was updated |
| box.template.name | The template name used for metadata update |
| box.template.scope | The template scope used for metadata update |
| error.code | The error code returned by Box |
| error.message | The error message returned by Box |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.box.FetchBoxFile](fetchboxfile)
* [org.apache.nifi.processors.box.ListBoxFile](listboxfile)
* [org.apache.nifi.processors.box.ListBoxFileMetadataTemplates](listboxfilemetadatatemplates)
