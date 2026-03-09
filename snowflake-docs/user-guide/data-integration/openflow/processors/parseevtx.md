---
title: "ParseEvtx 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/parseevtx"
---

# ParseEvtx 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-evtx-nar

## Description

Parses the contents of a Windows Event Log file (evtx) and writes the resulting XML to the FlowFile

## Tags

event, evtx, file, logs, message, windows

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Granularity | Output flow file for each Record, Chunk, or File encountered in the event log |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| bad chunk | Any bad chunks of records will be transferred to this relationship in their original binary form |
| failure | Any FlowFile that encountered an exception during conversion will be transferred to this relationship with as much parsing as possible done |
| original | The unmodified input FlowFile will be transferred to this relationship |
| success | Any FlowFile that was successfully converted from evtx to XML |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| filename | The output filename |
| mime.type | The output filetype (application/xml for success and failure relationships, original value for bad chunk and original relationships) |

See moreShow less

Expand
