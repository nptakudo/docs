---
title: "Working with Document AI"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/document-ai/working-with-document-ai"
---

# Working with Document AI

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Deprecated Feature

Document AI and the [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict) method are deprecated. For more information, see [Document AI decommission (Pending)](../../../release-notes/bcr-bundles/un-bundled/bcr-2156).

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Feature — Generally Available

Available to accounts in [AWS, Microsoft Azure, and Google Cloud commercial regions](../../intro-regions.html#label-na-general-regions), with some exceptions. For more information, see [Document AI availability](limitations.html#label-document-ai-availability).

This topic provides an overview of working with Document AI.

Note

Before you begin working with Document AI, confirm that a warehouse, database, and schema are prepared
and that the required roles and privileges are granted to the users.

For more information, see [Setting up Document AI](setting-up).

Working with Document AI consists of the following steps:

1. Prepare your documents.

   The documents you process with Document AI must meet certain requirements.
   For more information, see [Prepare your documents for Document AI](preparing-documents).
2. Prepare a Document AI model build in Snowsight.

   Document AI consists of model builds, which include the model, the documents uploaded to test the model, and the data values to be extracted.

   To prepare a Document AI model build:

   1. Upload the documents that will be used to test the model.
   2. Define the data values to be extracted by [asking questions using natural language](optimizing-questions).
   3. Review the results provided by the model.
   4. Decide to either publish the model or fine-tune the model to improve the results.

   For more information about working with model builds, see [Prepare a Document AI model build](prepare-model-build).
3. Extract information in worksheets by using the [extracting query](extract-information.html#label-document-ai-extracting-query).

   Use the extracting query to extract information from staged documents. For more information, see [Extract information with Document AI](extract-information).
