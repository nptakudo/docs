---
title: "CreateOpenAiEmbeddings 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/createopenaiembeddings"
---

# CreateOpenAiEmbeddings 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

com.snowflake.openflow.runtime | runtime-openai-nar

## Description

Uses OpenAI to create embeddings for text. The input text can be provided as a single FlowFile or as a record-oriented FlowFile.

## Tags

chatbot, embeddings, gen ai, generative ai, llm, nlp, openai, openflow, text

## Input Requirement

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Dimensions | The number of dimensions to request the resulting output embeddings have. This is only supported in text-embedding-3 and later models. |
| Embeddings Model | The model to use for embeddings |
| Embeddings Record Path | The path to the field in the record where the embeddings are to be written. |
| Max Batch Size | The maximum number of records to include in each batch sent to OpenAI |
| OpenAI API Key | The API Key for authenticating to OpenAI |
| OpenAI Organization | The organization to use for OpenAI |
| Record Reader | The record reader to use for reading record-oriented data. If the incoming data is to be treated as plaintext, this property should be left unset. |
| Record Writer | The Record Writer to use for writing the output |
| Text Record Path | The path to the field in the record that contains the text to be embedded. If the incoming data is to be treated as plaintext, this property should be left unset. |
| User | An identifier for the remote user on whose behalf the request is being made; OpenAI uses this to detect and prevent abuse. |
| Web Client Service | The Web Client Service to use for communicating with OpenAI |

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

## Use cases

|  |
| --- |
| Create embeddings for text using OpenAI’s Embeddings |

See moreShow less

Expand

## See also

* [com.snowflake.openflow.runtime.processors.openai.CreateAzureOpenAiEmbeddings](createazureopenaiembeddings)
* [com.snowflake.openflow.runtime.processors.openai.PromptOpenAI](promptopenai)
