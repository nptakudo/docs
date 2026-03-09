---
title: "SplitContent 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/splitcontent"
---

# SplitContent 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-standard-nar

## Description

Splits incoming FlowFiles by a specified byte sequence

## Tags

binary, content, split

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Byte Sequence | A representation of bytes to look for and upon which to split the source file into separate files |
| Byte Sequence Format | Specifies how the <Byte Sequence> property should be interpreted |
| Byte Sequence Location | If <Keep Byte Sequence> is set to true, specifies whether the byte sequence should be added to the end of the first split or the beginning of the second; if <Keep Byte Sequence> is false, this property is ignored. |
| Keep Byte Sequence | Determines whether or not the Byte Sequence should be included with each Split |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| original | The original file |
| splits | All Splits will be routed to the splits relationship |

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

## See also

* [org.apache.nifi.processors.standard.MergeContent](mergecontent)
