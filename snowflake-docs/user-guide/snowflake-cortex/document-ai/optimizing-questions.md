---
title: "Question optimization for extracting information with Document AI"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/document-ai/optimizing-questions"
---

# Question optimization for extracting information with Document AI

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Deprecated Feature

Document AI and the [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict) method are deprecated. For more information, see [Document AI decommission (Pending)](../../../release-notes/bcr-bundles/un-bundled/bcr-2156).

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Feature — Generally Available

Available to accounts in [AWS, Microsoft Azure, and Google Cloud commercial regions](../../intro-regions.html#label-na-general-regions), with some exceptions. For more information, see [Document AI availability](limitations.html#label-document-ai-availability).

This topic describes optimizing questions for the Document AI model to provide correct responses.

When you work with Document AI, use natural language to ask the model questions about your documents. To ask a question that
returns an accurate answer, follow these guidelines:

* Use plain English.
* For each question, know what answers you expect.
* Be specific; for example, if the document includes several dates (such as issuing date and signature date), do not ask
  “What is the date?” without including more details.
* Ask for a single value in each question.
* Do not expect Document AI to guess your intentions or have extended knowledge in a specific domain.

Consider the following document as an example. This purchase and sale agreement includes information such as the offer expiration date,
the names of the buyers and the seller, and the included items.

![Example document (purchase and sale agreement).](../../../_images/document-ai-sample-doc.png)

The following table provides examples of questions you can ask the Document AI model and the expected answers.

| Example question | Answer |
| --- | --- |
| What is the date of this agreement? | `'October 6, 2023'` |
| Who is the buyer of the condo? | `'John Davis', 'Jane Davis'` |
| What home appliances are included with the unit? | `'stove/range', 'refrigerator', 'washer', 'dishwasher', 'attached television(s)', 'microwave'` |
| What items are not included with the flat? | `'dryer', 'security system', 'satellite dish', 'wood stove', 'fireplace insert', 'hot tub', 'attached speaker(s)', 'generator'` |
| Is there a dryer in the flat? | `'No'` |
| What addenda are attached to this purchase and sale agreement? | `'22A (Financing)', '2AA (Appraisal)', '22FSBO (Owner Sale)'` |
| What is the seller’s fax number? | None |
| Is the buyer’s signature present on the form? | `'No'` |
| What is the MLS number? | `'59844680'` |
| What is the property’s address? | `'604 Bishop Crossing Land, Fort Lauderdale, Broward County, FL, 33338'` |

See moreShow less

Expand
