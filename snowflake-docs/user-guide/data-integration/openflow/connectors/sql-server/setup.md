---
title: "Set up the Openflow Connector for SQL Server"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/sql-server/setup"
---

# Set up the Openflow Connector for SQL Server

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) [Preview Feature](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/preview-terms-of-service/)

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes how to set up the Openflow Connector for SQL Server.

For information on the incremental load process, see [Incremental replication](incremental-replication).

## Prerequisites

Before setting up the connector, ensure that you have completed the following prerequisites:

1. Ensure that you have reviewed [About Openflow Connector for SQL Server](about).
2. Ensure that you have reviewed [Supported SQL Server versions](about.html#label-sql-server-versions).
3. Ensure that you have set up your runtime deployment. For more information, see the following topics:

   * [Set up Openflow - BYOC](../../setup-openflow-byoc)
   * [Set up Openflow - Snowflake Deployments](../../setup-openflow-spcs).
4. If you use Openflow - Snowflake Deployments, ensure that you have reviewed
   [configuring required domains](../../setup-openflow-spcs-sf-allow-list) and have granted access to the required domains for the [SQL Server](../../setup-openflow-spcs-sf-allow-list.html#label-openflow-domains-used-by-openflow-connectors-sqlserver) connector.

## Set up your SQL Server instance

Before setting up the connector, perform the following tasks in your SQL Server environment:

Note

You must perform these tasks as a database administrator.

1. Enable change tracking on the
   [databases](https://learn.microsoft.com/en-us/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server?view=sql-server-ver16#enable-change-tracking-for-a-database)
   and
   [tables](https://learn.microsoft.com/en-us/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server?view=sql-server-ver16#enable-change-tracking-for-a-table)
   that you plan to replicate, as shown in the following SQL Server example:

   CopyExpand

   ```
   ALTER DATABASE <database>
     SET CHANGE_TRACKING = ON
     (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON);

   ALTER TABLE <schema>.<table>
     ENABLE CHANGE_TRACKING;
   ```

   Show lessSee more

   Scroll to top

   Note

   Run these commands for every database and table that you plan to replicate.

   The connector requires that change tracking is enabled on the databases and tables before replication
   starts. Ensure that every table that you plan to replicate has enabled change tracking. You
   can also enable change tracking on additional tables while the connector is running.
2. Create a login for the SQL Server instance:

   CopyExpand

   ```
   CREATE LOGIN <user_name> WITH PASSWORD = '<password>';
   ```

   Show lessSee more

   Scroll to top

   This login is used to create users for the databases you plan to replicate.
3. Create a user for each database you are replicating by running the following
   SQL Server command in each database:

   CopyExpand

   ```
   USE <source_database>;
   CREATE USER <user_name> FOR LOGIN <user_name>;
   ```

   Show lessSee more

   Scroll to top
4. Grant the SELECT and VIEW CHANGE TRACKING permissions to the user for each database that you are
   replicating:

   CopyExpand

   ```
   GRANT SELECT ON <database>.<schema>.<table> TO <user_name>;
   GRANT VIEW CHANGE TRACKING ON <database>.<schema>.<table> TO <user_name>;
   ```

   Show lessSee more

   Scroll to top

   Run these commands in each database for every table that you plan to replicate.
   These permissions must be granted to the user of each database that you created in a
   previous step.
5. (Optional) Grant the VIEW DEFINITION privilege on the User Defined Data Types (UDDT).

   If your tables contain columns that use User Defined Data Types (UDDT), and the UDDT is owned by
   a different user than the connector user, you must grant the VIEW DEFINITION permission
   to the connector user as shown in the following SQL Server example:

   CopyExpand

   ```
   GRANT VIEW DEFINITION TO <user_name>;
   ```

   Show lessSee more

   Scroll to top

   Without this permission, columns using UDDT are silently excluded from replication.
6. (Optional) Configure SSL connection.

   If you use an SSL connection to connect SQL Server, create the root certificate for your database
   server. This is required when configuring the connector.

## Set up your Snowflake environment

As a Snowflake administrator, perform the following tasks:

1. Create a destination database in Snowflake to store the replicated data:

   CopyExpand

   ```
   CREATE DATABASE <destination_database>;
   ```

   Show lessSee more

   Scroll to top
2. Create a Snowflake [service user](../../../../../sql-reference/sql/create-user.html#label-user-type-property):

   CopyExpand

   ```
   CREATE USER <openflow_user>
     TYPE = SERVICE
     COMMENT='Service user for automated access of Openflow';
   ```

   Show lessSee more

   Scroll to top
3. Create a Snowflake role for the connector and grant the required privileges:

   CopyExpand

   ```
   CREATE ROLE <openflow_role>;
   GRANT ROLE <openflow_role> TO USER <openflow_user>;
   GRANT USAGE ON DATABASE <destination_database> TO ROLE <openflow_role>;
   GRANT CREATE SCHEMA ON DATABASE <destination_database> TO ROLE <openflow_role>;
   ```

   Show lessSee more

   Scroll to top

   Use this role to manage the connector’s access to the Snowflake database.

   To create objects in the destination database, you must grant the
   [USAGE and CREATE SCHEMA privileges](../../../../security-access-control-privileges.html#label-database-privileges) on the database to the
   role used to manage access.
4. Create a Snowflake warehouse for the connector and grant the required privileges:

   CopyExpand

   ```
   CREATE WAREHOUSE <openflow_warehouse> WITH
     WAREHOUSE_SIZE = 'XSMALL'
     AUTO_SUSPEND = 300
     AUTO_RESUME = TRUE;
   GRANT USAGE, OPERATE ON WAREHOUSE <openflow_warehouse> TO ROLE <openflow_role>;
   ```

   Show lessSee more

   Scroll to top

   Snowflake recommends starting with a XSMALL warehouse size, then experimenting with size
   depending on the number of tables being replicated and the amount of data transferred. Large
   numbers of tables typically scale better with multi-cluster warehouses, rather than a larger
   warehouse size. For more information, see
   [multi-cluster warehouses](../../../../warehouses-multicluster).
5. Set up the public and private keys for key pair authentication:

   1. Create a pair of secure keys (public and private).
   2. Store the private key for the user in a file to supply to the connector’s configuration.
   3. Assign the public key to the Snowflake service user:

      CopyExpand

      ```
      ALTER USER <openflow_user> SET RSA_PUBLIC_KEY = 'thekey';
      ```

      Show lessSee more

      Scroll to top

      For more information, see [Key-pair authentication and key-pair rotation](../../../../key-pair-auth).

## Install the connector

To install the connector, do the following as a data engineer:

1. Navigate to the Openflow overview page. In the Featured connectors section, select View more connectors.
2. On the Openflow connectors page, find the connector and select Add to runtime.
3. In the Select runtime dialog, select your runtime from the Available runtimes drop-down list and click Add.

   Note

   Before you install the connector, ensure that you have created a database and schema in Snowflake for the connector to store ingested data.
4. Authenticate to the deployment with your Snowflake account credentials and select Allow when prompted to allow the runtime application to access your Snowflake account. The connector installation process takes a few minutes to complete.
5. Authenticate to the runtime with your Snowflake account credentials.

The Openflow canvas appears with the connector process group added to it.

## Configure the connector

To configure the connector, do the following as a data engineer:

1. Right-click on the imported process group and select Parameters.
2. Populate the required parameter values as described in [Flow parameters](#label-of-sqlserver-flow-parameters).

### Flow parameters

Start by setting the parameters of the SQLServer Source Parameters context, then the SQLServer Destination Parameters context.
After you complete this, enable the connector. The connector connects to both SQLServer and Snowflake and starts running.
However, the connector does not replicate any data until any tables to be replicated are explicitly added to its configuration.

To configure specific tables for replication, edit the SQLServer Ingestion Parameters context. After you apply the changes to the
SQLServer Ingestion Parameters context, the configuration is picked up by the connector, and the replication lifecycle starts for every table.

#### SQLServer Source Parameters context

| Parameter | Description |
| --- | --- |
| SQLServer Connection URL | The full JDBC URL to the source database.  Example:   * `jdbc:sqlserver://example.com:1433;encrypt=false;` |
| SQLServer JDBC Driver | Select the Reference asset checkbox to upload the [SQL Server JDBC driver](https://learn.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server). |
| SQLServer Username | The user name for the connector. |
| SQLServer Password | The password for the connector. |

See moreShow less

Expand

#### SQLServer Destination Parameters context

| Parameter | Description | Required |
| --- | --- | --- |
| Destination Database | The database where data is persisted. It must already exist in Snowflake. The name is case-sensitive. For unquoted identifiers, provide the name in uppercase. | Yes |
| Snowflake Authentication Strategy | When using:   * **Snowflake Openflow Deployment** or **BYOC**: Use SNOWFLAKE\_MANAGED\_TOKEN.   This token is managed automatically by Snowflake.   BYOC deployments must have previously configured   [runtime roles](../../setup-openflow-byoc.html#label-deployment-byoc-setup-runtime-role) to use SNOWFLAKE\_MANAGED\_TOKEN. * **BYOC:** Alternatively BYOC can use KEY\_PAIR as the value for authentication strategy. | Yes |
| Snowflake Account Identifier | When using:   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Snowflake account name formatted as [organization-name]-[account-name] where data is persisted. | Yes |
| Snowflake Connection Strategy | When using KEY\_PAIR, specify the strategy for connecting to Snowflake:   * **STANDARD** (default): Connect using standard public routing to Snowflake services. * **PRIVATE\_CONNECTIVITY**: Connect using private addresses associated with the supporting cloud platform such as AWS PrivateLink. | Required for BYOC with KEY\_PAIR only, otherwise ignored. |
| Snowflake Object Identifier Resolution | Specifies how source object identifiers such as schemas, tables, and columns names are stored and queried in Snowflake. This setting dictates whether you must use double quotes in SQL queries.  Option 1: Default, case-insensitive (recommended).   * **Transformation**: All identifiers are converted to uppercase. For   example, `My_Table` becomes `MY_TABLE`. * **Queries**: SQL queries are case-insensitive and don’t require SQL   double quotes.  For example `SELECT * FROM my_table;` returns the same results as `SELECT * FROM MY_TABLE;`.   Note  Snowflake recommends using this option if database objects are not expected to have mixed case names.  Important  Do not change this setting after connector ingestion has begun. Changing this setting after ingestion has begun breaks the existing ingestion. If you must change this setting, create a new connector instance.  Option 2: case-sensitive.   * **Transformation**: Case is preserved.   For example, `My_Table` remains `My_Table`. * **Queries**: SQL queries must use double quotes to match the exact   case for database objects.   For example, `SELECT * FROM "My_Table";`.   Note  Snowflake recommends using this option if you must preserve source casing for legacy or compatibility reasons. For example, if the source database includes table names that differ in case only, such as `MY_TABLE` and `my_table`, that result in a name collision when using case-insensitive comparisons. | Yes |
| Snowflake Private Key | When using:   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Must be the RSA private key used for authentication.  The RSA key must be formatted according to PKCS8 standards and have standard PEM headers and footers.   Note that either a Snowflake Private Key File or a Snowflake Private Key must be defined. | No |
| Snowflake Private Key File | When using:   * **Session token authentication strategy**: The private key file must be blank. * **KEY\_PAIR**: Upload the file that contains the RSA private key used for authentication to Snowflake,   formatted according to PKCS8 standards and including standard PEM headers and footers.   The header line begins with `-----BEGIN PRIVATE`.   To upload the private key file, select the Reference asset checkbox. | No |
| Snowflake Private Key Password | When using:   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Provide the password associated with the Snowflake Private Key File. | No |
| Snowflake Role | When using:   * **Session Token Authentication Strategy**: Use Snowflake Role assigned to the runtime or child role granted to this Snowflake Role.   You can find your runtime Snowflake Role in the Openflow UI, by expanding the More Options [⋮] button for your runtime and selecting Set Snowflake role. * **KEY\_PAIR** Authentication Strategy: Use a valid role configured for your service user. | Yes |
| Snowflake Username | When using:   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Provide the user name used to connect to the Snowflake instance. | Yes |
| Snowflake Warehouse | Snowflake warehouse used to run queries. | Yes |

See moreShow less

Expand

#### SQLServer Ingestion Parameters context

| Parameter | Description |
| --- | --- |
| Included Table Names | A comma-separated list of source table paths, including their databases and schemas, for example:  `database_1.public.table_1, database_2.schema_2.table_2` |
| Included Table Regex | A regular expression to match against table paths, including database and schema names. Every path matching the expression is replicated, and new tables matching the pattern that are created later are also included automatically, for example:  `database_name\.public\.auto_.*` |
| Filter JSON | A JSON containing a list of fully-qualified table names and a regex pattern for column names that should be included into replication.  The following example includes all columns that end with `name` in `table1` from the public schema in the `my_db` database:  `[ {"database":"my_db", "schema":"public", "table":"table1", "includedPattern":".*name"} ]` |
| Merge Task Schedule CRON | CRON expression defining periods when merge operations from Journal to Destination Table will be triggered. Set it to `* * * * * ?` if you want to have continuous merge or time schedule to limit warehouse run time.  For example:  * The string `* 0 * * * ?` indicates that you want to schedule merges at full hour for one minute * The string `* 20 14 ? * MON-FRI` indicates that you want to schedule merges at 2:20 PM every   Monday through Friday.  For additional information and examples, see the cron triggers tutorial in the [Quartz Documentation](https://www.quartz-scheduler.org/documentation/quartz-2.2.2/tutorials/tutorial-lesson-06.html) |

See moreShow less

Expand

### Replicate tables from a SQL Server replica server

The connector can ingest data from a primary server or from a subscriber server using
[transactional replication](https://learn.microsoft.com/en-us/sql/relational-databases/replication/transactional/transactional-replication).
Before configuring the connector to connect to a SQL Server replica, ensure that replication between the primary and replica
nodes works correctly. When investigating issues with missing data in the connector, first ensure that missing rows and
change tracking events are present in the replica server used by the connector.

To ensure continuity, make sure that the same connection user is available on both the primary and replica servers and has access to the data and change tracking tables.

To configure the connector to read from a subscriber server instead of the publisher, specify the subscriber server URL in the
SQLServer Connection URL parameter.

Warning

Do not change the database server after replication has started. Each database maintains its own change tracking
state independently, so switching to a different server would cause the connector to lose track of which changes
have already been processed, and may result in data loss.

### Restart table replication

A table in FAILED state — for example, due to a missing primary key or unsupported schema change — does not restart automatically. If a table enters a FAILED state or you need to restart replication from scratch, use the following procedure to remove and re-add the table to replication.

Note

If the failure was caused by an issue in the source table such as a missing primary key, resolve that issue in the source database before continuing.

1. Remove the table from flow parameters: In the Ingestion Parameters context, either remove the table from the Included Table Names or modify the Included Table Regex so the table is no longer matched.
2. Verify the table has been removed:

   1. In the Openflow runtime canvas, right-click a processor group and choose Controller Services.
   2. In the table listing controller services, locate the Table State Store row, click the three vertical dots on the right side of the row, then choose View State.

   Important

   You must wait until the table’s state is fully removed from this list before proceeding. Do not continue until this configuration change has completed.
3. Clean up the destination: Once the table’s state shows as fully removed, manually [DROP](../../../../../sql-reference/sql/drop-table) the destination table in Snowflake. Note that the connector will not overwrite an existing destination table during the snapshot phase; if the table still exists, replication will fail again. Optionally, the journal table and stream can also be removed if they are no longer needed.
4. Re-add the table: Update the Included Table Names or Included Table Regex parameters to include the table again.
5. Verify the restart: Check the Table State Store using the instructions given previously. The state of the table should appear with the status NEW, then transition to SNAPSHOT\_REPLICATION, and finally INCREMENTAL\_REPLICATION.

### Replicate a subset of columns in a table

The connector filters the data replicated per table to a subset of configured columns.

To apply filters to columns, modify the Column Filter property in the Replication Parameters context,
adding an array of configurations, one entry for every table to which you want to apply a filter.

Include or exclude columns by name or pattern. You can apply a single condition per table,
or combine multiple conditions, with exclusions always taking precedence over inclusions.

The following example shows the fields that are available. The `schema` and `table` fields are mandatory. One or
more of `included`, `excluded`, `includedPattern`, `excludedPattern` is required.

CopyExpand

```
[
    {
        "schema": "<source table schema>",
        "table" : "<source table name>",
        "included": ["<column name>", "<column name>"],
        "excluded": ["<column name>", "<column name>"],
        "includedPattern": "<regular expression>",
        "excludedPattern": "<regular expression>",
    }
]
```

Show lessSee more

Scroll to top

### Track data changes in tables

The connector replicates the current state of data from the source tables,
as well as every state of every row from every changeset. This data is stored in journal tables
created in the same schema as the destination table.

The journal table names are formatted as: `<source_table_name>_JOURNAL_<timestamp>_<schema_generation>`
where `<timestamp>` is the value of epoch seconds when the source table was added to replication, and `<schema_generation>` is an integer increasing with every schema change on the source table.
As a result, source tables that undergo schema changes will have multiple journal tables.

When you remove a table from replication, then add it back, the `<timestamp>` value changes, and `<schema_generation>` starts again from `1`.

Important

Snowflake recommends not altering the structure of journal tables in any way.
The connector uses them to update the destination table as part of the replication process.

The connector never drops journal tables, but uses the latest
journal for every replicated source table, only reading append-only streams on top of journals.
To reclaim the storage, you can:

* Truncate all journal tables at any time.
* Drop the journal tables related to source tables that were removed from replication.
* Drop all but the latest generation journal tables for actively replicated tables.

For example, if your connector is set to actively replicate source table `orders`,
and you have earlier removed table `customers` from replication, you may have
the following journal tables. In this case you can drop all of them *except* `orders_5678_2`.

Expand

```
customers_1234_1
customers_1234_2
orders_5678_1
orders_5678_2
```

Show lessSee more

Scroll to top

### Configure scheduling of merge tasks

The connector uses a warehouse to merge change data capture (CDC) data into destination tables.
This operation is triggered by the MergeSnowflakeJournalTable processor. If there are no new changes or if no new flow files are waiting in
the MergeSnowflakeJournalTable queue, no merge is triggered and the warehouse auto-suspends.

Use the CRON expression in the Merge task Schedule CRON parameter to limit the warehouse cost and limit merges to only scheduled time.
It throttles the flow files coming to the MergeSnowflakeJournalTable processor
and merges are triggered only in a dedicated period of time.
For more information about scheduling, see [Scheduling strategy](https://nifi.apache.org/docs/nifi-docs/html/user-guide.html#scheduling-strategy).

## Run the flow

1. Right-click on the plane and select Enable all Controller Services.
2. Right-click on the imported process group and select Start. The connector starts the data ingestion.
