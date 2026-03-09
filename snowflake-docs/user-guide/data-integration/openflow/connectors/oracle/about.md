---
title: "About Openflow Connector for Oracle"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/oracle/about"
---

# About Openflow Connector for Oracle

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

Note

The Openflow Connector for Oracle is also subject to additional terms of service beyond the standard
connector terms of service. For more information, see the
[Openflow Connector for Oracle Addendum](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/openflow-oracle-terms/).

This topic describes the basic concepts of Openflow Connector for Oracle, its workflow, and limitations.

## About the Openflow Connector for Oracle

The Openflow Connector for Oracle connects an Oracle database instance to Snowflake and replicates
data from selected tables in near real-time or on a specified schedule.
The connector also creates a log of all data changes, which is available along
with the current state of the replicated tables.

## Use cases

The connector supports the following use case:

* Replicate Oracle database tables into Snowflake for comprehensive, centralized reporting.

## Licensing models and critical constraints

The Openflow Connector for Oracle supports two distinct licensing models. You must select the correct
model before installation. Failure to select the correct model might result in deployment
failure or unintended financial commitments.

For detailed licensing terms, comparison, and configuration instructions, see
[Oracle XStream licensing](#label-oracle-xstream-licensing).

### 1. Embedded License (Snowflake-provided)

Snowflake provides the Oracle XStream license to you directly for a fee. This model
allows you to consume XStream replication without a direct contract with Oracle.
For more information, see
[Embedded license details](#label-oracle-embedded-license-details) and the
[Openflow Connector for Oracle Addendum](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/openflow-oracle-terms/).

| Term | Details |
| --- | --- |
| Billing | License and Support & Maintenance (S&M) fees are drawn from your Snowflake Capacity. |
| Commitment | Activation initiates a non-cancelable 36-month term (after the 60-day trial). |
| Lifecycle | * **Post-term (36+ months)**: After the initial 36-month term, the license fee   drops to $0, but the S&M fee continues annually. * **Lock-out risk**: If you opt-out of S&M renewal, the connector will be   permanently locked when S&M coverage ends. Unlocking the connector requires   purchasing a new Embedded License, which triggers a new 36-month commitment   at full price. |
| Management UI | All license actions (Start/Cancel Trial, Monitor Usage, Opt-out) are performed by the ORGADMIN in Snowsight under Admin » Terms » Openflow for Oracle. For step-by-step instructions, see [Openflow Connector for Oracle: Enable and manage commercial terms](manage-commercial-terms). |
| Restrictions | The following customers are ineligible:   * Public sector entities. * Customers purchasing Snowflake through the GCP Marketplace. * Customers contracted with Snowflake through a third-party reseller. |

See moreShow less

Expand

### 2. Independent license (Bring Your Own License - BYOL)

You provide your own Oracle license that includes XStream entitlements (for example, Oracle
GoldenGate license). For more information, see
[Independent license (BYOL) details](#label-oracle-byol-license-details).

| Term | Details |
| --- | --- |
| Billing | No additional licensing fees from Snowflake. Standard storage and compute costs (for example, Openflow Compute) will apply. |
| Compliance | You are solely responsible for compliance with your Oracle license. |
| Usage | Mandatory for public sector, GCP Marketplace, and reseller customers. |

See moreShow less

Expand

## Choosing an Oracle XStream licensing model

The Openflow Connector for Oracle requires a paid license for Oracle XStream services. Two licensing
models are available:

* Embedded Oracle License
* Independent Oracle License (Bring Your Own License - BYOL)

Use the following table to determine the appropriate model for your organization.

| Consideration | Embedded License | Independent License (BYOL) |
| --- | --- | --- |
| Who is it for? | Customers who need to license Oracle XStream technology and want to purchase it directly through their Snowflake agreement. | Customers who already have an Oracle GoldenGate license or another Oracle agreement that provides entitlement for XStream. |
| Billing | Billed through Snowflake based on the number of processor cores on your source Oracle DB. Involves a non-cancelable 36-month commitment. Also billed for support and maintenance services.  Additionally, standard storage and compute costs (for example, Openflow Compute) will apply. | No additional licensing or support and maintenance fees for Oracle XStream services from Snowflake. You are responsible for all licensing and compliance directly with Oracle.  Standard storage and compute costs (for example, Openflow Compute) will apply. |
| Configuration | Requires you to input your Oracle DB’s CPU core count and a processor multiplier factor in the connector parameters. | Does not require you to provide CPU core information to Snowflake. |
| Trial period | Includes a 60-day free trial for up to 16 licensed cores. Billing commences automatically on the 61st day. | No trial period is offered through Snowflake. Your use is subject to your existing Oracle agreement. |

See moreShow less

Expand

## Embedded license details

By choosing this option, you are procuring the right to use Oracle XStream technology
with the connector through Snowflake. Be aware of the following key terms:

### Billing

Oracle XStream services are billed monthly and drawn from your Snowflake capacity
balance. The fee has two components - a license fee and a Support & Maintenance
(S&M) fee. The license fee is calculated based on the number of processor cores
in your source Oracle database, multiplied by the Oracle Processor Core Factor.

### Commitment (The “Day 61” Rule)

The first 60 days are free for up to 16 licensed cores. However, activating the
connector beyond the 60-day trial initiates a non-cancelable 36-month billing term
(“Initial Term”).

* **Automatic Conversion**: Billing commences automatically on Day 61. To avoid
  charges, you must cancel the trial in the
  Admin » Terms » Openflow for Oracle dashboard before
  Day 60.
* **Lock-in**: If your Snowflake agreement is terminated during this Initial Term,
  the entire remaining balance for the Initial Term becomes due immediately.

### Post-term renewal and penalties

After the Initial Term, the license fee becomes $0 but the Support & Maintenance
(S&M) fee continues.

* **Opt-out Consequence**: You can opt-out of S&M renewal through the dashboard in
  Admin » Terms » Openflow for Oracle. However, if S&M
  coverage stops, the connector processors are locked. To resume operations, you
  must purchase a NEW Embedded License (resetting the 36-month full-price
  commitment).

### Requirements

You are responsible for accurately reporting the number of processor cores and the
correct core factor in the connector configuration. This information must be kept
current if your source database hardware changes.

### Restrictions

This option is not available for:

* Public sector entities (for example, Government and Education entities).
* Customers purchasing Snowflake through the GCP Marketplace.
* Customers contracted with Snowflake through a third-party reseller (for example, CDW, Optiv).

### Configuration

To configure the Embedded License:

* Review and accept the
  [Openflow Connector for Oracle Addendum](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/openflow-oracle-terms/)
  terms presented in the UI.
* Select the Embedded License type.
* Enter the CPU core count details for your source Oracle database:
  **Total Cores** (the total number of physical cores on the source database
  server) and **Core Factor** (the Oracle processor core factor, for example, 0.5
  for Intel processors). Consult the Oracle Processor Core Factor Table for
  the correct value.

## Independent license (BYOL) details

This option is for customers who have already licensed the necessary Oracle technology.

### Requirements

You are solely responsible for ensuring that your use of the connector complies with
the terms of your existing Oracle license agreement. Snowflake does not validate or
audit your Oracle entitlements.

### Configuration

To configure the Independent License (BYOL):

* Review and accept the
  [Openflow Connector for Oracle Addendum](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/openflow-oracle-terms/)
  terms presented in the UI.
* Select the Independent License type.

When configuring the connector, proceed without entering any core
count or billing-related information.

## Openflow requirements

The following Openflow runtime requirements apply to the Openflow Connector for Oracle:

* The runtime size must be at least Medium. Use a bigger runtime when replicating
  large data volumes, especially when row sizes are large.
* The connector does not support multi-node Openflow runtimes. Configure the
  runtime for this connector with Min nodes and Max nodes set to `1`.

## Supported Oracle versions and platforms

The following Oracle database versions and platforms are supported:

* Oracle database versions 12cR2 and later
* On-premises servers
* Oracle Exadata
* OCI VM/Bare Metal
* AWS Custom RDS for Oracle
* AWS Standard Single-tenant RDS for Oracle

## Limitations

The following limitations apply to the Openflow Connector for Oracle:

* AWS Standard Multi-tenant RDS for Oracle is not supported.
* Oracle Autonomous Databases (ATP/ADW) are not supported.
* Oracle SaaS offerings such as Oracle Fusion Cloud Applications and NetSuite
  are not supported.
* The connector requires Openflow deployment version 0.55.0 or later for BYOC.
* The Openflow runtime must be created after the required Openflow deployment
  version is installed.
* Only database tables containing primary keys can be replicated.
* The connector works within a single database/container (PDB or CDB). To
  replicate tables from multiple containers, you must configure separate
  connector instances for each container.
* The connector does not support re-adding a column after it is dropped.
* The connector does not replicate individual values larger than 16 MB.
  By default, processing such a value results in the associated table being marked permanently failed.
  To prevent table failures, modify the **Oversized Value Strategy** destination parameter.
* Schema changes (such as ALTER TABLE statements that add or drop columns) are not supported
  while re-reading the redo logs from the earliest position. If any table’s schema was
  altered between the earliest available SCN and the current position, that table should
  be removed from replication and re-added with a fresh snapshot instead.

## How the connector works

The following sections describe how the connector works in different contexts,
including replication, schema changes, and data retention.

### How tables are replicated

The tables are replicated in the following stages:

1. Schema introspection: The connector discovers the columns in the source
   table, including the column names and types, then validates them against
   Snowflake’s and the connector’s [Limitations](#limitations). Validation failures cause
   this stage to fail, and the cycle completes. After successful completion
   of this stage, the connector creates an empty destination table in Snowflake.
2. Snapshot load: The connector copies all data available in the source table
   into the destination table. If this stage fails, then no more data is
   replicated. After successful completion, the data from the source table is
   available in the destination table.
3. Incremental load: The connector tracks
   changes in the source table and applies those changes to the destination table.
   This process continues until the table is removed from replication. Failure at this stage
   permanently stops replication of the source table until the issue is resolved.

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

If a permanent failure prevents table replication, remove the table from
replication. After you address the problem that caused the failure, you can add
the table back to replication. For more information, see
[Restart table replication](setup-connector.html#label-of-oracle-restart-table-replication).

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

After reviewing this topic, consider the following next steps:

* Review [Openflow Connector for Oracle: Enable and manage commercial terms](manage-commercial-terms) to enable the connector, accept the Oracle
  XStream terms, and configure your licensing model.
* Review [Openflow Connector for Oracle: Data mapping](data-mapping) to understand how the connector maps data types
  to Snowflake data types.
* Review [Set up tasks for the Openflow Connector for Oracle](setup-tasks) to set up the connector.
