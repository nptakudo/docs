---
title: "Cost governance of Document AI"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/document-ai/cost-governance"
---

# Cost governance of Document AI

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Deprecated Feature

Document AI and the [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict) method are deprecated. For more information, see [Document AI decommission (Pending)](../../../release-notes/bcr-bundles/un-bundled/bcr-2156).

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Feature — Generally Available

Available to accounts in [AWS, Microsoft Azure, and Google Cloud commercial regions](../../intro-regions.html#label-na-general-regions), with some exceptions. For more information, see [Document AI availability](limitations.html#label-document-ai-availability).

This topic provides best practices for cost governance of Document AI.

## Understanding cost for Document AI

Document AI incurs cost in the following ways:

AI Services compute:
:   Document AI enables extracting information from documents using the [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict)
    method, which incurs compute cost.

Virtual warehouse compute:
:   To run queries in worksheets (including using the [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict) method),
    you select a warehouse. Additionally, Document AI can incur costs for other operations related to retrieving data
    in the worksheets. For information about selecting warehouse size for Document AI,
    see [Determining optimal warehouse size for Document AI](#label-document-ai-determining-optimal-warehouse-size).

Storage:
:   To test the Document AI model, you upload the documents to the Document AI user interface in Snowsight, where you
    review the results and optionally fine-tune the model by training. These operations might incur storage costs because the results
    are stored within a Snowflake class object in your account. To extract information by using SQL, you upload the documents
    to either an internal or external stage, which might also incur storage costs. For information about viewing incurred storage costs,
    see [Exploring storage cost](../../cost-exploring-data-storage).

For more information about overall cost at Snowflake, see [Understanding overall cost](../../cost-understanding-overall).

### AI Services compute cost

Document AI uses Snowflake-managed compute resources, which are automatically scaled up or down by Snowflake as required for
each Document AI workload. With the Snowflake-managed compute structure, the consumption is based on the time spent actually
using these resources. In contrast, user-managed virtual warehouses consume credits while running, regardless of whether they are
performing any work, so they might be idle or overused.

The calculation of credit consumption for Document AI is based on the total amount of compute resources used to complete the job.
The amount of compute resources used is measured by the type of compute and the time spent and calculated on a per-second basis
across all resources used by the workload, rounded up to the nearest whole second.

For more information about compute cost at Snowflake, see [Understanding compute cost](../../cost-understanding-compute).

For more information about credit consumption, see
the [Snowflake Service Consumption Table](https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf).

#### Estimating credit consumption

Credit consumption for Document AI depends on the following:

* Number of pages (for document formats consisting of pages)
* Number of documents
* Page density

  Processing a document that is mostly blank (for example, an invoice) takes less time than a text-heavy document (for example,
  a research paper), which reduces credit consumption.
* Number of data values to be extracted

  Extracting more data values takes more time, which increases credit consumption.

The following table describes the *estimated* credit consumption for 1,000 pages based on different types of workloads:

| Number of documents | Number of pages per document | Page density | Estimated credit range for 10 values | Estimated credit range for 20 values | Estimated credit range for 40 values |
| --- | --- | --- | --- | --- | --- |
| 10 | 100 | Low; for example, an invoice or a slide | From 2 to 5 | From 3 to 7 | From 6 to 20 |
| 100 | 10 | Low; for example, an invoice or a slide | From 4 to 7 | From 6 to 11 | From 10 to 24 |
| 1,000 | 1 | Low; for example, an invoice or a slide | From 9 to 12 | From 10 to 14 | From 12 to 26 |
| 10 | 100 | Medium; for example, business correspondence or financial statements | From 3 to 6 | From 6 to 10 | From 12 to 26 |
| 100 | 10 | Medium; for example, business correspondence or financial statements | From 6 to 9 | From 9 to 12 | From 16 to 30 |
| 1,000 | 1 | Medium; for example, business correspondence or financial statements | From 9 to 12 | From 11 to 15 | From 15 to 29 |
| 10 | 100 | High; for example, a research paper or a legal document | From 4 to 7 | From 8 to 12 | From 16 to 30 |
| 100 | 10 | High; for example, a research paper or a legal document | From 7 to 10 | From 11 to 15 | From 21 to 35 |
| 1,000 | 1 | High; for example, a research paper or a legal document | From 10 to 13 | From 12 to 16 | From 17 to 31 |

See moreShow less

Expand

Consider the following example:

* You process 30,000 purchase order documents per year.
* Each document has 10 pages on average, which means the total page counts is 300,000.
* As these are purchase orders, you assess that each page is of medium density.
* You want to extract 10 values from each purchase order.

In this case, the estimated credit range per 1,000 pages is 6 to 9, resulting in 1,800 to 2,700 credits for 30,000 documents (300,000 pages) per year.

##### Estimating credit consumption for table extraction

The following table describes the *estimated* credit consumption for table extraction
(in preview) for 1,000 documents, depending on the number of pages per document and the number of cells in a table:

| Number of pages per document | Estimated credit range for tiny table (< 10 cells) | Estimated credit range for small table (11-25 cells) | Estimated credit range for medium table (26-50 cells) | Estimated credit range for large table (51-400 cells) |
| --- | --- | --- | --- | --- |
| 1 | From 3 to 29 | From 11 to 34 | From 19 to 53 |  |
| 2-10 | From 5 to 37 | From 14 to 52 | From 24 to 86 | From 37 to 369 |
| 11-100 | From 16 to 172 | From 30 to 172 | From 36 to 367 | From 63 to 601 |

See moreShow less

Expand

For more information about credit consumption, see the [Snowflake Service Consumption Table](https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf).

## Monitoring cost of Document AI

Usage of Document AI appears in the [METERING\_DAILY\_HISTORY view](../../../sql-reference/organization-usage/metering_daily_history) in the ORGANIZATION\_USAGE schema
with a service type of AI\_SERVICES.

To view credit consumption for AI services for all accounts in an organization, use the following query:

CopyExpand

```
SELECT * FROM SNOWFLAKE.ORGANIZATION_USAGE.METERING_DAILY_HISTORY
  WHERE service_type ILIKE '%ai_services%';
```

Show lessSee more

Scroll to top

Note

The SNOWFLAKE.ORGANIZATION\_USAGE.METERING\_DAILY\_HISTORY view can take up to four hours to update.

To view usage data for Document AI for your account, including credits used, use the
[DOCUMENT\_AI\_USAGE\_HISTORY view](../../../sql-reference/account-usage/document_ai_usage_history).

## Determining optimal warehouse size for Document AI

Snowflake recommends using an X-Small, Small, or Medium warehouse. Scaling up the warehouse does not increase
the speed of query processing, but might result in unnecessary costs.

Consider scaling up the warehouse if you perform additional operations that require warehouse resources.
