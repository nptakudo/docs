---
title: "Explore Data from SAP® Business Data Cloud"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/sap-sql/explore-data"
---

# Explore Data from SAP® Business Data Cloud

Feature — Limited General Availability

SAP® Business Data Cloud Connect for Snowflake is available by request through Snowflake. Please contact your Snowflake account manager to request access.

SAP® Snowflake is available by request through SAP®. Please contact your SAP® account manager to request access.

For more information on these solutions, see [Two Ways to Integrate Snowflake and SAP®](../about-sap-snowflake.html#label-two-ways-to-integrate-snowflake-and-sap).

In this topic we will explore the data that has been shared with Snowflake.
All examples are intended to showcase accessing data using Snowflake.

The following sections use `CUSTOMER` as an example database, but you can use the same steps to explore the data in your database.

## Explore the database, schemas, and tables

Examine the database:

> CopyExpand
>
> ```
> DESC DATABASE CUSTOMER;
> ```
>
> Show lessSee more
>
> Scroll to top
>
> Which should produce results similar to:
>
> Expand
>
> ```
> +--------------------------------+---------------------------------+
> |created_on                              | name                            | kind      |
> +--------------------------------+---------------------------------+
> | 2025-12-17 13:30:01.062 -0800  | INFORMATION_SCHEMA      | SCHEMA    |
> | 2025-12-17 13:11:12.206 -0800  | customer            | SCHEMA    |
> +--------------------------------+---------------------------------+
> ```
>
> Show lessSee more
>
> Scroll to top
>
> Where each row represents the schema in your database.

Examine the tables in the database:

> CopyExpand
>
> ```
> SHOW TABLES IN CUSTOMER;
> ```
>
> Show lessSee more
>
> Scroll to top
>
> Which should produce results similar to:
>
> Expand
>
> ```
> +------------------------+----------------+-------------+-------+-------+--------+
> | name                               | database_name  | schema_name | kind  | rows  | bytes  |
> +------------------------+----------------+-------------+-------+-------+--------+
> | customer                           | CUSTOMER       | customer    | TABLE | 2174  | 215708 |
> | customercompanycode          | CUSTOMER               | customer    | TABLE | 1792  | 37311  |
> | customerdunning                | CUSTOMER             | customer    | TABLE | 44    | 4912   |
> | customersalesarea            | CUSTOMER               | customer    | TABLE | 442   | 34415  |
> | customersalesareatax   | CUSTOMER             | customer    | TABLE | 883   | 9153   |
> | customerunloadingpoint | CUSTOMER             | customer    | TABLE | 37    | 13253  |
> +------------------------+----------------+-------------+-------+-------+--------+
> ```
>
> Show lessSee more
>
> Scroll to top

## Query tables in the CUSTOMER database

Query the ‘customer’ table:

> CopyExpand
>
> ```
> SELECT * FROM CUSTOMER.customer.customer;
>
> SELECT * FROM CUSTOMER.customer.customer WHERE CREATEDBYUSER = 'KAPOORM'
> ```
>
> Show lessSee more
>
> Scroll to top

## Create Derived Data from shared data

Create L1 data by joining tables from 2 shared Data Products:

CopyExpand

```
-- Join tables in CUSTOMER and ENTRYVIEWJOURNALENTRY to find top 10 customers by revenue
SELECT
    c.customer,
    c.customername,
    c.country,
    c.region,
    c.businesstype,
    COUNT(DISTINCT e.accountingdocument) as num_transactions,
    SUM(e.amountincompanycodecurrency) as total_revenue,
    AVG(e.amountincompanycodecurrency) as avg_transaction_amount
FROM CUSTOMER.customer.customer c
JOIN ENTRYVIEWJOURNALENTRY.entryviewjournalentry.operationalacctgdocitem e
   ON c.customer = e.customer
WHERE c.deletionindicator = FALSE
GROUP BY 1,2,3,4,5
ORDER BY total_revenue DESC
LIMIT 10;
```

Show lessSee more

Scroll to top

## Create Table As Select (CTAS) in a new database

Create a new database to hold the CTAS:

CopyExpand

```
CREATE DATABASE CUSTOMER_CTAS_DEMO;
USE DATABASE CUSTOMER_CTAS_DEMO;

-- Create the CTAS
CREATE OR REPLACE TABLE top_customers_by_revenue AS
SELECT
  c.customer,
  c.customername,
  c.country,
  c.region,
  c.businesstype,
  COUNT(DISTINCT e.accountingdocument) as num_transactions,
  SUM(e.amountincompanycodecurrency) as total_revenue,
  AVG(e.amountincompanycodecurrency) as avg_transaction_amount
FROM CUSTOMER.customer.customer c
JOIN ENTRYVIEWJOURNALENTRY.entryviewjournalentry.operationalacctgdocitem e
    ON c.customer = e.customer
WHERE c.deletionindicator = FALSE
 GROUP BY 1,2,3,4,5
 ORDER BY total_revenue DESC;

 -- Query the CTAS
 SELECT * FROM top_customers_by_revenue LIMIT 10;
```

Show lessSee more

Scroll to top
