---
title: "Create dynamic Apache Iceberg™ tables"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-create-iceberg"
---

# Create dynamic Apache Iceberg™ tables

This topic explains how to create dynamic Iceberg tables, which store query results in
the Iceberg table format.

## Create dynamic Iceberg tables

Dynamic Iceberg tables combine the benefits of dynamic tables and Snowflake-managed
Iceberg tables, offering features like external cloud storage management, automated data
transformation, and performance optimization.

Dynamic Iceberg tables integrate with data lakes, which let you store data in external
cloud storage such as AWS S3 or Azure Blob Storage while being managed by Snowflake.
These tables support ACID transactions, schema evolution, hidden partitioning, and
table snapshots.

Automated data transformation with dynamic Iceberg tables uses declarative SQL to define
the desired end state without managing intermediary steps. Snowflake handles
orchestration, scheduling, and refreshing data transformations based on your specified
data freshness targets.

Performance is optimized through incremental processing, which processes only changed
data to improve performance and reduce costs compared to full data refreshes.
Additionally, you can transition between batch processing and streaming data with a
simple command, providing flexibility in data processing workflows.

Example use cases for dynamic Iceberg tables include the following:

* **Data lake integration:** You can store large datasets cost-effectively while
  performing transformations and analytics within Snowflake, leveraging the Iceberg
  format for efficient querying and management.
* **Defining continuous data transformation pipelines:** By using dynamic tables, you
  can ensure data is always up to date without manual intervention and handle
  high-velocity data streams efficiently with incremental processing.

To create a dynamic Iceberg table, execute the [CREATE DYNAMIC ICEBERG TABLE](../sql-reference/sql/create-dynamic-table.html#label-create-dt-iceberg-syntax) SQL
statement. For example, to create a dynamic Iceberg table that reads from `my_iceberg_table`,
use the following syntax:

CopyExpand

```
CREATE DYNAMIC ICEBERG TABLE my_dynamic_iceberg_table (product_id NUMBER(10,0), product_name STRING, order_time TIMESTAMP_NTZ)
  TARGET_LAG = '20 minutes'
  WAREHOUSE = my_warehouse
  EXTERNAL_VOLUME = 'my_external_volume'
  CATALOG = 'SNOWFLAKE'
  BASE_LOCATION = 'my_iceberg_table'
  AS
    SELECT product_id, product_name, order_time FROM staging_table;
```

Show lessSee more

Scroll to top

## Future grants on dynamic Iceberg tables

To ensure access to any new dynamic Iceberg tables created in the schema, use the
[GRANT … ON FUTURE ICEBERG TABLES](../sql-reference/sql/grant-privilege.html#label-grant-privilege-schema-future-grants)
syntax without the `DYNAMIC` keyword. For example:

CopyExpand

```
GRANT <privilege> ON FUTURE ICEBERG TABLES IN SCHEMA my_schema TO ROLE my_role;
```

Show lessSee more

Scroll to top

If you use the `DYNAMIC` keyword, the grant doesn’t provide access to new dynamic
Iceberg tables created in the schema. For instance, the following command doesn’t apply
for dynamic Iceberg tables:

CopyExpand

```
GRANT <privilege> ON FUTURE DYNAMIC TABLES IN SCHEMA my_schema TO ROLE my_role;
```

Show lessSee more

Scroll to top

## Considerations and limitations

* Dynamic Iceberg tables support the same data types as regular Iceberg tables in
  Snowflake. For more information, see [Supported data types](tables-iceberg-data-types.html#label-tables-iceberg-data-type-mapping-table).
* The [Catalog](tables-iceberg.html#label-tables-iceberg-catalog-def) is an account, schema, or database parameter
  that you can configure to be implicit, just like regular Snowflake managed Iceberg tables.
* Dynamic Iceberg tables don’t currently support the `IF NOT EXISTS` clause. Using the
  `IF NOT EXISTS` clause throws an error if the target table already exists.
* Dynamic Iceberg tables are currently only supported for `CREATE` statements.
  Specifying `DYNAMIC ICEBERG` in any other command (for example,
  `ALTER DYNAMIC ICEBERG TABLE <name>`) results in an error.
* You can’t clone dynamic Iceberg tables. Additionally, cloning a database or schema
  containing a dynamic Iceberg table doesn’t clone the table to the new location.
