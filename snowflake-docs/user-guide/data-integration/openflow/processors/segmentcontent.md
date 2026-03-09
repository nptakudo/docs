---
title: "SegmentContent 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/segmentcontent"
---

# SegmentContent 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-standard-nar

## Description

Segments a FlowFile into multiple smaller segments on byte boundaries. Each segment is given the following attributes: fragment.identifier, fragment.index, fragment.count, segment.original.filename; these attributes can then be used by the MergeContent processor in order to reconstitute the original FlowFile

## Tags

segment, split

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Segment Size | The maximum data size in bytes for each segment |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| original | The original FlowFile will be sent to this relationship |
| segments | All segments will be sent to this relationship. If the file was small enough that it was not segmented, a copy of the original is sent to this relationship as well as original |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| fragment.identifier | All segments produced from the same parent FlowFile will have the same randomly generated UUID added for this attribute |
| fragment.index | A one-up number that indicates the ordering of the segments that were created from a single parent FlowFile |
| fragment.count | The number of segments generated from the parent FlowFile |
| segment.original.filename | The filename of the parent FlowFile |
| segment.original.filename | The filename will be updated to include the parent’s filename, the segment index, and the segment count |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.standard.MergeContent](mergecontent)
