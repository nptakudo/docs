---
title: "Consume imported data"
url: "https://docs.snowflake.com/en/user-guide/data-share-consumers"
---

# Consume imported data

This topic describes the tasks associated with creating databases from shares made available by data providers and then using the databases
for queries and other operations.

You must use the ACCOUNTADMIN role (or a role granted the IMPORT SHARE global privilege) to perform these tasks. For more details about the
IMPORT SHARE privilege, see [Enable non-ACCOUNTADMIN roles to perform data sharing tasks](security-access-privileges-shares).

Note

The tasks described in this topic do not apply to reader accounts. If you are using a reader account to consume imported data, you do not
need to perform any of these tasks because they have already been completed by an administrator from the provider account.

## General limitations for imported databases

Imported databases have the following limitations for consumers:

* Imported databases are read-only. Users in a consumer account can view/query data, but cannot insert or update data, or create any objects
  in the database.
* The following actions are not supported:

  > + Creating a clone of an imported database or any schemas/tables in the database.
  > + Time Travel for an imported database or any schemas/tables in the database.
  > + Editing the comments for an imported database.
  > + Attaching [storage lifecycle policies](storage-management/storage-lifecycle-policies) to tables in an imported database.
* Imported databases and all the objects in the database cannot be re-shared with (imported by) other accounts.
* Imported databases cannot be replicated.

## Viewing available shares

You can view the shares that are available to consume in your account using either the web interface or SQL:

SnowsightSQL

To view shares that have been shared with you, In the navigation menu, select Data sharing » Internal sharing, then select Shared With You.

> From this page, you can view the following:
>
> * **Privately Shared Listings** that have been shared with you. You can also view **Data exchange** listings that you have access to.
> * **Direct shares** that have been shared with you. Depending on the share status, shares are grouped into two sections:
>
>   + Direct shares that are ready to get (i.e. a database has not been created from the share).
>   + Direct shares that have been imported into a database and are ready to query.

To view Snowflake Marketplace listings that have been imported to a database and are ready to query, do the following steps:

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Marketplace » Snowflake Marketplace.

For more information, see [Explore listings](../collaboration/consumer-listings-exploring).

Execute a [SHOW SHARES](../sql-reference/sql/show-shares) or [DESCRIBE SHARE](../sql-reference/sql/desc-share) statement.

For example, using SQL:

CopyExpand

```
SHOW SHARES;
```

Show lessSee more

Scroll to top

The output shows the following:

* Two shares, `sales_s` and `sales_s2` are available. `INBOUND` in the `kind` column specifies that a data provider made the
  share available to your account to consume.
* The `name` column displays the name of each share, in the form of `share_name` (e.g. `SALES_S`).
* The `owner_account` column displays the account name that provided each share, in the form of `orgname.account_name`.
* If the `database_name` column is empty, a database has not yet been created from the share in your account.

Expand

```
+-------------------------------+----------+----------------------+---------------+-----------------------+------------------+--------------+----------------------------------------+---------------------+
| created_on                    | kind     | owner_account        | name          | database_name         | to               | owner        | comment                                | listing_global_name |                  |
|-------------------------------+----------+----------------------+---------------+-----------------------+------------------+--------------+----------------------------------------|---------------------|
| 2017-07-09 19:18:09.821 -0700 | INBOUND  | SNOW.XY12345         | SALES_S2      | UPDATED_SALES_DB      |                  |              | Transformed and updated sales data     |                     |
| 2017-06-15 17:02:29.625 -0700 | OUTBOUND | SNOW.MY_TEST_ACCOUNT | SALES_S       | SALES_DB              | XY12345, YZ23456 | ACCOUNTADMIN |                                        |                     |
+-------------------------------+----------+----------------------+---------------+-----------------------+----------------- -+--------------+----------------------------------------+---------------------+
```

Show lessSee more

Scroll to top

### DESCRIBE SHARE example

The following example uses the [DESCRIBE SHARE](../sql-reference/sql/desc-share) command to show the objects (database, schemas, and tables) that are in
the `sales_s` share:

> CopyExpand
>
> ```
> DESC SHARE xy12345.sales_s;
>
> +----------+------------------------------------+---------------------------------+
> | kind     | name                               | shared_on                       |
> |----------+------------------------------------+---------------------------------|
> | DATABASE | <DB>                               | Thu, 15 Jun 2017 17:03:16 -0700 |
> | SCHEMA   | <DB>.AGGREGATES_EULA               | Thu, 15 Jun 2017 17:03:16 -0700 |
> | TABLE    | <DB>.AGGREGATES_EULA.AGGREGATE_1   | Thu, 15 Jun 2017 17:03:16 -0700 |
> | VIEW     | <DB>.AGGREGATES_EULA.AGGREGATE_1_v | Thu, 15 Jun 2017 17:03:16 -0700 |
> +----------+------------------------------------+---------------------------------+
> ```
>
> Show lessSee more
>
> Scroll to top
>
> The share consists of one schema, `aggregates_eula`, with one table, `aggregate_1`. Each object name, including the database
> itself, is prefixed with `<DB>`. This indicates a database has not been created yet (in your account) from the share.

## Creating a database from a share

You can create a database from a share using the web interface or SQL:

SnowsightSQL

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Data sharing » External sharing.
3. Select the Shared with you tab.
4. In the Ready to Get section, select the share that you want to create a database for.
5. Set a database name and the roles that are permitted to access the database.
6. Select Get Data.

Execute a [CREATE DATABASE](../sql-reference/sql/create-database) statement with the following data sharing-specific syntax:

CopyExpand

```
CREATE DATABASE <name> FROM SHARE <provider_account>.<share_name>
```

Show lessSee more

Scroll to top

Where `provider_account` is the name of the account that provided the share and `share_name` is the name of the share
from which to create the database.

Note

* A share can only be consumed once per account.
* To see the objects that are being imported before creating a database, use the [DESCRIBE SHARE](../sql-reference/sql/desc-share) command.
* When a database is created from a share, only the role used to create the database can access objects in the database by default.
  For instructions on granting access to other roles, see [Granting Privileges on an Imported Database](#granting-privileges-on-an-imported-database) (in this topic).

### SQL examples

The following example creates a new database named `snow_sales` in your account from the `sales_s` share:

> CopyExpand
>
> ```
> CREATE DATABASE snow_sales FROM SHARE xy12345.sales_s;
> ```
>
> Show lessSee more
>
> Scroll to top

List the new `snow_sales` database:

> CopyExpand
>
> ```
> SHOW DATABASES LIKE 'snow%';
>
> +---------------------------------+-----------------------+------------+------------+-------------------------+--------------+---------+---------+----------------+
> | created_on                      | name                  | is_default | is_current | origin                  | owner        | comment | options | retention_time |
> |---------------------------------+-----------------------+------------+------------+-------------------------+--------------+---------+---------+----------------|
> | Sun, 10 Jul 2016 23:28:50 -0700 | SNOWFLAKE_SAMPLE_DATA | N          | N          | SFC_SAMPLES.SAMPLE_DATA | ACCOUNTADMIN |         |         | 1              |
> | Thu, 15 Jun 2017 18:30:08 -0700 | SNOW_SALES            | N          | Y          | xy12345.SALES_S         | ACCOUNTADMIN |         |         | 1              |
> +---------------------------------+-----------------------+------------+------------+-------------------------+--------------+---------+---------+----------------+
> ```
>
> Show lessSee more
>
> Scroll to top
>
> In this example, the `origin` column indicates the fully-qualified name of the share from which the database was created.

Similarly, the output of SHOW SHARES and DESC SHARE includes the name of the database that was created from the share:

> CopyExpand
>
> ```
> SHOW SHARES;
> ```
>
> Show lessSee more
>
> Scroll to top
>
> Expand
>
> ```
> +-------------------------------+----------+----------------------+---------------+-----------------------+------------------+--------------+----------------------------------------+---------------------+
> | created_on                    | kind     | owner_account        | name          | database_name         | to               | owner        | comment                                | listing_global_name |
> |-------------------------------+----------+----------------------+---------------+-----------------------+------------------+--------------+----------------------------------------|---------------------|
> | 2017-07-09 19:18:09.821 -0700 | INBOUND  | SNOW.XY12345         | SALES_S2      | UPDATED_SALES_DB      |                  |              | Transformed and updated sales data     |                     |
> | 2017-06-15 17:02:29.625 -0700 | OUTBOUND | SNOW.MY_TEST_ACCOUNT | SALES_S       | SALES_DB              | XY12345, YZ23456 | ACCOUNTADMIN |                                        |                     |
> +-------------------------------+----------+----------------------+---------------+-----------------------+------------------+--------------+----------------------------------------+---------------------+
> ```
>
> Show lessSee more
>
> Scroll to top
>
> CopyExpand
>
> ```
> DESC SHARE xy12345.sales_s;
>
> +----------+------------------------------------------+---------------------------------+
> | kind     | name                                     | shared_on                       |
> |----------+------------------------------------------+---------------------------------|
> | DATABASE | SNOW_SALES                               | Thu, 15 Jun 2017 17:03:16 -0700 |
> | SCHEMA   | SNOW_SALES.AGGREGATES_EULA               | Thu, 15 Jun 2017 17:03:16 -0700 |
> | TABLE    | SNOW_SALES.AGGREGATES_EULA.AGGREGATE_1   | Thu, 15 Jun 2017 17:03:16 -0700 |
> | VIEW     | SNOW_SALES.AGGREGATES_EULA.AGGREGATE_1_v | Thu, 15 Jun 2017 17:03:16 -0700 |
> +----------+------------------------------------------+---------------------------------+
> ```
>
> Show lessSee more
>
> Scroll to top

## Granting privileges on an imported database

The instructions to grant access to objects in a share differ depending on whether the provider segmented the objects in a share using
database roles. This option associates different objects in the share with different database roles.

Note that a single share can include both objects that are accessible via database roles and objects that are not associated with a
database role.

### Option 1: Objects in a share aren’t associated with a database role

Allow users to access objects in a share by granting the IMPORTED PRIVILEGES privilege on an imported database to one or more roles in your
account.

A role can grant IMPORTED PRIVILEGES on an imported database only when it either:

* Owns the imported database (i.e. has the OWNERSHIP privilege on the database).
* Was granted the MANAGE GRANTS global privilege.

#### Assigning IMPORTED PRIVILEGES to other roles

You can assign this role to other roles using either Snowsight or SQL:

SnowsightSQL

1. Select Catalog » Database Explorer.
2. Select the database that you want to grant privileges to.
3. In the Privileges section, select + Privileges.
4. Select a role and privilege to grant to that role.
5. Select Grant Privileges.

Execute a [GRANT <privileges> … TO ROLE](../sql-reference/sql/grant-privilege) statement.

To see the roles that have USAGE privileges on an imported database, use Snowsight or the [SHOW GRANTS](../sql-reference/sql/show-grants) command.

#### SQL examples

1. Role `r1` creates database `snow_sales` from share `xy12345.sales_s`:

   CopyExpand

   ```
   use role r1;
   create database snow_sales from share xy12345.sales_s;
   ```

   Show lessSee more

   Scroll to top
2. Role `r1` grants IMPORTED PRIVILEGES on database `snow_sales` to role `r2`:

   CopyExpand

   ```
   grant imported privileges on database snow_sales to role r2;
   ```

   Show lessSee more

   Scroll to top
3. Since `r2` does not have the OWNERSHIP privilege on the database, to be able to perform either of the following grant or revoke
   operations, role `r2` must hold the MANAGE GRANTS privilege on the account:

   CopyExpand

   ```
   use role r2;
   grant imported privileges on database snow_sales to role r3;
   revoke imported privileges on database snow_sales from role r3;
   ```

   Show lessSee more

   Scroll to top

### Option 2: Objects in a share associated with a database role

Allow users to access objects in a share by granting the appropriate database role in the imported database to one or more roles in your
account.

#### Step 1: Create a database from the share

Create a database from the share using [CREATE DATABASE … FROM SHARE](../sql-reference/sql/create-database).

Executing this command requires a role with the global CREATE DATABASE and IMPORT SHARE privileges.

For example, create databases `c1` from provider `provider1` and share `share1`:

CopyExpand

```
CREATE DATABASE c1 FROM SHARE provider1.share1;
```

Show lessSee more

Scroll to top

#### Step 2: Grant database roles to your account-level roles

Grant database roles to roles in your account to allow users with those roles to access database objects in the share.

Use the role that you used to create the database from the share.

For example, see the database roles available, then grant database role `c1.r1` to the `analyst` role in your account:

CopyExpand

```
SHOW DATABASE ROLES in DATABASE c1;
GRANT DATABASE ROLE c1.r1 TO ROLE analyst;
```

Show lessSee more

Scroll to top

## Creating streams on shared views or tables

Creating streams on shared objects (secure views or tables) enables you to track data manipulation language (DML) changes made in those
objects. This functionality is similar to creating and using streams on “local” objects (i.e. in the same account as the stream).

The role used to execute the SQL statements in this section must have the required grants on the shared table or secure view. For information,
see [Granting privileges on an imported database](#label-granting-privileges-on-shared-database) (in this topic).

* To create streams on shared views:

  CopyExpand

  ```
  CREATE STREAM <name> ON VIEW <shared_db>.<schema>.<view>;
  ```

  Show lessSee more

  Scroll to top

  For example, create a stream on the shared `aggregate_1_v` view in the `snow_sales.aggregates_eula` database and schema:

  CopyExpand

  ```
  CREATE STREAM aggregate_1_v_stream ON VIEW snow_sales.aggregates_eula.aggregate_1_v;
  ```

  Show lessSee more

  Scroll to top
* To create streams on shared tables:

  CopyExpand

  ```
  CREATE STREAM <name> ON TABLE <shared_db>.<schema>.<table>;
  ```

  Show lessSee more

  Scroll to top

  For example, create a table stream on the shared `aggregate_1` table in the `snow_sales.aggregates_eula` database and schema:

  CopyExpand

  ```
  CREATE STREAM aggregate_1_stream ON TABLE snow_sales.aggregates_eula.aggregate_1;
  ```

  Show lessSee more

  Scroll to top

For more information on creating streams, see [CREATE STREAM](../sql-reference/sql/create-stream).

Note

* The data provider must enable change tracking on views or tables before you can create streams on these objects. If you cannot
  create streams on a desired shared object, contact the data provider to consider enabling change tracking on the object.
* To avoid allowing a stream to become stale, consume the stream records within a transaction during the retention period for the table.
  Contact the data provider to determine the data retention period for the table.

  To determine whether a stream has become stale, execute the [DESCRIBE STREAM](../sql-reference/sql/desc-stream) or [SHOW STREAMS](../sql-reference/sql/show-streams)
  command. In the command output, when the STALE column value is TRUE, the stream may be stale. In practice, reading from the stream may
  succeed for some time after the expected STALE\_AFTER. However, the stream may become stale at any time during this period.

## Querying an imported database

Querying an imported database is the same as querying any other database in your account.

For example:

> CopyExpand
>
> ```
> USE ROLE r1;
>
> USE DATABASE snow_sales;
>
> SELECT * FROM aggregates_1;
> ```
>
> Show lessSee more
>
> Scroll to top
