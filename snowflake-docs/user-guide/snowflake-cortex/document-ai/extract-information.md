---
title: "Extract information with Document AI"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/document-ai/extract-information"
---

# Extract information with Document AI

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Deprecated Feature

Document AI and the [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict) method are deprecated. For more information, see [Document AI decommission (Pending)](../../../release-notes/bcr-bundles/un-bundled/bcr-2156).

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Feature — Generally Available

Available to accounts in [AWS, Microsoft Azure, and Google Cloud commercial regions](../../intro-regions.html#label-na-general-regions), with some exceptions. For more information, see [Document AI availability](limitations.html#label-document-ai-availability).

This topic describes extracting information from documents using Document AI.

If you previously published or trained the Document AI model build, you can now extract information from documents by
running the [extracting query](#label-document-ai-extracting-query) in worksheets.
You can also create [processing pipelines](#label-document-ai-create-processing-pipelines) to continuously process
new documents in a stage.

Note

Document AI has known limitations, including the number and size of documents you can process in a single query.
For more information, see [Known limitations to Document AI](limitations).

## Prerequisites

Successful information extraction requires the following conditions:

* The documents used for information extraction are stored in either an internal or external stage.
  For more information, see [Setting up Document AI](setting-up).
* You are using the database and schema you set up for Document AI. For example:

  CopyExpand

  ```
  USE DATABASE doc_ai_db;
  USE SCHEMA doc_ai_schema;
  ```

  Show lessSee more

  Scroll to top
* You are using an account role that is granted the SNOWFLAKE.DOCUMENT\_INTELLIGENCE\_CREATOR database role.
  For more information, see [Setting up Document AI](setting-up).
* You previously published a Document AI model build or trained a Document AI model.
  For more information, see [Publish a Document AI model build](prepare-model-build.html#label-document-ai-publish-model-build).

## Use the extracting query

An extracting query is a SQL query based on the PREDICT method. For more information,
see [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict).

To extract information from documents, run the extracting query in worksheets. After you publish or train the
Document AI model, you can see the extracting query defined in Snowsight.

To view the extracting query in Snowsight:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » AI Studio.
3. Next to the Document Processing Playground, select Open. In the Document Processing Playground, to access Document AI, select Go to Document AI model builds.
4. Select a warehouse.

   The list of model builds appears.
5. From the list of model builds, select the name of the model build you want to see the query for.
6. To view the Extracting Query, select the Build Details tab.

Important

The [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict) method is deprecated. Snowflake recommends
using the [AI\_EXTRACT](../../../sql-reference/functions/ai_extract-document-ai) function instead. For more information,
see [Document AI decommission (Pending)](../../../release-notes/bcr-bundles/un-bundled/bcr-2156).

## Create document processing pipelines

With Document AI, you can create pipelines that automatically process document files to extract information.
To create a processing pipeline, you need to create both a stream on a stage and a task to continuously process new documents
in the stage.

For more information, see [Tutorial: Create a document processing pipeline with Document AI](tutorials/create-processing-pipelines).
