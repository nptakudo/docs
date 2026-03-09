---
title: "PackageFlowFile 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/packageflowfile"
---

# PackageFlowFile 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-standard-nar

## Description

This processor will package FlowFile attributes and content into an output FlowFile that can be exported from NiFi and imported back into NiFi, preserving the original attributes and content.

## Tags

attributes, flowfile, flowfile-stream, flowfile-stream-v3, package

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Maximum Batch Content Size | Maximum combined content size of FlowFiles to package into one output FlowFile. Note, that FlowFiles whose content exceeds this limit are packaged separately. |
| max-batch-size | Maximum number of FlowFiles to package into one output FlowFile. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| original | The FlowFiles that were used to create the package are sent to this relationship |
| success | The packaged FlowFile is sent to this relationship |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| mime.type | The mime.type will be changed to application/flowfile-v3 |

See moreShow less

Expand

## Use Cases Involving Other Components

|  |
| --- |
| Send FlowFile content and attributes from one NiFi instance to another NiFi instance. |
| Export FlowFile content and attributes from NiFi to external storage and reimport. |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.standard.MergeContent](mergecontent)
* [org.apache.nifi.processors.standard.UnpackContent](unpackcontent)
