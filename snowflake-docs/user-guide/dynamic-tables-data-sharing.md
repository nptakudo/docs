---
title: "Data sharing with dynamic tables"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-data-sharing"
---

# Data sharing with dynamic tables

Dynamic tables are shareable objects. To share a dynamic table, data sharing providers grant privileges on a dynamic table to a share, which
in turn can be used by data sharing consumers.

## How data is shared with dynamic tables

To share a dynamic table with other Snowflake accounts, you can add dynamic tables to a share or to an application package.

* To share a dynamic table with accounts in your region, you can use a Direct Share. For more information, see [Data sharing and collaboration in Snowflake](../guides-overview-sharing).
* To share a dynamic table with accounts in other regions, add the share or application package to a listing as a data product and set up Cross-Cloud Auto-Fulfillment. For more information, see [Create and publish a listing](../collaboration/provider-listings-creating-publishing).

A data sharing provider can choose to grant the SELECT privilege on a single dynamic table or grant the SELECT privilege on all dynamic
tables in a database, as shown in the following examples.

CopyExpand

```
GRANT SELECT ON ALL DYNAMIC TABLES IN SCHEMA mydb.public TO SHARE share1;

GRANT SELECT ON DYNAMIC TABLE mydb.public TO SHARE share1;
```

Show lessSee more

Scroll to top

For more details, see [GRANT <privilege> … TO SHARE](../sql-reference/sql/grant-privilege-share).

## Create a dynamic table to ingest shared data

When you use a dynamic table to ingest shared data, the query can’t select from a shared dynamic table or a shared secure view that references
an upstream dynamic table.

To create a dynamic table to ingest shared data, do the following:

1. Ensure that you have the [right privileges](../sql-reference/sql/create-database), and create a database from a share and grant
   privileges on it.

   CopyExpand

   ```
   CREATE DATABASE my_shared_db FROM SHARE provider_account.share1;
   ```

   Show lessSee more

   Scroll to top
2. [Grant privileges](data-share-consumers.html#label-granting-privileges-on-shared-database) to the shared database.
3. Create a shared dynamic table.

> CopyExpand
>
> ```
> CREATE OR REPLACE DYNAMIC TABLE my_dynamic_table
>   TARGET_LAG = '1 day'
>   WAREHOUSE = mywh
>   AS
>     SELECT * FROM my_shared_db.public.mydb;
> ```
>
> Show lessSee more
>
> Scroll to top
>
> Note
>
> Change tracking must be enabled on all underlying objects used by a dynamic table. To use a dynamic table to ingest shared data, the data
> sharing provider needs to enable `change_tracking` on the shared object. For more information, see
> [Enable change tracking](dynamic-tables-create.html#label-dynamic-tables-and-change-tracking).
