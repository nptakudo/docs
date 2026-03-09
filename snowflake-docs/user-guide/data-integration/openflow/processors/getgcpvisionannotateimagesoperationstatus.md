---
title: "GetGcpVisionAnnotateImagesOperationStatus 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/getgcpvisionannotateimagesoperationstatus"
---

# GetGcpVisionAnnotateImagesOperationStatus 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-gcp-nar

## Description

Retrieves the current status of an Google Vision operation.

## Tags

Cloud, Google, Machine Learning, Vision

## Input Requirement

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| gcp-credentials-provider-service | The Controller Service used to obtain Google Cloud Platform credentials. |
| operationKey | The unique identifier of the Vision operation. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | FlowFiles are routed to failure relationship |
| original | Upon successful completion, the original FlowFile will be routed to this relationship. |
| running | The job is currently still being processed |
| success | FlowFiles are routed to success relationship |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.gcp.vision.StartGcpVisionAnnotateImagesOperation](startgcpvisionannotateimagesoperation)
