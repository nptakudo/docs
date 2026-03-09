---
title: "Snowflake Data Clean Room tutorials, samples, and videos"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/tutorials-and-samples"
---

# Snowflake Data Clean Room tutorials, samples, and videos

Feature — Generally Available

Currently available in [these regions](web-app-introduction.html#label-web-app-hosting).

Not available in government and VPS deployments.

## Tutorials

Here are tutorials to try out using Snowflake Data Clean Rooms when you’re just getting started:

* [Basic UI tutorial, single account](tutorials/cleanroom-web-app-single-account-tutorial): Demonstrates a
  simple overlap analysis and consumer activation, using a single Snowflake account. Single account testing supports most, but not all
  clean room features. To test the full functionality of a clean room, must use multiple Snowflake accounts.
* [Basic UI tutorial, two accounts](tutorials/cleanroom-web-app-tutorial): Demonstrates a simple overlap
  analysis and provider activation using two Snowflake accounts.
* [Basic API tutorial, single account](tutorials/cleanroom-api-tutorial-basic): Demonstrates using the API to
  create and run a custom template using a single Snowflake account.

## Sample notebooks and worksheets

Many of the use case topics include full running samples of Snowflake Data Clean Rooms as downloadable notebooks or worksheets. You
need a Snowflake account with the clean rooms API environment installed to run any of these samples, and you must be able to use the
SAMOOHA\_APP\_ROLE role.

Tip

* **To upload a notebook,** follow the [instructions for uploading a notebook](../ui-snowsight/notebooks-create.html#label-snowsight-import-notebook-file).
* **To upload a worksheet:**

  1. Open the workspaces panel: In the navigation menu, select Projects » Workspaces.
  2. Upload the SQL worksheet: In the workspace menu, select + Add new » Upload Files.

### List of sample files

* **Internal testing clean room:** Jupyter notebook demonstrating how to use a single account to act as both provider and consumer for
  testing purposes.

  + [`Download the notebook`](../../_downloads/980474433a279b8dd7a9409b77b0f54d/internal-testing-cleanroom.ipynb)
* **Consumer-run analysis:** Code for running a basic consumer analysis clean room using separate provider and consumer accounts.

  + [`Download the consumer worksheet`](../../_downloads/d898d27c6c1b81d0b16575285b2e0873/c-run-analysis-c.sql)
  + [`Download the provider worksheet`](../../_downloads/74f5e256a72d109f3bf5b741432911cd/c-run-analysis-p.sql)
* **Provider-run analysis:** Jupyter notebook showing how a provider can run an analysis in a clean room.

  + [`Download the notebook`](../../_downloads/6f09db32770533d503e9578e38467b8f/provider-analysis-notebook.ipynb)
* **Consumer-run consumer activation:** Code for activating analysis results to the consumer’s own Snowflake account, with setup and
  activation for both consumer and provider.

  + [`Download the consumer worksheet`](../../_downloads/edfeb20ec5896fb323528481c1ea3490/c-run-c-activation-c.sql)
  + [`Download the provider worksheet`](../../_downloads/3cdbbfc219b944cb8d7cb49014a520e4/c-run-c-activation-p.sql)
* **Consumer-run provider activation:** Code for activating analysis results to the provider’s Snowflake account, with setup and activation
  for both consumer and provider.

  + [`Download the consumer worksheet`](../../_downloads/76e14e2219bbbe735da2790398954b80/c-run-p-activation-c.sql)
  + [`Download the provider worksheet`](../../_downloads/249d42fdba29da93cd25d75850a016a9/c-run-p-activation-p.sql)
* **Provider-run provider activation:** Code for provider-run analysis with provider activation.

  + [`Download the consumer worksheet`](../../_downloads/4430a12585695047da96b61a06593dd6/p-run-p-activation-c.sql)
  + [`Download the provider worksheet`](../../_downloads/0ff6c0608e468a18e039163d4953ee52/p-run-p-activation-p.sql)
* **Consumer-defined templates:** Code for creating, submitting, and managing consumer-written templates in a clean room.

  + [`Download the consumer worksheet`](../../_downloads/56922f46a21ef92d28a78e521f593230/consumer-template-c.sql)
  + [`Download the provider worksheet`](../../_downloads/333d148177d16d9faaf78198f0f6cc21/consumer-template-p.sql)
* **Provider-defined templates:** Code for creating, managing, and using provider-created templates in a clean room.

  + [`Download the consumer worksheet`](../../_downloads/0773544e11ffd9a39d5fdf82dada99de/provider-template-c.sql)
  + [`Download the provider worksheet`](../../_downloads/80e34f3c8c5c6c2e38ea4e078375f3d8/provider-template-p.sql)
* **Consumer-written UDFs:** Code for uploading and using custom Python functions in a clean room.

  + [`Download the consumer worksheet`](../../_downloads/52519c3df63da0cbe3838f0878fbaec3/consumer-udf-c.sql)
  + [`Download the provider worksheet`](../../_downloads/9e9507050ea9767daf56d8c94a892579/consumer-udf-p.sql)
* **Provider-written UDFs:** Code for uploading and using provider-uploaded custom Python functions in a clean room.

  + [`Download the consumer worksheet`](../../_downloads/f9606ce3e3ad2dbd62a3fe9735894869/provider-udf-c.sql)
  + [`Download the provider worksheet`](../../_downloads/d5c64053435e55dc171af58a492f947f/provider-udf-p.sql)
  + [`Bulk UDF uploading example (single-account worksheet)`](../../_downloads/e3c5e0dab78085f95d314b4ce2e04c4e/upload-multiple-python-packages.sql)
* **UDF from stage:** Jupyter notebook demonstrating how to load user-defined functions from a Snowflake stage.

  + [`Download the notebook`](../../_downloads/c9458d589eac4e4354d19501fa9f1707/udf_from_stage.ipynb)
* **Snowpark UDFs:** Code for creating and using Snowpark-based user-defined functions in clean rooms.

  + [`Download the consumer worksheet`](../../_downloads/26b752753607b54bbd64faa7c688d52e/snowpark-udf-consumer.sql)
  + [`Download the provider worksheet`](../../_downloads/ef9713d25f1026f7271faa3e2f571a1f/snowpark-udf-provider.sql)
* **Consumer-written UDF run by the provider:** A UDF uploaded by the consumer can be run by the provider.

  + [`Download the consumer worksheet`](../../_downloads/7879eba9c233607e8d74f47e44e4997a/p-run-c-uploaded-code-c.sql)
  + [`Download the provider worksheet`](../../_downloads/e81d3235044a7367014ec0680eab0ddc/p-run-c-uploaded-code-p.sql)
* **Snowpark Container Services Integration:** Jupyter notebooks for integrating Snowpark Container Services in clean rooms.

  + [`Consumer notebook`](../../_downloads/c246bff46d86438d655c7a23e1afbc67/spcs-consumer.ipynb)
  + [`Provider notebook`](../../_downloads/109fc1643160d035866a43189b9565d0/spcs-provider.ipynb)
  + [`Spec and config files`](../../_downloads/36c3670d9a0df2bb0358fac7e0d45255/spcs-spec-and-config.zip)
* **Audience Overlap & Segmentation:** Jupyter notebook demonstrating the Audience Overlap & Segmentation template.

  + [`Download the notebook`](../../_downloads/44b3c72a8168d977419f51da25ef51d6/overlap-segmentation.ipynb)

## Sample templates

Snowflake Data Clean Rooms provides a few sample templates that you can download as Snowflake worksheets and implement or customize using the clean rooms API:

Inventory forecasting template:
:   This template helps publishers and advertisers forecast ad inventory availability within a secure data clean room. [Learn more and download the worksheet.](inventory-forecasting-template)

Last touch attribution template:
:   This template provides a comprehensive Last Touch Attribution analysis that allows businesses to measure the effectiveness of their marketing channels. [Learn more and download the worksheet.](last-touch-template)

Audience lookalike modeling template:
:   This template empowers you to discover and target new, high-value customers who mirror your most profitable existing ones. [Learn more and download the worksheet.](lookalike-audience-modeling-template)

## Videos

Our solutions engineers have created the following videos to demonstrate clean room usage. Watch them individually, or [subscribe to our playlist](https://www.youtube.com/playlist?list=PLavJpcg8cl1HrorP5u5VkoywZo5YMewxC).

[Native App Installation](https://youtu.be/FC4Ug95vepM?si=3TnuZDhOhl02V3LD):
:   How to install the Snowflake Data Clean Room environment in your account.

[Freeform SQL](https://youtu.be/847XBdAiam8?si=a9bAEmi8l566Qlbt):
:   How to make free-form SQL queries in Snowflake Data Clean Rooms.

[Editing A Clean Room](https://youtu.be/xMXrSiPBjrU?si=hBUO_1pi4d2hWyDr):
:   How to configure a clean room in the API or UI.

[Cross-Cloud Auto-Fulfillment](https://youtu.be/8BO2GwlZpJQ?si=mLQsLlq_GoAIS496):
:   How to enable Cross-Cloud Auto-Fulfillment in your clean rooms.
