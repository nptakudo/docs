---
title: "StartAwsTextractJob 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/startawstextractjob"
---

# StartAwsTextractJob 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-aws-nar

## Description

Trigger a AWS Textract job. It should be followed by GetAwsTextractJobStatus processor in order to monitor job status.

## Tags

AWS, Amazon, ML, Machine Learning, Textract

## Input Requirement

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| AWS Credentials Provider service | The Controller Service that is used to obtain AWS credentials provider |
| Communications Timeout |  |
| Endpoint Override URL | Endpoint URL to use instead of the AWS default including scheme, host, port, and path. The AWS libraries select an endpoint URL based on the AWS region, but this property overrides the selected endpoint URL, allowing use with other S3-compatible endpoints. |
| JSON Payload | JSON request for AWS Machine Learning services. The Processor will use FlowFile content for the request when this property is not specified. |
| Region |  |
| SSL Context Service | Specifies an optional SSL Context Service that, if provided, will be used to create connections |
| Textract Type | Supported values: “Document Analysis”, “Document Text Detection”, “Expense Analysis” |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | FlowFiles are routed to failure relationship |
| original | Upon successful completion, the original FlowFile will be routed to this relationship. |
| success | FlowFiles are routed to success relationship |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| awsTaskId | The task ID that can be used to poll for Job completion in GetAwsTextractJobStatus |
| awsTextractType | The selected Textract type, which can be used in GetAwsTextractJobStatus |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.aws.ml.textract.GetAwsTextractJobStatus](getawstextractjobstatus)
