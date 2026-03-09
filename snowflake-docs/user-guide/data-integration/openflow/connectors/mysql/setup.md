---
title: "Set up the Openflow Connector for MySQL"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/mysql/setup"
---

# Set up the Openflow Connector for MySQL

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes the steps to set up the Openflow Connector for MySQL.

Note

This connector can be configured to immediately start replicating incremental changes for newly added tables,
bypassing the snapshot load phase. This option is often useful when reinstalling the connector
in an account where previously replicated data exists and you want to continue replication without having to re-snapshot tables.

For details on the incremental load process, see [Incremental replication](incremental-replication).

## Prerequisites

1. Ensure that you have reviewed [About Openflow Connector for MySQL](about).
2. Ensure that you have [Set up Openflow - BYOC](../../setup-openflow-byoc) or [Set up Openflow - Snowflake Deployments](../../setup-openflow-spcs).
3. If using Openflow - Snowflake Deployments, ensure that you’ve reviewed [configuring required domains](../../setup-openflow-spcs-sf-allow-list)
   and have granted access to the required domains for the [MySQL](../../setup-openflow-spcs-sf-allow-list.html#label-openflow-domains-used-by-openflow-connectors-mysql) connector.
4. Ensure that you have a MySQL 8 or a later version to synchronize data with Snowflake.
5. Recommended: Ensure that you add only one connector instance per runtime.
6. As a database administrator, perform the following tasks:

   1. Enable [binary logs](https://dev.mysql.com/doc/refman/8.4/en/binary-log.html), then save and configure its format as follows:

      |  |  |
      | --- | --- |
      | `log_bin` | Set to `on`.  This enables the binary log that records structural and data changes. |
      | `binlog_format` | Set to `row`.  The connector supports only row-based replication. MySQL 8.x versions may be the last ones to support this setting, and future versions will only support row-based replication.  Not applicable in GCP Cloud SQL, where it is fixed at the right value. |
      | `binlog_row_metadata` | Set to `full`.  The connector requires all row metadata to operate, most importantly, column names and primary key information.  Under Microsoft Azure Database for MySQL the `binlog_row_metadata` field is not user modifiable. Raise a Microsoft support ticket to change this value. |
      | `binlog_row_image` | Set to `full`.  The connector requires that all columns be written into the binary log.  Not applicable in Amazon Aurora, where it is fixed at the right value. |
      | `binlog_row_value_options` | Leave empty.  This option ony affects JSON columns, where it can be set to include only the modified parts of JSON documents for `UPDATE` statements. The connector requires that full documents are written into the binary log. |
      | `binlog_expire_logs_seconds` | Set to at least a few hours, or longer to ensure that the database agent can continue incremental replication after extended pauses or downtime. Snowflake recommends that you set the [binary log expiration period (binlog\_expire\_logs\_seconds)](https://dev.mysql.com/doc/refman/8.4/en/replication-options-binary-log.html#sysvar_binlog_expire_logs_seconds) to at least a few hours to ensure stable working of the connector. After binary log expiration period ends, binary log files might be automatically removed. If the integration is paused for a long period, for example due to maintenance work, and the expired binary log files are deleted during this time, Openflow will not be able to replicate the data from these files.  If you’re using scheduled replication, the value needs to be longer than the configured schedule. |

      See moreShow less

      Expand

      For example:

      CopyExpand

      ```
      log_bin = on
      binlog_format = row
      binlog_row_metadata = full
      binlog_row_image = full
      binlog_row_value_options =
      ```

      Show lessSee more

      Scroll to top
   2. Increase the value of `sort_buffer_size`.

      CopyExpand

      ```
      sort_buffer_size = 4194304
      ```

      Show lessSee more

      Scroll to top

      `sort_buffer_size` defines the amount of memory (in bytes) allocated per query thread for in-memory sorting operations, such ORDER BY.
      If the value is too small, the connector may fail with the following error message:

      `Out of sort memory, consider increasing server sort buffer size`.
      This indicates that `sort_buffer_size` should be raised.
   3. If you’re using Amazon RDS databases, then increase the retention period relevant to `binlog_expire_logs_seconds` using `rds_set_configuration`.
      For example, if you want to store binlog for 24 hours, then call `mysql.rds_set_configuration('binlog retention hours', 24)`.
   4. When using a read replica to connect, binary logging must be enabled on the replica.

      Configuration details are provided in step 4.
   5. After binary logging is enabled, configure the replica to log the events received from its source into its own binary log.

      CopyExpand

      ```
      log_replica_updates = ON
      ```

      Show lessSee more

      Scroll to top

      `log_replica_updates` allows the replica to write events received from its source to its own binary
      log, making those changes available to any databases that are replicating from it.
   6. Connect via SSL. If you’re planning to use an SSL connection to MySQL, prepare the root certificate for your database server.
      It is required during configuration.
   7. Create a user for the connector. The connector requires a user with the REPLICATION\_SLAVE and REPLICATION\_CLIENT privileges
      for reading the binary logs. Grant these privileges:

      CopyExpand

      ```
      GRANT REPLICATION SLAVE ON *.* TO '<username>'@'%'
      GRANT REPLICATION CLIENT ON *.* TO '<username>'@'%'
      ```

      Show lessSee more

      Scroll to top
   8. Grant the SELECT privilege on every replicated table:

      CopyExpand

      ```
      GRANT SELECT ON <schema>.* TO '<username>'@'%'
      GRANT SELECT ON <schema>.<table> TO '<username>'@'%'
      ```

      Show lessSee more

      Scroll to top

      For more information on replication security, see [Binary log](https://dev.mysql.com/doc/refman/8.4/en/binary-log.html).
7. As a Snowflake account administrator, perform the following tasks:

   1. Create a Snowflake user with the type as [SERVICE](../../../../../sql-reference/sql/create-user.html#label-user-type-property).
      Create a database to store the replicated data, and set up
      privileges for the Snowflake user to create objects in that database by granting the [USAGE and CREATE SCHEMA privileges](../../../../security-access-control-privileges.html#label-database-privileges).

      CopyExpand

      ```
      CREATE DATABASE <destination_database>;
      CREATE USER <openflow_user> TYPE=SERVICE COMMENT='Service user for automated access of Openflow';
      CREATE ROLE <openflow_role>;
      GRANT ROLE <openflow_role> TO USER <openflow_user>;
      GRANT USAGE ON DATABASE <destination_database> TO ROLE <openflow_role>;
      GRANT CREATE SCHEMA ON DATABASE <destination_database> TO ROLE <openflow_role>;
      CREATE WAREHOUSE <openflow_warehouse>
           WITH
               WAREHOUSE_SIZE = 'XSMALL'
               AUTO_SUSPEND = 300
               AUTO_RESUME = TRUE;
      GRANT USAGE, OPERATE ON WAREHOUSE <openflow_warehouse> TO ROLE <openflow_role>;
      ```

      Show lessSee more

      Scroll to top
   2. Create a pair of secure keys (public and private). Store the private key for the user in a file to supply to the connector’s configuration.
      Assign the public key to the Snowflake service user:

      CopyExpand

      ```
      ALTER USER <openflow_user> SET RSA_PUBLIC_KEY = 'thekey';
      ```

      Show lessSee more

      Scroll to top

      For more information, see [pair of keys](../../../../key-pair-auth).
   3. Designate a warehouse for the connector to use. Start with the `XSMALL` warehouse size,
      then experiment with size depending on the amount of tables being replicated, and the amount of data
      transferred. Large table numbers typically scale better with [multi-cluster warehouses](../../../../warehouses-multicluster),
      rather than the warehouse size.

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
2. Populate the required parameter values as described in [Flow parameters](#label-of-mysql-flow-parameters).

### Flow parameters

Start with setting the parameters of the MySQL Source Parameters context, then the MySQL Destination Parameters context.
After this is done, you can enable the connector. The connector should connect to both MySQL and Snowflake and start running .
However, the connector does not replicate any data until any tables to be replicated are explicitly added to its configuration.

To configure specific tables for replication, edit the MySQL Ingestion Parameters context. After you apply the changes to the
Replication Parameters context, the configuration is picked up by the connector, and the replication lifecycle starts for every table.

#### MySQL Source Parameters context

| Parameter | Description |
| --- | --- |
| MySQL Connection URL | The full JDBC URL to the source database. The connector uses the MariaDB driver, which is compatible with MySQL and requires the `jdbc:mariadb` prefix in the URL. If the SSL is disabled, then the connection URL should have the `allowPublicKeyRetrieval` parameter set to `true`.  Examples:   * With SSL enabled: `jdbc:mariadb://example.com:3306` * With SSL disabled: `jdbc:mariadb://example.com:3306?allowPublicKeyRetrieval=true` |
| MySQL JDBC Driver | The absolute path to the [MariaDB JDBC driver jar](https://mariadb.com/downloads/connectors/connectors-data-access/java8-connector/). The connector uses the MariaDB driver, which is compatible with MySQL. Select the Reference asset checkbox to upload the MariaDB JDBC driver.  Example: `/opt/resources/drivers/mariadb-java-client-3.5.2.jar` |
| MySQL Username | The username for the connector. |
| MySQL Password | The password for the connector. |

See moreShow less

Expand

#### MySQL Destination Parameters context

| Parameter | Description | Required |
| --- | --- | --- |
| Destination Database | The database where data will be persisted. It must already exist in Snowflake. The name is case-sensitive. For unquoted identifiers, provide the name in uppercase. | Yes |
| Snowflake Authentication Strategy | When using:   * **Snowflake Openflow Deployment** or **BYOC**: Use SNOWFLAKE\_MANAGED\_TOKEN.   This token is managed automatically by Snowflake.   BYOC deployments must have previously configured   [runtime roles](../../setup-openflow-byoc.html#label-deployment-byoc-setup-runtime-role) to use SNOWFLAKE\_MANAGED\_TOKEN. * **BYOC:** Alternatively BYOC can use KEY\_PAIR as the value for authentication strategy. | Yes |
| Snowflake Account Identifier | When using:   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Snowflake account name formatted as [organization-name]-[account-name] where data will be persisted. | Yes |
| Snowflake Connection Strategy | When using KEY\_PAIR, specify the strategy for connecting to Snowflake:   * **STANDARD** (default): Connect using standard public routing to Snowflake services. * **PRIVATE\_CONNECTIVITY**: Connect using private addresses associated with the supporting cloud platform such as AWS PrivateLink. | Required for BYOC with KEY\_PAIR only, otherwise ignored. |
| Snowflake Private Key | When using:   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Must be the RSA private key used for authentication.  The RSA key must be formatted according to PKCS8 standards and have standard PEM headers and footers.   Note that either a Snowflake Private Key File or a Snowflake Private Key must be defined. | No |
| Snowflake Private Key File | When using:   * **Session token authentication strategy**: The private key file must be blank. * **KEY\_PAIR**: Upload the file that contains the RSA private key used for authentication to Snowflake,   formatted according to PKCS8 standards and including standard PEM headers and footers.   The header line begins with `-----BEGIN PRIVATE`.   To upload the private key file, select the Reference asset checkbox. | No |
| Snowflake Private Key Password | When using   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Provide the password associated with the Snowflake Private Key File. | No |
| Snowflake Role | When using   * **Session Token Authentication Strategy**: Use Snowflake Role assigned to the runtime or child role granted to this Snowflake Role.   You can find your runtime Snowflake Role in the Openflow UI, by expanding the More Options [⋮] button for your runtime and selecting Set Snowflake role. * **KEY\_PAIR** Authentication Strategy: Use a valid role configured for your service user. | Yes |
| Snowflake Username | When using   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Provide the user name used to connect to the Snowflake instance. | Yes |
| Oversized Value Strategy | Determines how the connector handles values that exceed its internal size limits (16 MB) during replication. Possible values are:  * **Fail Table** (default): The table is marked as permanently failed, and replication stops for that table. * **Set Null**: The value is replaced with `NULL` in the destination table.   Use this to prevent table failures when it is acceptable to lose data in tables beyond the oversized value. | No |
| Snowflake Warehouse | Snowflake warehouse used to run queries. | Yes |

See moreShow less

Expand

#### MySQL Ingestion Parameters context

| Parameter | Description |
| --- | --- |
| Included Table Names | A comma-separated list of table paths, including their schemas. Example: `public.my_table, other_schema.other_table` |
| Included Table Regex | A regular expression to match against table paths. Every path matching the expression will be replicated, and new tables matching the pattern that get created later will also be included automatically. Example: `public\.auto_.*` |
| Filter JSON | A JSON containing a list of fully-qualified table names and a regex pattern for column names that should be included into replication. Example: `[ {"schema":"public", "table":"table1", "includedPattern":".*name"} ]` will include all columns that end with `name` in `table1` from the `public` schema. |
| Merge Task Schedule CRON | CRON expression defining periods when merge operations from Journal to Destination Table will be triggered. Set it to `* * * * * ?` if you want to have continuous merge or time schedule to limit warehouse run time. For example, the string `* 0 * * * ?` indicates that you want to schedule merges at full hour for one minute. The string `* 20 14 ? * MON-FRI` indicates that you want to schedule merges at 2:20 PM every Monday through Friday. For more information and examples, see the [CronTrigger tutorial](https://www.quartz-scheduler.org/documentation/quartz-2.2.2/tutorials/tutorial-lesson-06.html). |
| Object Identifier Resolution | Specifies how source object identifiers such as the names of schemas, tables, and columns are stored and queried in Snowflake. This setting specifies that you must use double quotes in SQL queries.  Option 1: Default, case-sensitive. For backwards compatibility.   * **Transformation**: Case is preserved.   For example, `My_Table` remains `My_Table`. * **Queries**: SQL queries must use double quotes to match the exact case for database objects.   For example, `SELECT * FROM "My_Table";`.   Note  Snowflake recommends using this option if you must preserve source casing for legacy or compatibility reasons. For example, if the source database includes table names that differ in case only–such as `MY_TABLE` and `my_table`–that would result in a name collision when using when using case-insensitive comparisons.  Option 2: Recommended, case-insensitive   * **Transformation**: All identifiers are converted to uppercase. For example, `My_Table` becomes `MY_TABLE`. * **Queries**: SQL queries are case-insensitive and don’t require SQL double quotes.   For example, `SELECT * FROM my_table;` returns the same results as `SELECT * FROM MY_TABLE;`.   Note  Snowflake recommends using this option if database objects are not expected to have mixed case names.  Important  Do not change this setting after the connector has begun ingesting data. Changing this setting after ingestion has begun breaks the existing ingestion. If you must change this setting, create a new connector instance. |

See moreShow less

Expand

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

The connector can filter the data replicated per table to a subset of configured columns.

To apply filters to columns, modify the Column Filter property in the Replication Parameters context,
adding an array of configurations, one entry for every table to which you want to apply a filter.

Columns can be included or excluded by name or pattern. You can apply a single condition per table,
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

The connector replicates not only the current state of data from the source tables,
but also every state of every row from every changeset. This data is stored in journal tables
created in the same schema as the destination table.

The journal table names are formatted as: `<source_table_name>_JOURNAL_<timestamp>_<schema_generation>`
where `<timestamp>` is the value of epoch seconds when the source table was added to replication, and `<schema_generation>` is an integer increasing with every schema change on the source table.
As a result, source tables that undergo schema changes will have multiple journal tables.

When a table is removed from replication, then added back, the `<timestamp>` value will change, and `<schema_generation>` will start again from `1`.

Important

Snowflake recommends that you do not alter the structure of journal tables in any way.
They are used by the connector to update the destination table as part of the replication process.

The connector never drops journal tables, but does make use of the latest
journal for every replicated source table, only reading append-only streams on top of journals.
To reclaim the storage, you can:

* Truncate all journal tables at any time.
* Drop the journal tables related to source tables that were removed from replication.
* Drop all but the latest generation journal tables for actively replicated tables.

For example, if your connector is set to actively replicate source table `orders`, and you have earlier removed table `customers` from replication, you may have the following journal tables. In this case you can drop all of them *except* `orders_5678_2`.

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

To limit the warehouse cost and limit merges to only scheduled time, use the CRON expression in the Merge task Schedule CRON parameter.
It throttles the flow files coming to the MergeSnowflakeJournalTable processor
and merges are triggered only in a dedicated period of time.
For more information about scheduling, see [Scheduling strategy](https://nifi.apache.org/docs/nifi-docs/html/user-guide.html#scheduling-strategy).

## Run the flow

1. Right-click on the plane and select Enable all Controller Services.
2. Right-click on the imported process group and select Start. The connector starts the data ingestion.
