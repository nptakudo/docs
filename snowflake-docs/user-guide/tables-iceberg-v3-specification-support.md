---
title: "Apache Iceberg™ tables: Support for Apache Iceberg™ v3 (Preview)"
url: "https://docs.snowflake.com/en/user-guide/tables-iceberg-v3-specification-support"
---

# Apache Iceberg™ tables: Support for Apache Iceberg™ v3 (*Preview*)

[![Snowflake logo in black (no text)](../_images/logo-snowflake-black.png)](../_images/logo-snowflake-black.png) [Preview Feature](../release-notes/preview-features) — Open

Available to all accounts.

This preview introduces support for v3 of the Apache Iceberg™ specification, but with some considerations and limitations. Unless
otherwise noted, both Snowflake-managed and externally managed Iceberg tables are supported in this preview.

## Supported Iceberg v3 features

This section lists the Iceberg v3 features that are supported in this preview.

### Data types

The following v3 data types are supported in the public preview:

* `geography`
* `geometry`
* `nanosecond`
* `variant`

For more information, see [Iceberg v3 data types](tables-iceberg-data-types.html#label-tables-iceberg-v3-data-types).

### Default values

See [Default values](tables-iceberg-manage.html#label-tables-iceberg-default-values).

### Deletion vectors

See [Deletion vectors](tables-iceberg-manage.html#tables-iceberg-deletion-vectors).

### Row lineage

See [Row lineage](tables-iceberg-manage.html#label-tables-iceberg-row-lineage).

## Configure the default Iceberg version

Iceberg tables inherently have a format version that they conform to. For externally managed Iceberg tables in a standard Snowflake database,
Snowflake retrieves this version from the table’s metadata.

For the following Iceberg tables, the table owner must specify which Iceberg version the table should conform to:

* Snowflake-managed Iceberg tables
* Externally managed Iceberg tables that you create in a [catalog-linked database](tables-iceberg-catalog-linked-database)

The system default Iceberg format version in Snowflake is v2 but you can set it to v3, if needed. To set the Iceberg version to v3, perform one of the following actions:

* Use the ICEBERG\_VERSION\_DEFAULT parameter to set the Iceberg version to `3` at the account, database, or schema level. For more information,
  see [ICEBERG\_VERSION\_DEFAULT](../sql-reference/parameters.html#label-iceberg-version-default).
* Specify `ICEBERG_VERSION = 3` in your CREATE ICEBERG TABLE statement.

  Note

  If you don’t specify an Iceberg version when you create an Iceberg table, the table defaults to the Iceberg version set for the
  schema, database, or account. The schema takes precedence over the database, and the database takes precedence over the account.

Caution

Before you use other engines to upgrade an Iceberg tables format-version in table properties to v3, ensure that the table isn’t used by
engines or applications that don’t yet support v3. Downgrading format versions isn’t supported in the Apache Iceberg specification. Therefore, all
readers and writers must support v3. The default version for Iceberg tables in Snowflake is v2, which can be configured to v3 if
needed. Using Snowflake to perform in-place version upgrades isn’t supported at this time.

### Usage notes

* To modify the ICEBERG\_VERSION\_DEFAULT parameter at the account level, you must be an account administrator; that is, you must be a user
  with the ACCOUNTADMIN role.
* To modify the ICEBERG\_VERSION\_DEFAULT parameter at the database or schema level, the role used to perform the operation must have the OWNERSHIP
  privilege on the respective database or schema.

### Examples

Specify that new Iceberg tables in the `my_db` database should be created using v3:

CopyExpand

```
ALTER DATABASE my_db SET ICEBERG_VERSION_DEFAULT=3;
```

Show lessSee more

Scroll to top

Create a new externally managed Iceberg table with v3. The column definitions included with the command indicate that a new table
will be created, or an existing table will be replaced, in the remote catalog. The table is successfully created because this is a new
table that doesn’t have an existing version.

CopyExpand

```
CREATE OR REPLACE ICEBERG TABLE my_iceberg_v3_table (
    boolean_col boolean,
    int_col int,
    long_col long,
  )
  CATALOG='my_catalog_integration'
  ICEBERG_VERSION=3;
```

Show lessSee more

Scroll to top

Create an externally managed Iceberg table with v3 from an existing table with Iceberg metadata. The lack of a column definitions
or format version in this example indicates that this table already exists and the column specification and format version will be inferred from
Iceberg metadata from the remote catalog. This example uses
[catalog-vended credentials](tables-iceberg-configure-catalog-integration-vended-credentials), so
the EXTERNAL\_VOLUME parameter is excluded from the CREATE ICEBERG TABLE statement:

CopyExpand

```
CREATE OR REPLACE ICEBERG TABLE my_iceberg_v3_table
  CATALOG = 'my_catalog_integration'
  CATALOG_TABLE_NAME = 'my_table'
  AUTO_REFRESH = TRUE;
```

Show lessSee more

Scroll to top

Note

You can’t use the ALTER ICEBERG TABLE command to change the format version for an existing table.

## Get the format version for Iceberg tables

* The following example shows how to get the Iceberg version for a specific table:

  CopyExpand

  ```
  SHOW PARAMETERS LIKE 'ICEBERG_VERSION' IN TABLE my_v3_iceberg_table;
  ```

  Show lessSee more

  Scroll to top

  Output:

  Expand

  ```
  +-----------------+-------+---------+-------+---------------------------------------------------+--------+
  | key             | value | default | level | description                                       | type   |
  +-----------------+-------+---------+-------+---------------------------------------------------+--------+
  | ICEBERG_VERSION | 3     | 2       | TABLE | Specifies the Iceberg table format version to ... | NUMBER |
  +-----------------+-------+---------+-------+---------------------------------------------------+--------+
  ```

  Show lessSee more

  Scroll to top
* The following example shows how to get the Iceberg version for a specific table by using the [GET\_DDL](../sql-reference/functions/get_ddl) function
  to retrieve the Iceberg table definition:

  CopyExpand

  ```
  SELECT GET_DDL('ICEBERG_TABLE', 'my_v3_iceberg_table');
  ```

  Show lessSee more

  Scroll to top

  Output:

  Expand

  ```
   CREATE ICEBERG TABLE my_v3_iceberg_table (
    record VARIANT,
    event_timestamp TIMESTAMP_LTZ(6)
  )
    CATALOG = 'SNOWFLAKE'
    EXTERNAL_VOLUME = 'my_external_volume'
    BASE_LOCATION = 'my_iceberg_table'
    ICEBERG_VERSION = 3;
  ```

  Show lessSee more

  Scroll to top

## Considerations and limitations for Iceberg v3 features

Consider the following information when you use Iceberg v3 features:

### Unsupported Snowflake features

The following Snowflake features aren’t supported in this preview for Iceberg v3:

* Append-only streams on externally managed Iceberg tables
* [dbt Projects on Snowflake](data-engineering/dbt-projects-on-snowflake)
* [Schema inference](../sql-reference/functions/infer_schema.html#label-iceberg-infer-schema-column-definitions)
* [Snowpipe Streaming classic architecture](snowpipe-streaming/snowpipe-streaming-classic-overview)
* SnowGov Regions
* For tables that use an external catalog, you can’t create Iceberg v3 tables with structured type columns, which includes OBJECT, ARRAY,
  or MAP. For example, you can’t use CREATE ICEBERG TABLE … AS SELECT (CTAS) to create an externally managed Iceberg v3 table with
  structured type columns.

  You can create Snowflake-managed Iceberg v3 tables with structured type columns.
* An in-place upgrade of a Snowflake-managed Iceberg table from v2 to v3, which includes cloning a v2 table, and then upgrading the clone to v3

  Important

  If you use Apache Spark to upgrade an externally managed Iceberg table from v2 to v3, you must use a commit that creates a new
  snapshot, such as DML operations. Otherwise, if the format-version is updated in table properties without a new snapshot, Snowflake’s
  manual and automated refresh for the table will fail until a new snapshot is created.

  The following example uses Apache Spark to upgrade an externally managed Iceberg table from v2 to v3:

  CopyExpand

  ```
  ALTER TABLE table_name SET TBLPROPERTIES('format-version'='3');
  ```

  Show lessSee more

  Scroll to top

Note

* The list of unsupported features isn’t finalized and is subject to change in the future. The list will be updated, as needed,
  to reflect the latest unsupported features.
* For considerations and limitations
  specific to a v3 feature, see the [feature topic](#label-apache-iceberg-specification-supported-v3-features) for a feature.

### Supported Snowflake features

Features that aren’t listed in the [Unsupported Snowflake features](#label-tables-iceberg-v3-unsupported-features) section are
supported. Supported features include those in the following list:

| Feature | Notes |
| --- | --- |
| [Auto-fulfillment for listings](../collaboration/provider-listings-auto-fulfillment) |  |
| [Automated refresh](tables-iceberg-auto-refresh) |  |
| [Catalog integrations](tables-iceberg-configure-catalog-integration) |  |
| [Catalog-linked databases](tables-iceberg-catalog-linked-database) |  |
| [Cloning](../sql-reference/sql/create-clone.html#label-create-clone-iceberg-tables-syntax) |  |
| Clustering | Snowflake-managed Iceberg v3 only. |
| [Converting externally managed v3 tables to Snowflake-managed](tables-iceberg-conversion) | Supported with the following considerations:   * Iceberg partitioning remains intact when you convert a v3 Iceberg table. * Before conversion, Snowflake never deletes any metadata, manifest lists, or manifests from your external storage. * During conversion, Snowflake doesn’t rewrite any metadata or Parquet data files. * After conversion, Snowflake is the catalog that is fully responsible for the lifecycle management of the table. Snowflake   deletes metadata, manifest lists, manifests, and data files, either created before or after conversion from your external storage   after they expire and pass the retention window. |
| [COPY INTO <table>](../sql-reference/sql/copy-into-table) | LOAD\_MODE = FULL\_INGEST or ADD\_FILES COPY are supported with the following considerations:   * To load the row lineage metadata columns in Parquet files   (`_row_id` and `_last_updated_sequence_number`), you must use the FULL\_INGEST option. The other   LOAD\_MODE methods aren’t supported. However, Parquet files containing row lineage are likely already part of an Iceberg v3 table.   Registering Parquet files by using ADD\_FILES\_COPY isn’t recommended if those files are already part of another Iceberg table. The best   practice for converting externally-managed Iceberg tables to Snowflake-managed Iceberg tables without rewriting files is to use the   [ALTER ICEBERG TABLE … CONVERT TO MANAGED](../sql-reference/sql/alter-iceberg-table-convert-to-managed) command. |
| [COPY INTO <location>](../sql-reference/sql/copy-into-location) | Supported with the following limitations:   * VARIANT, GEOMETRY, and GEOGRAPHY are unloaded as JSON-encoded strings. * TIMESTAMP\_NTZ(9) is unloaded as milliseconds, not nanoseconds. * TIMESTAMP\_LTZ(9), ARRAY, OBJECT, and MAP must be casted to other data types. |
| [Data Clean Rooms](cleanrooms/introduction) |  |
| [Data lineage](ui-snowsight-lineage.html#label-ui-lineage-supported-objects) |  |
| Data protection policies | The following data protection policies are supported:   * Masking policies * Row access policies * Projection policies * Aggregation policies * Privacy policies * Join policies |
| [Data protection policy enforcement from Apache Spark](tables-iceberg-query-using-external-query-engine-snowflake-horizon-enforce-access-policies) |  |
| Data quality monitoring |  |
| [Dynamic tables](dynamic-tables-create-iceberg) | Write a v3 externally managed Iceberg table as the target of a dynamic table. |
| [Horizon Iceberg REST Catalog API](tables-iceberg-query-using-external-query-engine-snowflake-horizon) |  |
| [LOB (Large Object)](../release-notes/bcr-bundles/2025_03/bcr-1942) |  |
| [Materialized Views](views-materialized) |  |
| [Object tagging](object-tagging/introduction) |  |
| Query acceleration |  |
| [Replication](account-replication-intro) |  |
| [Search optimization](search-optimization-service) |  |
| [Secure views](views-secure) |  |
| Sensitive Data Classification |  |
| [Target file size](tables-iceberg-manage.html#label-tables-iceberg-target-file-size) |  |
| [Single-argument Iceberg partitioning](tables-iceberg-metadata.html#label-tables-iceberg-partitioning) | Partitioned tables can’t also write deletion vectors; only copy-on-write is supported for partitioned tables. |
| [Snowflake Connector for Kafka](kafka-connector) | Versions 4.0 or newer. |
| [Snowpark](https://docs.snowflake.com/en/developer-guide/snowpark/reference/python/latest/snowpark/api/snowflake.snowpark.DataFrameWriter.saveAsTable) | 1.33.0 or newer. |
| Snowpark pandas API method [to\_iceberg](https://docs.snowflake.com/en/developer-guide/snowpark/reference/python/1.45.0/modin/pandas_api/modin.pandas.to_iceberg) | Only supported for Iceberg v3 when ICEBERG\_VERSION\_DEFAULT is set on the account, database, or schema. If ICEBERG\_VERSION = 3 is set at the table level, Snowpark pandas API method to\_iceberg isn’t supported. |
| [Snowpark Connect for Apache Spark](../developer-guide/snowpark-connect/snowpark-connect-overview) | Writing dataframes to existing Iceberg v3 tables by using an append or overwrite method is supported. Creating a new Iceberg v3 table isn’t supported. |
| [Snowpipe](data-load-snowpipe-intro) |  |
| [Snowpipe Streaming high-performance architecture](snowpipe-streaming/snowpipe-streaming-high-performance-overview) |  |
| [Sharing](data-sharing-intro) |  |
| [Streams](streams-intro) | * Append-only streams and standard streams are supported on Snowflake-managed Iceberg v3 tables. * Insert-only streams and standard streams are supported on externally managed Iceberg v3 tables.    + To have standard streams produce the correct results, the external engine must write to Iceberg v3 tables with respect to the Iceberg v3     specification. Specifically, newly inserted rows should have `_row_id=NULL`. Rows that are copied during copy-on-write should maintain the `_row_id`.   + MAX\_DATA\_EXTENSION\_TIME\_IN\_DAYS doesn’t work on externally managed Iceberg v3 tables. * When DMLs are committed over multi-statement transactions, append-only streams on Iceberg v3 tables have different semantics compared to Iceberg v2 tables:    + On Iceberg v2, for append-only streams, if a row is added and then deleted in a multi-statement transaction, this row is considered an     insertion.   + On Iceberg v3, for append-only streams, this row isn’t treated as an insertion. |
| [Table optimization](tables-iceberg-manage.html#label-tables-iceberg-configure-table-optimization-snowflake-managed) |  |

See moreShow less

Expand

### Unsupported Iceberg v3 features

The following features from the Iceberg v3 specification aren’t supported:

* Nested variant
* Multi-argument transforms for partitioning and sorting
* Table encryption keys
* UNKNOWN data type

## Examples: Support for v3 with existing Snowflake features

This section lists examples of the existing Snowflake features that are supported with v3. A feature listing includes an example for a
Snowflake-managed table and an externally managed table, when supported.

For the full list of Snowflake features that are supported in this preview for Iceberg v3,
see [Supported Snowflake features](#label-iceberg-v3-supported-snowflake-features).

### Create a v3 Iceberg table

The following example creates a Snowflake-managed Apache Iceberg™ table that conforms to v3 of the Apache Iceberg™ specification:

CopyExpand

```
CREATE ICEBERG TABLE my_v3_iceberg_table (
  record VARIANT,
  event_timestamp TIMESTAMP_LTZ(6)
)
  CATALOG = 'SNOWFLAKE'
  EXTERNAL_VOLUME = 'my_external_volume'
  BASE_LOCATION = 'my_iceberg_table'
  ICEBERG_VERSION = 3;
```

Show lessSee more

Scroll to top

The following example creates an Apache Iceberg™ table that uses a remote Iceberg REST catalog and conforms to v3 of the Apache Iceberg™ specification:

Note

You don’t need to specify `ICEBERG_VERSION = 3` with the command because the format version is already defined in the
external catalog’s metadata, so Snowflake retrieves this version from the metadata.

CopyExpand

```
CREATE ICEBERG TABLE my_v3_iceberg_table
  EXTERNAL_VOLUME = 'my_external_volume'
  CATALOG = 'my_rest_catalog_integration'
  CATALOG_TABLE_NAME = 'my_remote_table'
  AUTO_REFRESH = TRUE;
```

Show lessSee more

Scroll to top

The following example creates a writable Iceberg table in a
[catalog-linked database](tables-iceberg-catalog-linked-database)
with column definitions and conforms to v3 of the Apache Iceberg™ specification:

CopyExpand

```
USE DATABASE my_catalog_linked_db;

USE SCHEMA 'my_namespace';

CREATE OR REPLACE ICEBERG TABLE my_iceberg_table (
  first_name string,
  last_name string,
  amount int,
  create_date date
)
  ICEBERG_VERSION = 3;
```

Show lessSee more

Scroll to top

### Write to a v3 Iceberg table

DML commands INSERT, UPDATE, DELETE, MERGE, TRUNCATE TABLE, and COPY INTO are supported for writing to Snowflake-managed and
[externally managed](tables-iceberg-externally-managed-writes.html#label-tables-iceberg-externally-managed-write-operations) Iceberg v3 tables:

The following example inserts a row into an Apache Iceberg™ table that conforms to v3 of the Apache Iceberg™ table specification:

CopyExpand

```
INSERT INTO my_v3_iceberg_table (id, payload) VALUES (1, PARSE_JSON('{"name": "Alice", "age": 30}'));
```

Show lessSee more

Scroll to top

The following example loads files into an Apache Iceberg™ table that conforms to v3 of the Apache Iceberg™ table specification:

CopyExpand

```
COPY INTO my_v3_iceberg_table
  FROM @my_json_stage
  FILE_FORMAT = 'my_json_format'
  MATCH_BY_COLUMN_NAME = CASE_SENSITIVE;
```

Show lessSee more

Scroll to top

### Load data by using Snowpipe

The following example loads data from files for Iceberg v3 tables, for both Snowflake-managed and externally managed tables:

CopyExpand

```
CREATE PIPE mypipe
  AUTO_INGEST = TRUE
  INTEGRATION = 'MYINT'
  AS
  COPY INTO snowpipe_db.public.my_v3_iceberg_table
  FROM @snowpipe_db.public.mystage
  FILE_FORMAT = (TYPE = 'JSON');
```

Show lessSee more

Scroll to top

Note

Snowflake supports additional write features for Iceberg v3. For this list, see the
[considerations and limitations for Iceberg v3 features](#iceberg-v3-considerations-limitations), and
then see the supported Snowflake features list.

### Create a v3 dynamic Iceberg table

The following example writes a v3 Snowflake-managed Iceberg table as the output of a dynamic table:

CopyExpand

```
CREATE DYNAMIC ICEBERG TABLE my_dynamic_iceberg_v3_table (
    num_orders NUMBER(10,0),
    order_day
  )
  TARGET_LAG = '20 minutes'
  WAREHOUSE = my_warehouse
  EXTERNAL_VOLUME = 'my_external_volume'
  CATALOG = 'SNOWFLAKE'
  BASE_LOCATION = 'my_dynamic_iceberg_v3_table'
  ICEBERG_VERSION = 3
  AS
    SELECT
        COUNT(DISTINCT order_id)
        DATE_TRUNC('DAY', order_timestamp_ns) AS order_day
      FROM staging_v3_iceberg_table;
```

Show lessSee more

Scroll to top

Note

Writing either a v2 or v3 externally managed Iceberg table as the target of a dynamic table isn’t supported. The output of a dynamic
Iceberg table can only be Snowflake-managed.

### Query a v3 Iceberg table

The following example queries a Snowflake-managed or externally managed Iceberg v3 table:

> CopyExpand
>
> ```
> SELECT * FROM MY_DB.MY_SCHEMA.MY_ICEBERG_V3_TABLE;
> ```
>
> Show lessSee more
>
> Scroll to top
