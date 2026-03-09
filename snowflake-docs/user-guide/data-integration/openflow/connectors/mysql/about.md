---
title: "About Openflow Connector for MySQL"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/mysql/about"
---

# About Openflow Connector for MySQL

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes the basic concepts of Openflow Connector for MySQL,
its workflow, and limitations.

## About the Openflow Connector for MySQL

The Openflow Connector for MySQL connects a MySQL database instance to Snowflake and replicates data from selected tables in near real-time or on a specified schedule.
The connector also creates a log of all data changes, which is available along with the current state of the replicated tables.

## Use cases

Use this connector if you’re looking to do the following:

* CDC replication of MySQL tables into Snowflake for comprehensive, centralized reporting

## Supported MySQL versions

The following table lists the tested and officially supported MySQL versions.

|  | 8.0 | 8.4 |
| --- | --- | --- |
| [Standard](https://www.mysql.com/) | Yes | Yes |
| [AWS RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_MySQL.html) | Yes | Yes |
| [Amazon Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraMySQLReleaseNotes/Welcome.html) | Yes, as Version 3 | Not applicable. Aurora 8.4 is not currently supported. |
| [GCP Cloud SQL](https://cloud.google.com/sql/mysql?hl=en) | Yes | Yes |
| [Azure Database](https://azure.microsoft.com/en-us/products/mysql/) | Yes | Yes |

See moreShow less

Expand

## Openflow requirements

* The runtime size must be at least Medium. Use a bigger runtime when replicating large data volumes, especially when row sizes are large.
* The connector does not support multi-node Openflow runtimes. Configure the runtime for this connector with Min nodes and Max nodes set to `1`.

## Limitations

* The connector supports MySQL version 8 or later.
* The connector supports only username and password authentication with MySQL.
* Only database tables containing primary keys can be replicated.
* The connector does not replicate individual values larger than 16 MB. By default, processing such a value results in the associated table being marked permanently failed.
  To prevent table failures, modify the **Oversized Value Strategy** destination parameter.
* The connector does not replicate tables with data that exceeds
  [Snowflake’s type limitations](../../../../../sql-reference/intro-summary-data-types).
* The connector does not replicate columns of types GEOMETRY, GEOMETRYCOLLECTION, LINESTRING, MULTILINESTRING, MULTIPOINT, MULTIPOLYGON, POINT, and POLYGON.
* The connector has the [Group Replication Limitations of MySQL](https://dev.mysql.com/doc/refman/8.4/en/group-replication-limitations.html#group-replication-limitations-transaction-size).
  This means that a single transaction must fit into a binary log message of size no more than 4 GB.
* The connector does not support replicating tables from a reader instance in Amazon Aurora as Aurora reader instances do not maintain their own binary logs.
* The connector supports source table schema changes with the exception of changing primary key definitions and
  changing the precision or the scale of a numeric column.
* The connector does not support re-adding a column after it is dropped.
* For `DATE` and `DATETIME` types, any values that contain a zero month or day
  are mapped to the Unix epoch (‘1970-01-01’ or ‘1970-01-01T00:00’). Date zero (‘0000-00-00’)
  is also mapped to the Unix epoch. Values with a zero year are converted to year one, for
  example, ‘0000-05-30 7:59:59’ becomes ‘0001-05-30T7:59:59’). The remaining date and time
  components are unchanged.
* For `TIMESTAMP` type, value ‘0000-00-00 00:00:00’ is mapped to the Unix EPOCH (‘1970-01-01T00:00Z’).

Note

Limitations affecting certain table columns can be bypassed by excluding these specific columns from replication.

## Workflow

1. A **MySQL database administrator** performs the following tasks:

   > * Configure MySQL replication settings
   > * Create credentials for the connector
   > * (Optionally) Provide the SSL certificate.
2. A **Snowflake account administrator** performs the following tasks:

   1. Creates a service user for the connector, a warehouse for the connector, and a destination database for the replicated data.
   2. Installs the connector.
   3. Specifies the required parameters for the flow template.
   4. Runs the flow. The connector performs the following tasks when run in Openflow:

      1. Creates a schema for journal tables.
      2. Creates the schemas and destination tables matching the source tables configured for replication.
      3. Starts replicating the tables. For details on the replication process, see [How tables are replicated](#how-tables-are-replicated).

## How the connector works

The following sections describe how the connector works in various scenarios, including replication, changes in schema, and data retention.

### How tables are replicated

The tables are replicated in the following stages:

1. Schema introspection: The connector discovers the columns in the source table, including the column names and types,
   then validates them against Snowflake’s and the connector’s [Limitations](#limitations). Validation failures cause
   this stage to fail, and the cycle completes. After successful completion of this stage, the connector creates an empty destination table.
2. Snapshot load: The connector copies all data available in the
   source table into the destination table. If this stage fails, then
   no more data is replicated. After successful completion, the data from the source table is available in the destination table.
3. Incremental load: The connector tracks
   changes in the source table and applies those changes to the destination table.
   This process continues until the table is removed from replication. Failure at this stage
   permanently stops replication of the source table, until the issue is resolved.

   Note

   This connector can be configured to immediately start replicating incremental changes for newly added tables,
   bypassing the snapshot load phase. This option is often useful when reinstalling the connector
   in an account where previously replicated data exists and you want to continue replication without having to re-snapshot tables.

   For details on the bypassing snapshot load and using the incremental load process, see [Incremental replication](incremental-replication).

Important

Interim failures, such as connection errors, do not prevent tables from being replicated.
Permanent failures, such as unsupported data types, do prevent tables from being replicated.
If a permanent failure prevents a table from being replicated, remove the table from the list of replicated tables.
After you address the problem that caused the failure, you can add the table back to the list of replicated tables.

### Table replication status

Interim failures, such as connection errors, do not prevent table replication. However,
permanent failures, such as unsupported data types, prevent table replication.

To troubleshoot replication issues or verify that a table has been successfully removed from the replication flow, check the Table State Store:

1. In the Openflow runtime canvas, right-click a processor group and choose Controller Services. A table listing controller services displays.
2. Locate the row labeled Table State Store, click the More [![Three vertical dots indicating more options](../../../../../_images/vertical-more-icon.png)](../../../../../_images/vertical-more-icon.png) button on the right side of the row, and then choose View State.

A list of tables and their current states displays. Type in the search box to filter the list by table name. The possible states are:

* NEW: The table is scheduled for replication but replication hasn’t started.
* SNAPSHOT\_REPLICATION: The connector is copying existing data. This status displays until all records are stored in the destination table.
* INCREMENTAL\_REPLICATION: The connector is actively replicating changes. This status displays after snapshot replication ends and continues to display indefinitely until a table is either removed from replication or replication fails.
* FAILED: Replication has permanently stopped due to an error.

Note

The Openflow runtime canvas doesn’t display table status changes — only the current table status. However, table status changes are recorded in logs when they occur. Look for the following log message:

CopyExpand

```
Replication state for table <database_name>.<schema_name>.<table_name> changed from <old_state> to <new_state>
```

Show lessSee more

Scroll to top

If a permanent failure prevents table replication, remove the table from replication. After you address the problem that caused the failure, you can add the table back to replication. For more information, see [Restart table replication](setup.html#label-of-mysql-restart-table-replication).

## Understanding data retention

The connector follows a data retention philosophy where customer data is never automatically deleted.
You maintain full ownership and control over your replicated data, and the connector preserves historical
information rather than permanently removing it.

This approach has the following implications:

* Rows deleted from the source table are soft-deleted in the destination table rather than physically removed.
* Columns dropped from the source table are renamed in the destination table rather than dropped.
* Journal tables are retained indefinitely and are not automatically cleaned up.

### Destination table metadata columns

Each destination table includes the following metadata columns that track replication information:

| Column name | Type | Description |
| --- | --- | --- |
| `_SNOWFLAKE_INSERTED_AT` | TIMESTAMP\_NTZ | The timestamp when the row was originally inserted into the destination table. |
| `_SNOWFLAKE_UPDATED_AT` | TIMESTAMP\_NTZ | The timestamp when the row was last updated in the destination table. |
| `_SNOWFLAKE_DELETED` | BOOLEAN | Indicates whether the row was deleted from the source table. When `true`, the row has been soft-deleted and no longer exists in the source. |

See moreShow less

Expand

### Soft-deleted rows

When a row is deleted from the source table, the connector does not physically remove it from the
destination table. Instead, the row is marked as deleted by setting the `_SNOWFLAKE_DELETED` metadata
column to `true`.

This approach allows you to:

* Retain historical data for auditing or compliance purposes.
* Query deleted records when needed.
* Decide when and how to permanently remove data based on your requirements.

To query only active (non-deleted) rows, filter on the `_SNOWFLAKE_DELETED` column:

CopyExpand

```
SELECT * FROM my_table WHERE _SNOWFLAKE_DELETED = FALSE;
```

Show lessSee more

Scroll to top

To query deleted rows:

CopyExpand

```
SELECT * FROM my_table WHERE _SNOWFLAKE_DELETED = TRUE;
```

Show lessSee more

Scroll to top

### Dropped columns

When a column is dropped from the source table, the connector does not drop the corresponding column
from the destination table. Instead, the column is renamed by appending the `__SNOWFLAKE_DELETED` suffix
to preserve historical values.

For example, if a column named `EMAIL` is dropped from the source table, it is renamed to
`EMAIL__SNOWFLAKE_DELETED` in the destination table. Rows that existed before the column was dropped
retain their original values, while rows added after the drop have `NULL` in this column.

You can still query historical values from the renamed column:

CopyExpand

```
SELECT EMAIL__SNOWFLAKE_DELETED FROM my_table;
```

Show lessSee more

Scroll to top

### Renamed columns

Due to limitations in CDC (Change Data Capture) mechanisms, the connector cannot distinguish between
a column being renamed and a column being dropped followed by a new column being added. As a result,
when you rename a column in the source table, the connector treats this as two separate operations:
dropping the original column and adding a new column with the new name.

For example, if you rename a column from `A` to `B` in the source table, the destination table
will contain:

* `A__SNOWFLAKE_DELETED`: Contains values from before the rename. Rows added after the rename have
  `NULL` in this column.
* `B`: Contains values from after the rename. Rows that existed before the rename have `NULL`
  in this column.

#### Querying renamed columns

To retrieve data from both the original and renamed columns as a single unified column, use a
`COALESCE` or `CASE` expression:

CopyExpand

```
SELECT
    COALESCE(B, A__SNOWFLAKE_DELETED) AS A_RENAMED_TO_B
FROM my_table;
```

Show lessSee more

Scroll to top

Alternatively, using a `CASE` expression:

CopyExpand

```
SELECT
    CASE
        WHEN B IS NOT NULL THEN B
        ELSE A__SNOWFLAKE_DELETED
    END AS A_RENAMED_TO_B
FROM my_table;
```

Show lessSee more

Scroll to top

#### Creating a view for renamed columns

Rather than manually modifying the destination table, you can create a view that presents the renamed
column as a single unified column. This approach is recommended because it preserves the original data
and avoids potential issues with ongoing replication.

CopyExpand

```
CREATE VIEW my_table_unified AS
SELECT
    *,
    COALESCE(B, A__SNOWFLAKE_DELETED) AS A_RENAMED_TO_B
FROM my_table;
```

Show lessSee more

Scroll to top

Important

Manually modifying the destination table structure (such as dropping or renaming columns) is not
recommended, as it may interfere with ongoing replication and cause data inconsistencies.

### Journal tables

During incremental replication, changes from the source database are first written to journal tables
before being merged into the destination tables. The connector does not automatically remove data from
journal tables, as this data may be useful for auditing, debugging, or reprocessing purposes.

Journal tables are created in the same schema as their corresponding destination tables and follow
this naming convention:

`<TABLE_NAME>_JOURNAL_<timestamp>_<number>`

Where:

* `<TABLE_NAME>` is the name of the destination table.
* `<timestamp>` is the creation timestamp in Unix epoch format (seconds since January 1, 1970),
  ensuring uniqueness.
* `<number>` starts at 1 and increments whenever the destination table schema changes, either due to
  schema changes in the source table or modifications to column filters.

For example, if your destination table is `SALES.ORDERS`, the journal table might be named
`SALES.ORDERS_JOURNAL_1705320000_1`.

Important

Do not drop journal tables while replication is in progress. Removing an active journal table may
cause data loss or replication failures. Only drop journal tables after the corresponding source
table has been fully removed from replication.

#### Managing journal table storage

If you need to manage storage costs by removing old journal data, you can create a Snowflake task
that periodically cleans up journal tables for tables that are no longer being replicated.

Before implementing journal cleanup, verify that:

* The corresponding source tables have been fully removed from replication.
* You no longer need the journal data for auditing or processing purposes.

For information on creating and managing tasks for automated cleanup, see
[Introduction to tasks](../../../../tasks-intro).

## Next steps

Review [Openflow Connector for MySQL: Data mapping](data-mapping) to understand how the connector maps data types to Snowflake data types.

Review [Set up the Openflow Connector for MySQL](setup) to set up the connector.
