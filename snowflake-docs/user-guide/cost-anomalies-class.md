---
title: "Programmatically work with cost anomalies"
url: "https://docs.snowflake.com/en/user-guide/cost-anomalies-class"
---

# Programmatically work with cost anomalies

You can use the ANOMALY\_INSIGHTS [class](../sql-reference/snowflake-db-classes) to programmatically identify and investigate cost
anomalies. The fully qualified instance that you use to work with anomalies is SNOWFLAKE.LOCAL.ANOMALY\_INSIGHTS.

You must have the [required privileges](cost-anomalies-access-control) to run the
[class methods](../sql-reference/classes/anomaly_insights.html#label-anomaly-insights-methods).

For an overview of cost anomalies, see [Introduction to cost anomalies](cost-anomalies).

## Identify cost anomalies with ANOMALY\_INSIGHTS

Snowflake creates an instance of the ANOMALY\_INSIGHTS class that you can use to programmatically identify cost anomalies. The
[ANOMALY\_INSIGHTS!GET\_DAILY\_CONSUMPTION\_ANOMALY\_DATA](../sql-reference/classes/anomaly-insights/methods/get_daily_consumption_anomaly_data) method returns consumption data for an account or
organization along with a boolean value that indicates whether that consumption is a cost anomaly.

### Identify organization-level cost anomalies

Users call the GET\_DAILY\_CONSUMPTION\_ANOMALY\_DATA method from the organization account or an ORGADMIN-enabled account to identify
[organization-level cost anomalies](cost-anomalies.html#label-cost-anomaly-level). To focus on organization-level cost anomalies, the user passes NULL as
an argument instead of the name of an account.

Example: Organization-level cost anomaly
:   To identify organization-level cost anomalies between January 1, 2024, and March 31, 2024, do the following:

    1. Sign in to the [organization account](organization-accounts) or an
       [ORGADMIN-enabled account](organization-administrators.html#label-enabling-orgadmin-role-for-account).
    2. Call the method:

       CopyExpand

       ```
       CALL SNOWFLAKE.LOCAL.ANOMALY_INSIGHTS!GET_DAILY_CONSUMPTION_ANOMALY_DATA(
         '2024-01-01', '2024-03-31', NULL);
       ```

       Show lessSee more

       Scroll to top
    3. In the output, find days where the value of the `is_anomaly` column is `TRUE`.

### Identify account-level cost anomalies

You can use the GET\_DAILY\_CONSUMPTION\_ANOMALY\_DATA method to identify account-level cost anomalies for the current account or, if you are
signed in to the organization account or an ORGADMIN-enabled account, any account in the organization.

Example: Cost anomalies in the current account
:   To identify cost anomalies in the current account between January 1, 2024, and March 31, 2024, call the following method when signed in
    to the account.

    CopyExpand

    ```
    CALL SNOWFLAKE.LOCAL.ANOMALY_INSIGHTS!GET_DAILY_CONSUMPTION_ANOMALY_DATA(
      '2024-01-01', '2024-03-31', CURRENT_ACCOUNT_NAME() );
    ```

    Show lessSee more

    Scroll to top

    To use the output to identify the cost anomalies, look for the days where the value of the `is_anomaly` column is `TRUE`.

Example: Cost anomalies in a different account
:   If you are signed in to the organization account or an ORGADMIN-enabled account, and want to identify cost anomalies in a different
    account, specify the name of the account when you call the GET\_DAILY\_CONSUMPTION\_ANOMALY\_DATA method.

    For example, suppose you are signed in to the organization account `my_orgacct`. You can identify cost anomalies in the account
    `prod_acct` between November 1, 2024, and December 31, 2024 by executing the following command:

    CopyExpand

    ```
    CALL SNOWFLAKE.LOCAL.ANOMALY_INSIGHTS!GET_DAILY_CONSUMPTION_ANOMALY_DATA(
      '2024-11-01', '2024-12-31', 'prod_acct');
    ```

    Show lessSee more

    Scroll to top

    To use the output to identify the cost anomalies, look for the days where the value of the `is_anomaly` column is `TRUE`.

## Investigate cost anomalies with ANOMALY\_INSIGHTS

The ANOMALY\_INSIGHTS class provides methods that you can use to investigate why a cost anomaly occurred. These methods allow you to drill
down into the following:

* [Account-level consumption](#label-cost-anomaly-investigate-account-class)
* [Warehouse-level consumption](#label-cost-anomaly-investigate-warehouse-class)
* [Query-level consumption](#label-cost-anomaly-investigate-query-class)
* [Hourly consumption](#label-cost-anomaly-investigate-hourly-class)

### Account-level consumption

Call the [ANOMALY\_INSIGHTS!GET\_TOP\_ACCOUNTS\_BY\_CONSUMPTION](../sql-reference/classes/anomaly-insights/methods/get_top_accounts_by_consumption) method to retrieve a list of accounts with
the highest change in consumption on a given day. Change in consumption is determined by comparing the consumption on a specified day with
consumption on the previous day. This is useful to investigate organization-level cost anomalies.

For example, if you are an administrator who wants to know the top five accounts in terms of change in consumption when comparing
December 14, 2024, and December 15, 2024, execute the following from the organization account or an ORGADMIN-enabled account:

CopyExpand

```
CALL SNOWFLAKE.LOCAL.ANOMALY_INSIGHTS!GET_TOP_ACCOUNTS_BY_CONSUMPTION('2024-12-15', 5);
```

Show lessSee more

Scroll to top

### Warehouse-level consumption

Call the [ANOMALY\_INSIGHTS!GET\_TOP\_WAREHOUSES\_ON\_DATE](../sql-reference/classes/anomaly-insights/methods/get_top_warehouses_on_date) method to retrieve a list of warehouses with the
highest change in consumption on a given day. Change in consumption is determined by comparing the consumption of a warehouse on a specified
day with consumption on the previous day. You can focus on the top warehouses within a specific account or identify top warehouses across
the organization.

Example: Identify top warehouses in the organization
:   To find the top six warehouses in the organization in terms of change in consumption when comparing August 9, 2024, and August 10, 2024,
    sign in to the organization account or an ORGADMIN-enabled account and execute the following:

    CopyExpand

    ```
    CALL SNOWFLAKE.LOCAL.ANOMALY_INSIGHTS!GET_TOP_WAREHOUSES_ON_DATE(
      '2024-08-10', 6, NULL);
    ```

    Show lessSee more

    Scroll to top

Example: Identify top warehouses in current account
:   To find the top five warehouses in the current account in terms of change in consumption when comparing December 8, 2024, and December 9,
    2024, execute the following:

    CopyExpand

    ```
    CALL SNOWFLAKE.LOCAL.ANOMALY_INSIGHTS!GET_TOP_WAREHOUSES_ON_DATE(
      '2024-12-09', 5, CURRENT_ACCOUNT_NAME());
    ```

    Show lessSee more

    Scroll to top

Example: Identify top warehouses in a different account
:   To find the top three warehouses in the account `my_acct` in terms of change in consumption when comparing November 8, 2024, and November 9,
    2024, sign in to the organization account or an ORGADMIN-enabled account and execute the following:

    CopyExpand

    ```
    CALL SNOWFLAKE.LOCAL.ANOMALY_INSIGHTS!GET_TOP_WAREHOUSES_ON_DATE(
      '2024-11-09', 5, 'my_acct');
    ```

    Show lessSee more

    Scroll to top

### Query-level consumption

Call the [ANOMALY\_INSIGHTS!GET\_TOP\_QUERIES\_FROM\_WAREHOUSE](../sql-reference/classes/anomaly-insights/methods/get_top_queries_from_warehouse) method to retrieve a list of queries that ran
on a specific warehouse so you can identify which queries resulted in high consumption. The returned queries are listed in the order of
consumption, from highest to lowest.

You use a Warehouse ID to specify which warehouse you are investigating. You can find the Warehouse ID by calling the
[ANOMALY\_INSIGHTS!GET\_TOP\_WAREHOUSES\_ON\_DATE](../sql-reference/classes/anomaly-insights/methods/get_top_warehouses_on_date) method or querying the
[WAREHOUSE\_METERING\_HISTORY view](../sql-reference/account-usage/warehouse_metering_history).

For example, to investigate consumption of a warehouse whose Warehouse ID is `838`, execute the following to list the top six queries that
consumed the most credits on December 1, 2024:

CopyExpand

```
CALL SNOWFLAKE.LOCAL.ANOMALY_INSIGHTS!GET_TOP_QUERIES_FROM_WAREHOUSE(838, '2024-12-01', 6);
```

Show lessSee more

Scroll to top

### Hourly consumption

Call the [ANOMALY\_INSIGHTS!GET\_HOURLY\_SPEND\_FOR\_ANOMALY](../sql-reference/classes/anomaly-insights/methods/get_hourly_spend_for_anomaly) method to retrieve the hourly consumption for a
given day. You can only retrieve data for the account that you are currently signed in to.

For example, to return the hourly consumption on October, 17 2024, execute the following:

CopyExpand

```
CALL SNOWFLAKE.LOCAL.ANOMALY_INSIGHTS!GET_HOURLY_SPEND_FOR_ANOMALY('2024-10-17');
```

Show lessSee more

Scroll to top
