---
title: "Trial accounts"
url: "https://docs.snowflake.com/en/user-guide/admin-trial-account"
---

# Trial accounts

A Snowflake trial account lets you evaluate/test Snowflake’s full range of innovative and powerful features with no cost or contractual obligations. To sign
up for a trial account, all you need is a valid email address; no payment information or other qualifying information is required.

## Signing up for a trial account

You can sign up for a free trial using the [self-service form](https://signup.snowflake.com/) (on the Snowflake website).

When you sign up for a trial account, you select your [cloud platform](intro-cloud-platforms), [region](intro-regions),
and [Snowflake Edition](intro-editions). These selections can affect how quickly you exhaust your free usage balance. For example, some features available in the Enterprise Edition consume additional [credits](cost-understanding-compute.html#label-what-are-credits).

The balance of your free usage decreases as you consume credits to use [compute resources](cost-understanding-compute) and accrue costs associated with [storage](cost-understanding-data-storage). You can [track your remaining balance](#label-trial-account-track-balance) at any time.

The trial continues for 30 days (from the sign-up date) or until you’ve depleted your free usage balance, whichever occurs first. At any time during the trial, you can [cancel the trial](#label-trial-account-cancel) or [convert the account to a paid account](#label-trial-account-convert).

At the end of the trial, the account is suspended. You can still log into a suspended account, but you cannot use any features, such as running a virtual warehouse,
loading data, or performing queries.

To reactivate a suspended trial account, you must enter a credit card, which converts it to a paid account.

## Using compute resources

[Virtual warehouses](warehouses) provide the compute power to [load data](../guides-overview-loading-data) and
[perform queries](../guides-overview-queries). These warehouses consume credits, which reduces your free usage balance. To begin, simply start a warehouse; any credits consumed by the warehouse will be deducted from your balance. If your credit consumption fully depletes your free usage balance, you must add a credit card to the account to continue using Snowflake.

Free credits are only consumed by the virtual warehouses you create in your account, and only when they are running.

Tip

To prevent unintentional usage of your free credits:

* Verify the size of your virtual warehouses before you start/resume them. The larger the warehouse, the more credits it consumes while running.
  In many situations, Small or Medium size warehouses are sufficient for evaluating Snowflake’s loading and querying capabilities.
* Do not disable [auto-suspend](warehouses-overview.html#label-auto-suspension-and-auto-resumption) when creating a warehouse. Choosing a short auto-suspend time period (e.g. 5 minutes or less) can reduce credit consumption.

For additional tips on using your trial account:

1. In the left navigation bar, find the tile showing your remaining balance.
2. Select … » Using your trial credits.

## Using storage

As you load data into your trial account, the cost of that storage is subtracted from your free usage balance based on the standard On-Demand cost of a TB in your cloud platform and region. In addition to the cost of storage, loading data also consumes credits as it uses the compute resources of a warehouse.

## Tutorials for trial accounts

The following tutorials are available for trial accounts:

* [Load and query sample data using SQL](tutorials/tasty-bytes-sql-load)
* [Load data from cloud storage: Amazon S3](tutorials/load-from-cloud-tutorial)
* [Load data from cloud storage: Microsoft Azure](tutorials/load-from-cloud-tutorial-azure)
* [Load data from cloud storage: Google Cloud Storage](tutorials/load-from-cloud-tutorial-gcs)
* [Create users and grant roles](tutorials/users-and-roles-tutorial)

## Tracking your remaining balance

Users with the ACCOUNTADMIN role can track the remaining balance of their trial using a tile in the left navigation bar of
Snowsight.

From this tile you can also:

* Select Upgrade to [convert the trial account to a paid account](#label-trial-account-convert).
* Select … » see organization usage details to access the Usage page, which allows you to drill down into your credit
  consumption and storage costs.
* Select the … button to access resources that help you get the most out of your trial account.

## Converting to a paid account

You can add a credit card to a trial account at any time to convert it to a paid account.

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. Do one of the following:

   * Select Upgrade in the left navigation.
   * In the navigation menu, select Admin » Billing.
   * Click the Snowflake billing tab.
3. In the Payment method pane, click + Credit Card.
4. Enter the required information, and then select Add Card.

Adding a payment method converts your trial account to a self-service account. Snowflake knows trial account subscribers who convert to paid
accounts as on-demand, self-service (ODSS) customers. ODSS customers can edit the billing contact information for a self-service account using
Snowsight. For more information, see [Update billing contact information](billing-contacts).

Note that you can also change the credit card for a trial account, at any time, using the same interface in which you added the card.

Note

Adding a credit card to a trial account converts it to a paid account without ending the trial period. During the remainder of the trial period, you can continue using your free credits and storage until the balance is exhausted, after which all additional credit consumption and storage costs will be charged.

Unused balances expire when the trial period ends, at which time costs (for consuming credits and storing data) are charged to the credit card on file at the end of each billing cycle (typically monthly).

For pricing details, see the [pricing page](https://www.snowflake.com/pricing/) (on the Snowflake website).

## Canceling a trial account

You can cancel a trial account at any time by contacting [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support)
and requesting the account to be canceled.

Note

Currently, trial accounts cannot be canceled through the web interface. To cancel an account, you must contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

## Current limitations for trial accounts

The following features are not available for trial accounts:

* [External network access](../developer-guide/external-network-access/external-network-access-overview)
* [Hybrid tables](tables-hybrid)
* [Outbound private connectivity](private-connectivity-outbound)
* [Snowflake Openflow](data-integration/openflow/about)
* Using Duo for multi-factor authentication (MFA)
* [Cortex Code CLI](cortex-code/cortex-code-cli) (requires a Cortex Code CLI trial account; sign up [here](https://signup.snowflake.com/cortex-code?utm_source=docs&utm_medium=docs&utm_campaign=-us-en-all&utm_content=-admin-trial-account-current-limitations-for-trial-accounts)).
* Trial accounts without a valid payment method are limited to roughly ten credits of usage per day of
  [Snowflake Cortex AI Functions](snowflake-cortex/aisql).
  To remove this restriction, [convert your trial account to a paid account](#label-trial-account-convert).
