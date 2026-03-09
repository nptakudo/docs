---
title: "DeleteSQS 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/deletesqs"
---

# DeleteSQS 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-aws-nar

## Description

Deletes a message from an Amazon Simple Queuing Service Queue

## Tags

AWS, Amazon, Delete, Queue, SQS

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| AWS Credentials Provider service | The Controller Service that is used to obtain AWS credentials provider |
| Communications Timeout |  |
| Endpoint Override URL | Endpoint URL to use instead of the AWS default including scheme, host, port, and path. The AWS libraries select an endpoint URL based on the AWS region, but this property overrides the selected endpoint URL, allowing use with other S3-compatible endpoints. |
| Queue URL | The URL of the queue delete from |
| Receipt Handle | The identifier that specifies the receipt of the message |
| Region |  |
| SSL Context Service | Specifies an optional SSL Context Service that, if provided, will be used to create connections |
| proxy-configuration-service | Specifies the Proxy Configuration Controller Service to proxy network requests. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | FlowFiles are routed to failure relationship |
| success | FlowFiles are routed to success relationship |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.aws.sqs.GetSQS](getsqs)
* [org.apache.nifi.processors.aws.sqs.PutSQS](putsqs)
