---
title: "GetAwsTranscribeJobStatus 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/getawstranscribejobstatus"
---

# GetAwsTranscribeJobStatus 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-aws-nar

## Description

Retrieves the current status of an AWS Transcribe job.

## Tags

AWS, Amazon, ML, Machine Learning, Transcribe

## Input Requirement

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| AWS Credentials Provider service | The Controller Service that is used to obtain AWS credentials provider |
| AWS Task ID |  |
| Communications Timeout |  |
| Endpoint Override URL | Endpoint URL to use instead of the AWS default including scheme, host, port, and path. The AWS libraries select an endpoint URL based on the AWS region, but this property overrides the selected endpoint URL, allowing use with other S3-compatible endpoints. |
| Region |  |
| SSL Context Service | Specifies an optional SSL Context Service that, if provided, will be used to create connections |
| proxy-configuration-service | Specifies the Proxy Configuration Controller Service to proxy network requests. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | The job failed, the original FlowFile will be routed to this relationship. |
| original | Upon successful completion, the original FlowFile will be routed to this relationship. |
| running | The job is currently still being processed |
| success | Job successfully finished. FlowFile will be routed to this relation. |
| throttled | Retrieving results failed for some reason, but the issue is likely to resolve on its own, such as Provisioned Throughput Exceeded or a Throttling failure. It is generally expected to retry this relationship. |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| outputLocation | S3 path-style output location of the result. |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.aws.ml.transcribe.StartAwsTranscribeJob](startawstranscribejob)
