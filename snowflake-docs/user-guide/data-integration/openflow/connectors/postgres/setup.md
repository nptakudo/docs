---
title: "Set up the Openflow Connector for PostgreSQL"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/postgres/setup"
---

# Set up the Openflow Connector for PostgreSQL

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes the steps to set up the Openflow Connector for PostgreSQL.

Note

This connector can be configured to immediately start replicating incremental changes for newly added tables,
bypassing the snapshot load phase. This option is often useful when reinstalling the connector
in an account where previously replicated data exists and you want to continue replication without having to re-snapshot tables.

For details on the incremental load process, see [Incremental replication](incremental-replication).

## Prerequisites

1. Ensure that you have reviewed [About Openflow Connector for PostgreSQL](about).
2. Ensure that you have reviewed the [supported PostgreSQL versions](about.html#label-supported-pg-versions).
3. Recommended: Ensure that you add only one connector instance per runtime.
4. Ensure that you have [Set up Openflow - BYOC](../../setup-openflow-byoc) or [Set up Openflow - Snowflake Deployments](../../setup-openflow-spcs).
5. If using Openflow - Snowflake Deployments, ensure that you’ve reviewed [configuring required domains](../../setup-openflow-spcs-sf-allow-list)
   and have granted access to the required domains for the [PostgreSQL](../../setup-openflow-spcs-sf-allow-list.html#label-openflow-domains-used-by-openflow-connectors-postgresql) connector.
6. As a database administrator, perform the following tasks:

   1. [Configure wal\_level](#configure-wal-level)
   2. [Create a publication](#create-a-publication)
   3. Ensure that there is enough disk space on your PostgreSQL server for the WAL.
      This is because once created, a replication slot causes PostgreSQL to retain the WAL data from the position held by the replication slot,
      until the connector confirms and advances that position.
   4. Ensure that every table enabled for replication has a primary key. The key can be a single column or composite.
   5. Set the [REPLICA IDENTITY](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-ALTERTABLE-REPLICA-IDENTITY) of tables to
      `DEFAULT`. This ensures that the primary keys are represented in the WAL, and the connector can read them.
   6. Create a user for the connector. The connector requires a user with the `REPLICATION` attribute and permissions
      to SELECT from every replicated table. Create that user with a password to enter into the connector’s configuration.
      For more information on replication security, see [Security](https://www.postgresql.org/docs/current/logical-replication-security.html).
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
   2. Create a pair of secure keys (public and private). Store the private key for the user in a file to use while configuring the connector.
      Assign the public key to the Snowflake service user:

      CopyExpand

      ```
      ALTER USER <openflow_user> SET RSA_PUBLIC_KEY = 'thekey';
      ```

      Show lessSee more

      Scroll to top

      For more information, see [key-pair authentication](../../../../key-pair-auth).
   3. Designate a warehouse for the connector to use. Start with the `XSMALL` warehouse size,
      then experiment with size depending on the amount of tables being replicated, and the amount of data
      transferred. Large numbers of tables typically scale better with [multi-cluster warehouses](../../../../warehouses-multicluster),
      rather than the warehouse size.

### Configure wal\_level

Openflow Connector for PostgreSQL requires [wal\_level](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-LEVEL) to be set to `logical`.

Depending on where your PostgreSQL server is hosted, you can configure the wal\_level as follows:

|  |  |
| --- | --- |
| On premise | Execute following query with superuser or user with `ALTER SYSTEM` privilege:  CopyExpand  ``` ALTER SYSTEM SET wal_level = logical; ```  Show lessSee more  Scroll to top |
| RDS | User used by the agent needs to have the `rds_superuser` or `rds_replication` roles assigned.  You also need to set:  * `rds.logical_replication` static parameter to 1. * `max_replication_slots`, `max_connections` and `max_wal_senders` parameters according to your database and replication setup. |
| AWS Aurora | Set the `rds.logical_replication` static parameter to 1. |
| GCP | Set the following flags:  * `cloudsql.logical_decoding=on`. * `cloudsql.enable_pglogical=on`.   For more information, see [Google Cloud documentation](https://cloud.google.com/sql/docs/postgres/replication/configure-logical-replication#set-up-logical-replication-with-pglogical). |
| Azure | Set the replication support to `Logical`. For more information, see [Azure documentation](https://learn.microsoft.com/en-us/azure/postgresql/single-server/concepts-logical#set-up-your-server). |

See moreShow less

Expand

### Create a publication

Openflow Connector for PostgreSQL requires a [publication](https://www.postgresql.org/docs/current/logical-replication-publication.html#LOGICAL-REPLICATION-PUBLICATION) to be created and configured in PostgreSQL before replication starts.
You can create it for all, or a subset of tables, as well as for specific tables with specified columns only.
Make sure that every table and column that you plan to have replicated is included in the publication.
You can also modify the publication later, while the connector is running. To create and configure a publication, do the following:

1. Log in as a user with the CREATE privilege on the database and run the following query:

   * For PostgreSQL 13 and later:

     CopyExpand

     ```
     CREATE PUBLICATION <publication name> WITH (publish_via_partition_root = true);
     ```

     Show lessSee more

     Scroll to top

     The additional `publish_via_partition_root` is needed for correct replication of partitioned tables.
     To learn more about ingestion of partitioned tables see [Replicate a partitioned table](#label-postgres-connector-replicate-partitioned-table).
   * For PostgreSQL versions earlier than 13:

     CopyExpand

     ```
     CREATE PUBLICATION <publication name>;
     ```

     Show lessSee more

     Scroll to top
2. Define tables that the database agent will be able to see using:

> CopyExpand
>
> ```
> ALTER PUBLICATION <publication name> ADD TABLE <table name>;
> ```
>
> Show lessSee more
>
> Scroll to top
>
> For partitioned tables, it’s enough to just add the root partition table to the publication.
> See [Replicate a partitioned table](#label-postgres-connector-replicate-partitioned-table) for more details.
>
> Important
>
> **PostgreSQL 15 and later** support configuring publications for a specified subset of table columns. For the
> connector to support this correctly, you must use the [column filtering settings](#label-postgres-connector-replication-subset-of-columns)
> to include the same columns as set on the publication.
>
> Without this setting, the connector will behave as follows:
>
> * In the destination table, columns that are not included in the filter will be suffixed with `__DELETED`. All data
>   replicated during the snapshot phase will be retained.
> * After you add new columns to the publication, the table will be permanently failed, and you will need to restart its replication.
>
> For more information, see [ALTER PUBLICATION](https://www.postgresql.org/docs/current/sql-alterpublication.html).

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
2. Populate the required parameter values as described in [Flow parameters](#label-of-postgres-flow-parameters).

### Flow parameters

Start with setting the parameters of the PostgreSQL Source Parameters context, then the PostgreSQL Destination Parameters context.
Once this is done, you can enable the connector, and it should connect both to PostgreSQL and Snowflake and start running.
However, it will not replicate any data until any tables are explicitly added to its configuration.

To configure specific tables for replication, edit the PostgreSQL Ingestion Parameters context. Shortly after you apply the changes to the
Replication Parameters context, the configuration will be picked up by the connector, and the replication lifecycle will start for every table.

#### PostgreSQL Source Parameters context

| Parameter | Description |
| --- | --- |
| PostgreSQL Connection URL | The full JDBC URL to the source database. Example: `jdbc:postgresql://example.com:5432/public`  If you are connecting to PostgreSQL replica server, see [Replicate tables from a PostgreSQL replica server](#label-replicate-tables-from-postgres-replica). |
| PostgreSQL JDBC Driver | The path to the [PostgreSQL JDBC driver jar](https://jdbc.postgresql.org/). Download the jar from its website, then select the Reference asset checkbox to upload and attach it. |
| PostgreSQL Username | The username for the connector. |
| PostgreSQL Password | The password for the connector. |
| Publication Name | The name of the publication you created earlier. |
| Replication Slot Name | Optional. When no value is provided, the connector will create a new, uniquely-named slot. When given a value, the connector will use the existing slot, or create a new one with the provided name.  Changing the value for a running connector will restart reading the incremental change data capture (CDC) stream from the updated slot’s position. |

See moreShow less

Expand

#### PostgreSQL Destination Parameters context

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

#### PostgreSQL Ingestion Parameters context

| Parameter | Description |
| --- | --- |
| Included Table Names | A comma-separated list of table paths, including their schemas. Example: `public.my_table, other_schema.other_table`.  Select tables either by name or by Regex. If you use both, all matching tables from either option will be included.  Tables being sub-partitions are always excluded from ingestion. See [Replicate a partitioned table](#label-postgres-connector-replicate-partitioned-table) for more information. |
| Included Table Regex | A regular expression to match against table paths. Every path matching the expression will be replicated, and new tables matching the pattern that get created later will also be included automatically. Example: `public\.auto_.*`  Select tables either by name or by Regex. If you use both, all matching tables from either option will be included.  Tables being sub-partitions are always excluded from ingestion. See [Replicate a partitioned table](#label-postgres-connector-replicate-partitioned-table) for more information. |
| Column Filter JSON | Optional. A JSON containing a list of fully-qualified table names and a regex pattern for column names that should be included into replication. Example: `[ {"schema":"public", "table":"table1", "includedPattern":".*name"} ]` will include all columns that end with `name` in `table1` from the `public` schema. |
| Merge Task Schedule CRON | CRON expression defining periods when merge operations from Journal to Destination Table will be triggered. Set it to `* * * * * ?` if you want to have continuous merge or time schedule to limit warehouse run time.  For example:  * The string `* 0 * * * ?` indicates that you want to schedule merges at full hour for one minute * The string `* 20 14 ? * MON-FRI` indicates that you want to schedule merges at 2:20 PM every Monday through Friday.  For additional information and examples, see the cron triggers tutorial in the [Quartz Documentation](https://www.quartz-scheduler.org/documentation/quartz-2.2.2/tutorials/tutorial-lesson-06.html) |
| Object Identifier Resolution | Specifies how source object identifiers such as the names of schemas, tables, and columns are stored and queried in Snowflake. This setting specifies that you must use double quotes in SQL queries.  Option 1: Default, case-sensitive. For backwards compatibility.   * **Transformation**: Case is preserved.   For example, `My_Table` remains `My_Table`. * **Queries**: SQL queries must use double quotes to match the exact case for database objects.   For example, `SELECT * FROM "My_Table";`.   Note  Snowflake recommends using this option if you must preserve source casing for legacy or compatibility reasons. For example, if the source database includes table names that differ in case only–such as `MY_TABLE` and `my_table`–that would result in a name collision when using when using case-insensitive comparisons.  Option 2: Recommended, case-insensitive   * **Transformation**: All identifiers are converted to uppercase. For example, `My_Table` becomes `MY_TABLE`. * **Queries**: SQL queries are case-insensitive and don’t require SQL double quotes.   For example, `SELECT * FROM my_table;` returns the same results as `SELECT * FROM MY_TABLE;`.   Note  Snowflake recommends using this option if database objects are not expected to have mixed case names.  Important  Do not change this setting after the connector has begun ingesting data. Changing this setting after ingestion has begun breaks the existing ingestion. If you must change this setting, create a new connector instance. |

See moreShow less

Expand

### Replicate tables from a PostgreSQL replica server

The connector can ingest data from a primary server, a [hot standby replica](https://www.postgresql.org/docs/current/hot-standby.html),
or subscriber server using [logical replication](https://www.postgresql.org/docs/current/logical-replication.html).
Before configuring the connector to connect to a PostgreSQL replica, ensure that replication between primary and replica
nodes works correctly. When investigating issues with missing data in the connector, first ensure that missing rows are
present in replica server used by the connector.

Additional considerations when connecting to a standby replica:

> * Only connecting to hot standby replica is supported. Note that warm standby replicas cannot accept connections
>   from clients until they are promoted to a primary instance.
> * PostgreSQL version of the server must be >= 16.
> * [The publication](#label-postgres-connector-create-a-publication) needed by the connector must be created on
>   the primary server, not the standby server. The standby server is read-only and doesn’t allow to create publication.

If you connect to a hot standby instance and see
Trying to create the replication slot ‘<replication slot>’ timed out. If connecting to a standby instance, ensure there is some traffic on the primary PostgreSQL instance, otherwise the call to create a replication slot will never return.
error in the Openflow bulletin, or the Read PostgreSQL CDC Stream processor isn’t starting, log in to the primary PostgreSQL instance and
execute the following query:

CopyExpand

```
SELECT pg_log_standby_snapshot();
```

Show lessSee more

Scroll to top

The error occurs when there are no data changes in the primary server. As such the connector can stall while
creating a replication slot on the replica server. This results from the replica server requiring information about
running transactions from the primary server to be able to create a replication slot. Primary servers won’t send the
information while idle. The `pg_log_standby_snapshot()` function forces the primary server to send information
about running transactions to the replica server.

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
adding an array of configurations, one entry for every table you wish to apply a filter to.

Columns can be included or excluded by name or pattern. You can apply a single condition per table,
or combine multiple conditions, with exclusions always taking precedence over inclusions.

The following example shows the fields that are available. `schema` and `table` are mandatory, and then one or
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

### Replicate a partitioned table

The connector supports replication of partitioned tables for PostgreSQL servers with version >= 15. A PostgreSQL
partitioned table will be replicated into Snowflake as a single destination table.

For example, if you have a partitioned table `orders`, with sub-partitions `orders_2023`, `orders_2024`,
and configured the connector to ingest all tables matching `orders.*` pattern, then only the `orders` table
will be replicated to Snowflake, and it will include data from all sub-partitions.

To support replication of partitioned tables, ensure that [the publication](#label-postgres-connector-create-a-publication)
created in PostgreSQL has the `publish_via_partition_root` option set to `true`.

Ingestion of partitioned tables has currently the following limitations:

* When a table is attached as a partition to a partitioned table after ingestion was started, the connector won’t fetch
  data that existed in the partition table before attaching.
* When a sub-partition table is detached from the partitioned table after ingestion was started, the connector won’t
  mark the data from this sub-partition as deleted in the root partition table.
* Truncate operation on subpartitions will not mark affected records as deleted.

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

To limit the warehouse cost and limit merges to only scheduled time, use the CRON expression in the Merge task Schedule CRON parameter.
It throttles the flow files coming to the MergeSnowflakeJournalTable processor
and merges are triggered only in a dedicated period of time.
For more information about scheduling, see [Scheduling strategy](https://nifi.apache.org/docs/nifi-docs/html/user-guide.html#scheduling-strategy).

### Stop or delete the connector

When stopping or removing the connector, you have to consider the [replication slot](https://www.postgresql.org/docs/current/warm-standby.html#STREAMING-REPLICATION-SLOTS) that the connector uses.

The connector creates its own replication slot with a name starting with
`snowflake_connector_` followed by a random suffix. As the connector reads the replication stream,
it advances the slot, so that PostgreSQL can trim its WAL log and free up disk space.

When the connector is paused, the slot is not advanced, and changes to the source database keep increasing the WAL
log size. You should not keep the connector paused for extended periods of time, especially on high-traffic databases.

When the connector is removed, whether by deleting it from the Openflow canvas,
or any other means, such as deleting the whole Openflow instance, the replication slot remains in place, and must be dropped manually.

If you have multiple connector instances replicating from the same PostgreSQL database,
each instance will create its own uniquely-named replication slot. When dropping a replication slot manually, make sure
it’s the right one. You can see which replication slot is used by a given connector instance by checking the state of the `CaptureChangePostgreSQL` processor.

## Run the flow

1. Right-click on the plane and select Enable all Controller Services.
2. Right-click on the imported process group and select Start. The connector starts the data ingestion.
