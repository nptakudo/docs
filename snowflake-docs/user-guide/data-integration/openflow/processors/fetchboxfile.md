---
title: "FetchBoxFile 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/fetchboxfile"
---

# FetchBoxFile 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-box-nar

## Description

Fetches files from a Box Folder. Designed to be used in tandem with ListBoxFile.

## Tags

box, fetch, storage

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Box Client Service | Controller Service used to obtain a Box API connection. |
| File ID | The ID of the File to fetch |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | A FlowFile will be routed here for each File for which fetch was attempted but failed. |
| success | A FlowFile will be routed here for each successfully fetched File. |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| box.id | The id of the file |
| filename | The name of the file |
| path | The folder path where the file is located |
| box.size | The size of the file |
| box.timestamp | The last modified time of the file |
| error.code | The error code returned by Box |
| error.message | The error message returned by Box |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.box.ListBoxFile](listboxfile)
* [org.apache.nifi.processors.box.PutBoxFile](putboxfile)
