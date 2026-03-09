---
title: "Known limitations to Document AI"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/document-ai/limitations"
---

# Known limitations to Document AI

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Deprecated Feature

Document AI and the [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict) method are deprecated. For more information, see [Document AI decommission (Pending)](../../../release-notes/bcr-bundles/un-bundled/bcr-2156).

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Feature — Generally Available

Available to accounts in [AWS, Microsoft Azure, and Google Cloud commercial regions](../../intro-regions.html#label-na-general-regions), with some exceptions. For more information, see [Document AI availability](#label-document-ai-availability).

This topic describes known limitations to Document AI.

Document AI has the following limitations:

* Document AI supports processing documents in English, Spanish, French, German, Portuguese, Italian, and Polish. The results for other languages might not be satisfactory.
* Document AI supports processing documents of specific formats and sizes.

  For more information, see [Prepare your documents for Document AI](preparing-documents).
* Document AI supports processing a maximum of 1000 documents in one query.
* Document AI does not support multiple users working on the same model build at the same time in Snowsight. For example,
  two users cannot upload documents or review answers within the same model build at the same time.
* Document AI does not support double quotes around identifiers for the database and schema.
* Document AI does not support altering a database or a schema where the model build is located.
* Document AI does not support [serverless tasks](../../tasks-intro.html#label-tasks-compute-resources-serverless).
* Document AI does not currently support renaming models.
* Document AI is available to accounts in AWS, Microsoft Azure, and Google Cloud commercial regions, with some exceptions. For more information, see [Document AI availability](#label-document-ai-availability).
* The Document AI model returns answers that are up to 512 tokens long (about 320 words) per question.

  The model for table extraction returns answers that are up to 2048 tokens long.
* For internal stages, Document AI supports using server-side encryption only.

## Document AI availability

Document AI is currently available in the following regions:

| Cloud platform | Cloud region |
| --- | --- |
| Amazon Web Services (AWS) | * US East (N. Virginia) * US East (Ohio) * US West (Oregon) * Canada (Central) * South America (Sao Paulo) * Europe (London) * EU (Stockholm) * EU (Ireland) * EU (Frankfurt) * Asia Pacific (Mumbai) * Asia Pacific (Tokyo) * Asia Pacific (Seoul) * Asia Pacific (Sydney) * Asia Pacific (Jakarta) |
| Microsoft Azure | * East US 2 (Virginia) * West US 2 (Washington) * South Central US (Texas) * Canada Central (Toronto) * UK South (London) * North Europe (Ireland) * West Europe (Netherlands) * Southeast Asia (Singapore) * UAE North (Dubai) * Australia East (New South Wales) * Central India (Pune) * Japan East (Tokyo) |
| Google Cloud | * US East4 (N. Virginia) * Europe West2 (London) * Europe West3 (Frankfurt) * Europe West4 (Netherlands) |

See moreShow less

Expand

For more information, see [Supported cloud regions](../../intro-regions).

If you want to process documents stored in a cloud platform or region that is not supported by Document AI, create an account in
a supported Snowflake region, and then create an external stage to connect to the cloud storage provider that contains your documents.
For more information, see [Overview of data loading](../../data-load-overview).
