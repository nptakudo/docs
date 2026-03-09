---
title: "Share data securely across regions and cloud platforms"
url: "https://docs.snowflake.com/en/user-guide/secure-data-sharing-across-regions-platforms"
---

# Share data securely across regions and cloud platforms

This topic provides instructions on using [replication](account-replication-intro) to allow data providers
to securely share data with data consumers across different [regions](intro-regions) and
[cloud platforms](intro-cloud-platforms).

Note

If you use listings to share data with specific consumer accounts, or you use the Snowflake Marketplace,
you can use [Cross-Cloud Auto-fulfillment](../collaboration/provider-listings-auto-fulfillment)
to automatically fulfill your data product to other regions.

Cross-region data sharing is supported by Snowflake accounts hosted on any of the following cloud platforms:

* Amazon Web Services (AWS)
* Google Cloud Platform (GCP)
* Microsoft Azure (Azure)

Important

If you replicate a primary database to accounts in a geographic region or country that is different from that in which your source
Snowflake account is located, you should confirm that your organization does not have any legal or regulatory restrictions as to where
your data can be transferred or hosted.

## Data sharing considerations

> ![Diagram of data replication and sharing between regions and clouds](../_images/global-data-sharing-replication-sharing.png)

* Since cross-region data sharing utilizes Snowflake data replication functionality, understand how replication works in Snowflake
  as part of your planning process. For more information, see:

  + [Introduction to replication and failover across multiple accounts](account-replication-intro)
  + [Replication considerations](account-replication-considerations)
  + [Replicating databases and account objects across multiple accounts](account-replication-config)
* Data providers only need to create one copy of the dataset per region; and not a copy per consumer.
* When sharing a view that references objects in multiple databases, each of these other databases must be included in the replication
  group. Sharing data from more than one database requires additional steps. For instructions, see
  [Share data from multiple databases](data-sharing-multiple-db).
* For information related to using [Virtual Private Snowflake (VPS)](intro-editions.html#label-snowflake-editions-vps) with data sharing,
  see [About collaboration in VPS environments](../collaboration/virtual-private-snowflake/about-vps-collaboration).

## Sharing data with data consumers in a different region and cloud platform

Snowflake data providers can share data with data consumers in a different region in a few simple steps.

### Step 1: Set up data replication

Note

Before configuring data replication, you must create an account in a region where you wish to share data and link it to your local
account. For more information, see [Working with organizations and accounts](../guides-overview-manage).

Setting up data replication involves the following tasks:

1. Enable replication for your accounts.

   An [organization administrator](organization-administrators) must enable replication for the source account that
   contains the data to share and the target accounts in regions where you want to share data with consumers. For instructions on enabling
   replication, see [Prerequisite: Enable replication for accounts in the organization](account-replication-config.html#label-enabling-accounts-for-replication).
2. Create a replication group and add databases and shares.
3. Replicate the group with the databases and shares to the regions where you want to share data with consumers.

### Step 2. Share data with data consumers

Sharing data with data consumers in the same region involves adding one or more consumer accounts to the
secondary shares that you replicated from the source account.

For detailed instructions, see [Getting Started with Secure Data Sharing](data-sharing-gs).

## Example 1: Share data

A data provider, Acme, wants to share data with data consumers in a different region.

> ![Diagram of a basic example on how to share data between regions](../_images/global-data-sharing-basic.png)

### Execute from source account

To create a replication group that contains the databases and shares to replicate to another region, execute the following
SQL statement.

Note

If you have previously enabled replication for an individual database, you must disable database replication for the
database *before* you add it to a replication group. For details, see [Transitioning from database replication to group-based replication](account-replication-config.html#label-disabling-database-replication).

Create a replication group `my_rg` that includes database `db1` and share `share1` to replicate to the account `account_2`
in the `acme` org.

CopyExpand

```
USE ROLE ACCOUNTADMIN;

CREATE REPLICATION GROUP my_rg
  OBJECT_TYPES = databases, shares
  ALLOWED_DATABASES = db1
  ALLOWED_SHARES = share1
  ALLOWED_ACCOUNTS = acme.account_2;
```

Show lessSee more

Scroll to top

### Execute from target account

From the target account in the other region, execute the following SQL statements.
Any account that you add to the share should be local to the region of the target account.
After you alter the share to set a list of accounts (targets), your added accounts won’t be overwritten in the next refresh.

1. Create a secondary replication group in `account_2`:

   CopyExpand

   ```
   USE ROLE ACCOUNTADMIN;

   CREATE REPLICATION GROUP my_rg
     AS REPLICA OF acme.account1.my_rg;
   ```

   Show lessSee more

   Scroll to top
2. Manually refresh the replication group to replicate the databases and shares to `account_2`:

   CopyExpand

   ```
   ALTER REPLICATION GROUP my_rg REFRESH;
   ```

   Show lessSee more

   Scroll to top
3. Add one or more consumer accounts to `share1`:

   CopyExpand

   ```
   ALTER SHARE share1 ADD ACCOUNTS = consumer_org.consumer_account_name;
   ```

   Show lessSee more

   Scroll to top

You can automate refresh operations by setting the REPLICATION\_SCHEDULE parameter for the *primary* replication group using the
[ALTER REPLICATION GROUP](../sql-reference/sql/alter-replication-group) command in the source account. For more information,see
[Replication schedule](account-replication-intro.html#label-replication-schedule).

## Example 2: Share a subset of data from a database

A data provider, Acme, wants to share a subset of data with data consumers in a different region. To reduce replication costs, they
would like to only replicate the relevant rows from their master table. Since replication is done at the database level, this example
describes how Acme can use streams and tasks to copy the desired rows from the main database to a new database, create a share and
grant privileges on the view, and replicate both in a replication group to an account in a different region for consumer access.
In this scenario the new database and share are designated as primary objects for data replication.

> ![Diagram of an advanced example on how to share data between regions](../_images/global-data-sharing-advanced.png)

### Execute from source account

Use the following SQL commands to create a new database in the source account and enable replication.

Note

If you have previously enabled replication for an individual database, you must disable database replication for the
database *before* you add it to a replication group. For details, see [Transitioning from database replication to group-based replication](account-replication-config.html#label-disabling-database-replication).

1. In your local account, create a database `db1` with a subset of data from the database with the source data:

   CopyExpand

   ```
   USE ROLE ACCOUNTADMIN;

   CREATE DATABASE db1;

   CREATE SCHEMA db1.sch;

   CREATE TABLE db1.sch.table_b AS
     SELECT customerid, user_order_count, total_spent
     FROM source_db.sch.table_a
     WHERE REGION='azure_eastus2';
   ```

   Show lessSee more

   Scroll to top
2. Create a secure view with the data to share:

   CopyExpand

   ```
   CREATE SECURE VIEW db1.sch.view1 AS
     SELECT customerid, user_order_count, total_spent
     FROM db1.sch.table_b;
   ```

   Show lessSee more

   Scroll to top
3. Create a stream to record changes made to the source table:

   CopyExpand

   ```
   CREATE STREAM mystream ON TABLE source_db.sch.table_a APPEND_ONLY = TRUE;
   ```

   Show lessSee more

   Scroll to top
4. Create a task to insert data into the table in `db1` with changes from the source data:

   CopyExpand

   ```
   CREATE TASK mytask1
     WAREHOUSE = mywh
     SCHEDULE = '5 minute'
   WHEN
     SYSTEM$STREAM_HAS_DATA('mystream')
   AS
     INSERT INTO table_b(CUSTOMERID, USER_ORDER_COUNT, TOTAL_SPENT)
       SELECT customerid, user_order_count, total_spent
       FROM mystream
       WHERE region='azure_eastus2'
       AND METADATA$ACTION = 'INSERT';
   ```

   Show lessSee more

   Scroll to top
5. Start the task to update data:

   CopyExpand

   ```
   ALTER TASK mytask1 RESUME;
   ```

   Show lessSee more

   Scroll to top
6. Create a share and grant privileges to the share:

   CopyExpand

   ```
   CREATE SHARE share1;

   GRANT USAGE ON DATABASE db1 TO SHARE share1;
   GRANT USAGE ON SCHEMA db1.sch TO SHARE share1;
   GRANT SELECT ON VIEW db1.sch.view1 TO SHARE share1;
   ```

   Show lessSee more

   Scroll to top
7. Create a primary replication group with the database and share:

   CopyExpand

   ```
   CREATE REPLICATION GROUP my_rg
     OBJECT_TYPES = DATABASES, SHARES
     ALLOWED_DATABASES = db1
     ALLOWED_SHARES = share1
     ALLOWED_ACCOUNTS = acme_org.account_2;
   ```

   Show lessSee more

   Scroll to top

### Execute from target account

Execute the following SQL commands from the target account in the other region.

1. Create a secondary replication group to replicate the databases and shares from the source account:

   CopyExpand

   ```
   USE ROLE ACCOUNTADMIN;

   CREATE REPLICATION GROUP my_rg
     AS REPLICA OF acme_org.account_1.my_rg;
   ```

   Show lessSee more

   Scroll to top
2. Manually refresh the group to replicate objects to the current account:

   CopyExpand

   ```
   ALTER REPLICATION GROUP my_rg REFRESH;
   ```

   Show lessSee more

   Scroll to top
3. Add one or more consumer accounts to the share:

   CopyExpand

   ```
   ALTER SHARE share1 ADD ACCOUNTS = consumer_org.consumer_account_name;
   ```

   Show lessSee more

   Scroll to top

You can automate refresh operations by setting the REPLICATION\_SCHEDULE parameter for the *primary* replication group using the
[ALTER REPLICATION GROUP](../sql-reference/sql/alter-replication-group) command in the source account. For more information,see
[Replication schedule](account-replication-intro.html#label-replication-schedule).

## Example 3: Share data from multiple databases

A data provider, Acme, wants to share data from multiple databases with data consumers in a different region. They
create a secure view and share (for instructions, see [Share data from multiple databases](data-sharing-multiple-db)), then
replicate all the databases and share in a replication group to replicate data to accounts in other regions.

### Execute from source account

Create a replication group `my_rg` that includes the databases and share from [Example 1: Create and share a secure view in an existing database](data-sharing-multiple-db.html#label-share-multi-db-example1) to replicate
to `account_2` in the `acme` org:

CopyExpand

```
CREATE REPLICATION GROUP my_rg
  OBJECT_TYPES = databases, shares
  ALLOWED_DATABASES = database1, database2, database3
  ALLOWED_SHARES = share1
  ALLOWED_ACCOUNTS = acme.account_2;
```

Show lessSee more

Scroll to top

### Execute from target account

Execute the following SQL commands from the target account in the other region.

1. Create a secondary replication group to replicate the databases and shares from the source account:

   CopyExpand

   ```
   USE ROLE ACCOUNTADMIN;

   CREATE REPLICATION GROUP my_rg
     AS REPLICA OF acme_org.account_1.my_rg;
   ```

   Show lessSee more

   Scroll to top
2. Manually refresh the group to replicate objects to the current account:

   CopyExpand

   ```
   ALTER REPLICATION GROUP my_rg REFRESH;
   ```

   Show lessSee more

   Scroll to top
3. Add one or more consumer accounts to the share:

   CopyExpand

   ```
   ALTER SHARE share1 ADD ACCOUNTS = consumer_org.consumer_account_name;
   ```

   Show lessSee more

   Scroll to top

You can automate refresh operations by setting the REPLICATION\_SCHEDULE parameter for the *primary* replication group using the
[ALTER REPLICATION GROUP](../sql-reference/sql/alter-replication-group) command in the source account. For more information,see
[Replication schedule](account-replication-intro.html#label-replication-schedule).
