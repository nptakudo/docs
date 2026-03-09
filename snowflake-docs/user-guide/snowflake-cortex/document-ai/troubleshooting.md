---
title: "Troubleshooting Document AI"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/document-ai/troubleshooting"
---

# Troubleshooting Document AI

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Deprecated Feature

Document AI and the [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict) method are deprecated. For more information, see [Document AI decommission (Pending)](../../../release-notes/bcr-bundles/un-bundled/bcr-2156).

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Feature — Generally Available

Available to accounts in [AWS, Microsoft Azure, and Google Cloud commercial regions](../../intro-regions.html#label-na-general-regions), with some exceptions. For more information, see [Document AI availability](limitations.html#label-document-ai-availability).

The following scenarios can help you troubleshoot issues that might occur when working with Document AI.

## Extracting query is not working

For the [extracting query](extract-information.html#label-document-ai-extracting-query) to work, you must store the documents for extraction in either an internal or external stage.
Ensure that you specify the `SNOWFLAKE_SSE` encryption type when you create an internal stage.

|  |  |
| --- | --- |
| Error | Depending on the document format, you might get an error such as one of the following:  Expand  ``` {   "__processingErrors": [     "File extension does not match actual mime type. Mime-Type: application/octet-stream"   ] } ```  Show lessSee more  Scroll to top  Expand  ``` {   "__processingErrors": [     "cannot identify image file <_io.BytesIO object at 0x7f8a800ba020>"   ] } ```  Show lessSee more  Scroll to top |
| Cause | You didn’t specify the `SNOWFLAKE_SSE` encryption type when you created internal stage to store documents. |
| Solution | To create an [internal stage](../../data-load-local-file-system-create-stage), run the [CREATE STAGE](../../../sql-reference/sql/create-stage) command as shown in the following example:  CopyExpand  ``` CREATE STAGE doc_ai_stage   DIRECTORY = (ENABLE = TRUE)   ENCRYPTION = (TYPE = 'SNOWFLAKE_SSE'); ```  Show lessSee more  Scroll to top |

See moreShow less

Expand

## Presigned URL has expired

The presigned URL of the staged documents is a required argument to [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict).
To get the presigned URL, call the [GET\_PRESIGNED\_URL](../../../sql-reference/functions/get_presigned_url) function, which has the default expiration time.

For more information, see [GET\_PRESIGNED\_URL](../../../sql-reference/functions/get_presigned_url).

|  |  |
| --- | --- |
| Error | Expand  ``` { "__processingErrors": [ "Received HTTP 403 response for presigned URL. URL may be expired." ] } ```  Show lessSee more  Scroll to top |
| Cause | Presigned URL has expired. |
| Solution | Either reduce the number of documents in one query, or extend the expiration time. For more information about extending the expiration time, see [GET\_PRESIGNED\_URL](../../../sql-reference/functions/get_presigned_url). |

See moreShow less

Expand

## Too many documents in one query

Document AI has a limitation on the number of documents processed in one [extracting query](extract-information.html#label-document-ai-extracting-query).
For more information, see [Known limitations to Document AI](limitations).

|  |  |
| --- | --- |
| Error | Expand  ``` { "__processingErrors": [ "Query limit reached: too many documents in a single query." ] } ```  Show lessSee more  Scroll to top |
| Cause | You tried to process too many documents in one query. |
| Solution | Use several queries to process the documents. |

See moreShow less

Expand

## Documents don’t meet specific requirements

The documents you process with Document AI must meet specific requirements. For more information, see [Prepare your documents for Document AI](preparing-documents).

|  |  |
| --- | --- |
| Error | You might get one of the following errors:  Expand  ``` { "__processingErrors": [ "Page 0 size is larger than the limit. Actual: 1083 mm x 1384 mm. Maximum: 1200 mm x 1200 mm." ] } ```  Show lessSee more  Scroll to top  Expand  ``` { "__processingErrors": [ "Document has too many pages. Actual: 150. Maximum: 125." ] } ```  Show lessSee more  Scroll to top  Expand  ``` { "__processingErrors": [ "Image size is too small. Actual: 20x20 px. Minimum: 50x50 px." ] } ```  Show lessSee more  Scroll to top  Expand  ``` { "__processingErrors": [ "Unsupported file format. Actual: csv. Supported: docx, eml, htm, html, jpeg, jpg, pdf, png, text, tif, tiff, txt." ] } ```  Show lessSee more  Scroll to top  Expand  ``` { "__processingErrors": [ "File exceeds maximum size. Actual: 54096026 bytes. Maximum: 50000000 bytes." ] } ```  Show lessSee more  Scroll to top |
| Cause | The documents attempted to process don’t meet the requirements of Document AI. For more information about the requirements, see [Prepare your documents for Document AI](preparing-documents). |
| Solution | Prepare your documents to meet the requirements. |

See moreShow less

Expand

## The Document AI model build was not published

To extract information with Document AI, you need to have the Document AI model build published.
You don’t need to publish the model build if you trained the model and didn’t add new data values (ask new questions) after the training.

|  |  |
| --- | --- |
| Error | The error message starts with the following:  Expand  ``` Request failed for external function DOCUMENT_EXTRACT_FEATURES$V1 with remote service error: 422 ```  Show lessSee more  Scroll to top |
| Cause | The Document AI model build was not published. |
| Solution | Publish the Document AI model build. For more information, see [Publish a Document AI model build](prepare-model-build.html#label-document-ai-publish-model-build). |

See moreShow less

Expand

## Required privileges are not granted or the model build name is duplicated

To create a Document AI model build, you must grant the required privileges to your role, and choose a unique model build name.

For more information on required privileges, see [Document AI access control](setting-up.html#label-document-ai-access-control).

|  |  |
| --- | --- |
| Error | Expand  ``` Unable to create a build on the specified database and schema. Please check the documentation to learn more. ```  Show lessSee more  Scroll to top |
| Cause | Possible causes are:   * The CREATE SNOWFLAKE.ML.DOCUMENT\_INTELLIGENCE privilege is not granted to your role. * Your role has not been granted the CREATE MODEL privilege on the schema that uses the model. * The model build name already exists in the database and schema. |
| Solution | * Grant the CREATE SNOWFLAKE.ML.DOCUMENT\_INTELLIGENCE privilege to your role. See [Grant the required roles and privileges to Document AI users](setting-up.html#label-document-ai-grant-roles). * Grant the CREATE MODEL privilege on the schema that uses the model. See [Grant the required roles and privileges to Document AI users](setting-up.html#label-document-ai-grant-roles). * Use a unique model build name within the database and schema. |

See moreShow less

Expand
