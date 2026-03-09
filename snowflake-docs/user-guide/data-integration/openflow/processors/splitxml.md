---
title: "SplitXml 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/splitxml"
---

# SplitXml 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-standard-nar

## Description

Splits an XML File into multiple separate FlowFiles, each comprising a child or descendant of the original root element

## Tags

split, xml

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Split Depth | Indicates the XML-nesting depth to start splitting XML fragments. A depth of 1 means split the root ‘s children, whereas a depth of 2 means split the root’s children’s children and so forth. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | If a FlowFile fails processing for any reason (for example, the FlowFile is not valid XML), it will be routed to this relationship |
| original | The original FlowFile that was split into segments. If the FlowFile fails processing, nothing will be sent to this relationship |
| split | All segments of the original FlowFile will be routed to this relationship |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| fragment.identifier | All split FlowFiles produced from the same parent FlowFile will have the same randomly generated UUID added for this attribute |
| fragment.index | A one-up number that indicates the ordering of the split FlowFiles that were created from a single parent FlowFile |
| fragment.count | The number of split FlowFiles generated from the parent FlowFile |
| segment.original.filename | The filename of the parent FlowFile |

See moreShow less

Expand
