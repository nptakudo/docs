---
title: "Introduction to cost anomalies"
url: "https://docs.snowflake.com/en/user-guide/cost-anomalies"
---

# Introduction to cost anomalies

A cost anomaly occurs when daily consumption is above or below the expected range of consumption for the day. Snowflake uses an algorithm to
automatically detect these cost anomalies based on prior levels of consumption, which simplifies the process of identifying spikes or dips
in costs so you can find ways to optimize your spend. Snowflake also provides tools to investigate these cost anomalies to identify root
causes.

Note

The algorithm that detects cost anomalies requires at least 30 days of consumption before it can identify anomalies. If your consumption
in the last seven days was less than 10 credits, Snowflake does not identify changes as an anomaly.

## Account-level vs. organization-level cost anomalies

An account-level cost anomaly occurs when the consumption in a single account falls outside the expected range of consumption for that
account.

An organization-level cost anomaly occurs when the consumption in the entire organization falls outside the expected range of consumption
for the organization. It is based on the aggregate consumption of all accounts in the organization. For example, if there is a significant
consumption spike in one account, but a dip in another, the two might offset each other such that it is not flagged as an organization-level
anomaly. To help investigate organization-level anomalies, Snowflake provides tools to identify which accounts had the biggest increase or
decrease in consumption on a specific day.

To identify and investigate organization-level cost anomalies, you need to be signed in to the
[organization account](organization-accounts) or an [ORGADMIN-enabled account](organization-administrators.html#label-enabling-orgadmin-role-for-account).

## Get started

To identify and investigate cost anomalies using a user interface:

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in) as a user with the [required privileges](cost-anomalies-access-control).
2. In the navigation menu, select Admin » Cost management, and then select Anomalies.

## Unit of measure for cost data

Cost data can be shown with credits as the unit of measure or with a currency as the unit of measure. The unit of measure is a currency in
the following situations:

* If you use the ACCOUNTADMIN or GLOBALORGADMIN system role to work with cost anomalies, cost data displays in a currency if you
  are signed in to the [organization account](organization-accounts) or one that has the
  [ORGADMIN role enabled](organization-administrators.html#label-enabling-orgadmin-role-for-account).
* If you are not a system administrator, cost data displays in a currency if you are granted the ORGANIZATION\_BILLING\_VIEWER
  application role or APP\_ORGANIZATION\_BILLING\_VIEWER application role. For more information about these application roles, see
  [Access control for cost anomalies](cost-anomalies-access-control).

## Run queries against cost anomaly views

You can run queries against views in the ACCOUNT\_USAGE and ORGANIZATION\_USAGE schemas to return historical data about account-level cost
anomalies. Each row in the view includes the consumption on a specific day, and whether that consumption was a cost anomaly.

Cost anomalies for current account
:   Execute queries against the [ANOMALIES\_DAILY view](../sql-reference/account-usage/anomalies_daily) in the
    [ACCOUNT\_USAGE schema](../sql-reference/account-usage) to gain insights into whether cost anomalies occurred in the current account.

    This view uses credits as the unit of measure for consumption.

Cost anomalies for all accounts in an organization
:   Execute queries against the [ANOMALIES\_IN\_CURRENCY\_DAILY view](../sql-reference/organization-usage/anomalies_in_currency_daily) in the
    [ORGANIZATION\_USAGE schema](../sql-reference/organization-usage) to gain insights into whether cost anomalies occurred in accounts in
    the organization. Note that not all accounts have access to the ORGANIZATION\_USAGE schema.

    Use this view to see currency as the unit of measure rather than credits.

## Learn more

For information about how to work with cost anomalies, see the following:

* [Use Snowsight to work with cost anomalies](cost-anomalies-ui)
* [Programmatically work with cost anomalies](cost-anomalies-class)
