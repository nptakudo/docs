---
title: "Prepare your documents for Document AI"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/document-ai/preparing-documents"
---

# Prepare your documents for Document AI

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Deprecated Feature

Document AI and the [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict) method are deprecated. For more information, see [Document AI decommission (Pending)](../../../release-notes/bcr-bundles/un-bundled/bcr-2156).

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Feature — Generally Available

Available to accounts in [AWS, Microsoft Azure, and Google Cloud commercial regions](../../intro-regions.html#label-na-general-regions), with some exceptions. For more information, see [Document AI availability](limitations.html#label-document-ai-availability).

This topic describes how to prepare your documents for use with Document AI.

The documents you process with Document AI must meet the following requirements:

* The documents must be no more than 125 pages long.
* The documents must be in one of the following formats:

  + PDF
  + PNG
  + DOCX
  + EML
  + JPEG, JPG
  + HTM, HTML
  + TEXT, TXT
  + TIF, TIFF
* The documents must be 50 MB or less in size.
* Document pages must have dimensions of 1200 x 1200 mm or less.
* The images must be between 50 x 50 and 10,000 x 10,000 pixels.

To improve the model training process, ensure that the documents you upload to Document AI represent a real use case or scenario
and that the dataset consists of diverse documents in terms of both layout and data.

Ensure that the information in your dataset is varied. If all documents contain the same data (for example, the same gender or ethnicity),
or the information is always presented in the same form (for example, a specific date format), the model might provide incorrect results.
