---
title: "Last touch attribution"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/last-touch-template"
---

# Last touch attribution

Feature — Generally Available

Currently available in [these regions](web-app-introduction.html#label-web-app-hosting).

Not available in government and VPS deployments.

## About the template

The last touch attribution template provides a comprehensive last touch
attribution analysis that allows businesses to measure the effectiveness of their marketing channels. By securely joining consumer and
provider datasets in a Snowflake Data Clean Room, the analysis identifies the sequence of marketing touch points leading to a conversion.

The process involves joining consumer click data with provider transaction data, ranking each touch point by time, and then attributing the
conversion to the most recent interaction. The final output aggregates key metrics like total conversions and conversion value by channel.
This helps businesses understand which channels are most effective at driving immediate conversions, enabling data-driven decisions for
optimizing marketing strategies and budget allocation.

This analysis attributes 100% of the conversion credit to the last marketing touch point a customer interacted with before converting. It
identifies the final click preceding a transaction and assigns the entire value of that conversion to that single channel.

This template activates the result of the consumer analysis to the provider’s account.

## Key use cases

* **Channel performance analysis:** Identify which channels are driving the most conversions and have the highest conversion value.
* **Budget allocation:** Optimize marketing spend by allocating more budget to the channels that are performing well based on last-touch
  attribution.
* **Campaign optimization:** Understand the effectiveness of different campaigns in driving final conversions and optimize them for better
  performance.

## Get the worksheets and template

Download the worksheets and install them in two separate Snowflake accounts in the same organization and the same cloud hosting environment.
These worksheets show how to create and run a clean room with a last touch attribution template that you can use and modify. The template
includes a UI form so you can run the clean room either in code or in the clean rooms UI. The example enables the consumer to run the
analysis, and optionally to activate the results to the provider’s account.
[See instructions to upload a SQL worksheet into your Snowflake account](tutorials-and-samples.html#label-dcr-list-of-sample-notebooks-and-worksheets).

To try out the templates, run the sample data generator first in both your provider and consumer accounts, to generate sample data to use
with the clean room.

* [`Download the Python sample data table generator.`](../../_downloads/a9580b36376aeb160880444db2305279/last-touch-sample-generator.py)
  Run this to generate data that can be used as sample data for the consumer and provider worksheets.
* [`Download the consumer worksheet.`](../../_downloads/34c5ae0ae79ad0eb7505935a2308c66a/last-touch-attribution-c.sql)
* [`Download the provider worksheet.`](../../_downloads/2c4779ab955fb463cfba4b21dab8edd9/last-touch-attribution-p.sql)
