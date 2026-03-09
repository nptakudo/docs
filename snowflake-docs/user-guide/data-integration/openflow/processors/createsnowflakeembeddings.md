---
title: "CreateSnowflakeEmbeddings 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/createsnowflakeembeddings"
---

# CreateSnowflakeEmbeddings 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

com.snowflake.openflow.runtime | runtime-snowflake-processors-nar

## Description

Create vector embeddings using Snowflake Cortex Large Language Model functions

## Tags

chatbot, embeddings, gen ai, generative ai, llm, nlp, openflow, snowflake, text

## Input Requirement

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Dimensions | The number of dimensions to request the resulting output embeddings have. |
| Embeddings Model | The model to use for embeddings |
| Record Writer | The Record Writer to use for writing the output |
| Snowflake Connection Service | Database Connection Service for accessing Snowflake |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | The original FlowFile will be routed to this relationship if the embeddings could not be created |
| success | The embeddings will be routed to this relationship |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| record.count | The number of records written to the output |
| mime.type | The MIME type of the output data, based on the chosen Record Writer |

See moreShow less

Expand
