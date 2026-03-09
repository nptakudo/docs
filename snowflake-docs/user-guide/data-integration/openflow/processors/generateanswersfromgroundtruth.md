---
title: "GenerateAnswersFromGroundTruth 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/generateanswersfromgroundtruth"
---

# GenerateAnswersFromGroundTruth 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

com.snowflake.openflow.runtime | runtime-rag-evaluation-processors-nar

## Description

Generates synthetic answers for each question in the incoming records using an LLM. The synthetic answers are added to the specified RecordPath within each record. Additionally, the processor tracks the number of answers generated and updates the FlowFile attributes accordingly.

## Tags

ai, answers, generation, llm, nlp, openai, openflow, rag, synthetic

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Answer Record Path | The RecordPath to the synthetically generated answers. |
| Ground Truth Record Path | The RecordPath to the ground truth field in the record. |
| LLM Provider Service | The provider service for sending evaluation prompts to LLM |
| Question Record Path | The RecordPath to the question field in the record. |
| Record Reader | The Record Reader to use for reading the FlowFile. |
| Record Writer | The Record Writer to use for writing the results. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | FlowFiles that cannot be processed are routed to this relationship |
| success | FlowFiles that are successfully processed are routed to this relationship |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| answers.successfully.generated | The total number of successfully synthetic answers generated for the FlowFile. |
| answers.failed.generated | The total number of failed answer generation for the FlowFile. |
| json.parse.failures | Number of JSON parse failures encountered. |

See moreShow less

Expand
