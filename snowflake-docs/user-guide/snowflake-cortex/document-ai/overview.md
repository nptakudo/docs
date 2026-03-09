---
title: "Document AI"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/document-ai/overview"
---

# Document AI

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Deprecated Feature

Document AI and the [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict) method are deprecated. For more information, see [Document AI decommission (Pending)](../../../release-notes/bcr-bundles/un-bundled/bcr-2156).

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Feature — Generally Available

Available to accounts in [AWS, Microsoft Azure, and Google Cloud commercial regions](../../intro-regions.html#label-na-general-regions), with some exceptions. For more information, see [Document AI availability](limitations.html#label-document-ai-availability).

## What is Document AI

Document AI is a Snowflake AI feature that uses Arctic-TILT, a proprietary large language model (LLM), to extract data from documents.
Document AI processes documents of various formats and extracts information from both text-heavy paragraphs and the content in a
graphical form, such as logos, handwritten text (signatures), tables, or checkmarks. With Document AI, you can prepare pipelines
for continuous processing of new documents of a specific type, such as invoices or finance statements. You can extract information from
entities (in a form of a single value or a list of values), or tables based on the list of specified columns.

Document AI provides both zero-shot extraction and fine-tuning. Zero-shot means that the foundation model can locate
and extract information specific to a document type, even if the model has never seen the document before. This is because the
foundation model is trained on a large volume of various documents, so the model broadly understands the type of document being
processed.

Additionally, you can fine-tune the Snowflake Arctic-TILT model to improve your results by training the model on the documents specific to your
use case. The fine-tuned model (including the training data used) is available only to you and is not shared with other Snowflake customers.

## When to use Document AI

Document AI is best used when:

* You want to turn unstructured data from documents into structured data in tables.
* You want to create pipelines for continuous processing of new documents of a specific type.
* Business users with domain knowledge prepare the model, and the data engineers working with SQL prepare pipelines to automate the
  processing of new documents.

## How Document AI works

Working with Document AI is divided into two phases:

* Preparing a Document AI model build

  You can think of the model build as representing a single type of a document or a use case; for example, a model build for
  extracting information from invoice documents. The Document AI model build includes the model, the data values to be extracted,
  and the documents uploaded to test and train the model.

  You prepare the model build through a Document AI user interface in Snowsight. The interface lets you create a model build,
  upload documents to test and train the model, define data values (information to be extracted) by asking questions using natural
  language, evaluate the model, and publish the model build or fine-tune the model to improve the results.

  For more information, see [Prepare a Document AI model build](prepare-model-build).
* Extracting information from documents

  When the model build is ready, you can begin extracting information from documents by running an extracting query,
  which uses the [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict) method.
  You can then use the extracting query to create pipelines for continuous processing with streams and tasks.

  For more information, see [Extract information with Document AI](extract-information).

  Note

  The documents to be processed using the [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict) method must be stored in an
  internal or external stage.

![Document AI overview](../../../_images/overview1.svg)

To get started with Document AI, see [Tutorial: Create a document processing pipeline with Document AI](tutorials/create-processing-pipelines).

## Document AI model version history

To work with the latest version of the Arctic-TILT model, create a new
Document AI model build.

| Model version release date | Model version improvements |
| --- | --- |
| [May 8, 2025](../../../release-notes/2025/other/2025-05-08-document-ai) | * Checkbox identification |
| [April 16, 2025](../../../release-notes/2025/other/2025-04-16-document-ai) | * Language support for Spanish, French, German, Portuguese, Italian, and Polish * Language-specific diacritics * Overall model quality |
| [February 14, 2025](../../../release-notes/2025/other/2025-02-14-document-ai) | * Checkbox identification * Answers to yes/no questions * Overall model quality |
| [August 6, 2024](../../../release-notes/2024/other/2024-08-06-document-ai) | * Doubling length of the answers provided by the model. * Training time. See [Training time estimation](prepare-model-build.html#label-document-ai-training-time-estimation). |
| [June 21, 2024](../../../release-notes/2024/other/2024-06-21-document-ai) | * Extraction of lists of values * Checkbox identification * Query paraphrasing recognition to improve recognizing queries built as sentences, such as *Give me the date of the agreement* |

See moreShow less

Expand

## Legal notices

The data classification of inputs and outputs are as set forth in the following table.

| Input data classification | Output data classification | Designation |
| --- | --- | --- |
| Usage Data | Customer Data | Generally available functions are Covered AI Features. Preview functions are Preview AI Features. [[1]](#id2) |

See moreShow less

Expand

[[1](#id1)]

Represents the defined term used in the AI Terms and Acceptable Use Policy.

For additional information, refer to [Snowflake AI and ML](../../../guides-overview-ai-features).
