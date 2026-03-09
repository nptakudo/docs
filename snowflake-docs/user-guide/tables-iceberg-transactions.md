---
title: "Transactions and Apache Iceberg™ tables"
url: "https://docs.snowflake.com/en/user-guide/tables-iceberg-transactions"
---

# Transactions and Apache Iceberg™ tables

This topic provides information about how Snowflake specifically handles transactions for Apache Iceberg™ tables.
The rules described in the Snowflake [Transactions](../sql-reference/transactions) topic also apply to Iceberg tables.

## Tables that use Snowflake as the catalog

For a [table that uses Snowflake as the catalog](tables-iceberg.html#label-tables-iceberg-snowflake-as-catalog),
Snowflake manages the Iceberg metadata so that other query engines, such as Spark, can read from the table.

### Queries

When you use Snowflake to query this type of table, the table follows the general Snowflake transaction principles.

Snowflake currently supports [read committed isolation](../sql-reference/transactions.html#label-transactions-read-committed-isolation) for transactions
for better concurrency and throughput, while Iceberg currently supports serializable or snapshot isolation.

### DDL statements

Snowflake processes [DDL](../sql-reference/sql-ddl-summary) statements as individual transactions and doesn’t isolate
DDL statements across multiple concurrent transactions. For more information, see [DDL in implicit transactions](../sql-reference/transactions.html#label-transactions-ddl).

This differs from how Iceberg tables typically handle transactions with DDL statements,
where a single committed transaction can include both [DML](../sql-reference/sql-dml) and DDL statements, or multiple bundled DDL statements.

Note

* The Iceberg metadata doesn’t always show a new schema version for each individual DDL change. In some instances,
  Snowflake groups DDL statements together and records the group as a single new schema version in the Iceberg metadata.
* DDL changes might appear out of order in the Iceberg metadata, especially if a DDL change occurs in close proximity to other DDL or DML operations.

### Writes from external engines to Snowflake-managed tables

Snowflake doesn’t currently support writes to Snowflake-managed tables from external query engines, such as Spark.

## Tables that use an external catalog

For an Iceberg table that uses an external catalog,
Snowflake retrieves the latest table state from the external catalog when you run the [ALTER ICEBERG TABLE … REFRESH](../sql-reference/sql/alter-iceberg-table-refresh) command.

### Refresh transactions

Snowflake automatically commits ALTER ICEBERG TABLE … REFRESH statements inside a single-statement transaction.

In an [implicit transaction](../sql-reference/transactions.html#label-transactions-starting-and-ending-implicitly),
Snowflake processes the statement in the same way it handles any other statement when [AUTOCOMMIT](../sql-reference/transactions.html#label-txn-autocommit) is enabled.

In an [explicit transaction](../sql-reference/transactions.html#label-transactions-starting-and-ending-explicitly) (with multiple statements),
Snowflake executes and automatically commits the refresh as a single-statement transaction before committing the explicit transaction block.

### Writes to externally managed tables

Snowflake supports writes to externally managed tables that use a remote Iceberg REST catalog.
For more information, see [Write support for externally managed Apache Iceberg™ tables](tables-iceberg-externally-managed-writes).

## Multi-statement transactions

Snowflake supports multi-statement transactions by committing multiple DML statements atomically, and uses the following logic:

* Each DDL statement executes as an individual transaction when encountered.
* Each ALTER ICEBERG TABLE … REFRESH operation executes as a single transaction when encountered.
* All other statements within an explicit or implicit transaction are grouped and committed as a single transaction

Consider the following example of an explicit transaction block for an Iceberg table in Snowflake:

CopyExpand

```
BEGIN
  INSERT INTO table1 VALUES (1, "One");
  INSERT INTO table1 VALUES (2, "Two");
  ALTER ICEBERG TABLE table1 ALTER COLUMN c3 SET DATA TYPE ARRAY(long);
  INSERT INTO table1 VALUES (3, "Three");
  INSERT INTO table1 VALUES (4, "Four");
COMMIT;
```

Show lessSee more

Scroll to top

1. When Snowflake encounters the ALTER ICEBERG TABLE statement,
   it commits the first two INSERT INTO TABLE statements (everything processed so far) as a transaction.
2. Snowflake then commits the ALTER ICEBERG TABLE statement as a separate transaction.
3. Finally, Snowflake creates a new transaction and processes the remaining INSERT INTO statements.
   Because the rest of the block contains no DDL or refresh statements, it commits the remaining transactions at the end of the block (at COMMIT).
