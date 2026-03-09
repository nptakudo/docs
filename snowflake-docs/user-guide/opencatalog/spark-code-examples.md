---
title: "Code examples: Apache Spark™"
url: "https://docs.snowflake.com/en/user-guide/opencatalog/spark-code-examples"
---

# Code examples: Apache Spark™

Feature — Generally Available

Not available in government regions.

This section provides code examples for using Apache Spark™ to do the following tasks in Snowflake Open Catalog:

* Configure a service connection
* Use a catalog
* List catalogs
* List namespaces
* Create a namespace
* Use a namespace
* Drop a namespace
* Create a table
* Query a table
* Show table properties
* List tables
* Drop a table

## Required privileges

To perform the commands included in the code examples, the following privileges must be bestowed to the service principal you use to connect
Spark to Open Catalog:

| Command | Required privilege |
| --- | --- |
| Show Namespaces | NAMESPACE\_LIST |
| Create namespace | NAMESPACE\_CREATE |
| Use namespace | NAMESPACE\_READ\_PROPERTIES |
| Show tables | TABLE\_LIST |
| Create or replace table | * TABLE\_WRITE\_DATA * TABLE\_CREATE |
| Drop namespace | NAMESPACE\_DROP |
| Drop table | TABLE\_DROP |
| Insert into table | TABLE\_WRITE\_DATA |
| Select from table | TABLE\_READ\_DATA |

See moreShow less

Expand

## Configure a service connection

See [examples of configuring a service connection in Spark](register-service-connection.html#examples).

## Use catalog

Use the catalog `catalog1`:

CopyExpand

```
spark.sql("use catalog1").show()
```

Show lessSee more

Scroll to top

## List catalogs

List the catalogs you’re connected to:

CopyExpand

```
spark.sql("show catalogs").show()
```

Show lessSee more

Scroll to top

## List namespaces

List the namespaces for the catalog you’re connected to:

CopyExpand

```
spark.sql("show namespaces").show()
```

Show lessSee more

Scroll to top

## Create a namespace

Create the namespace `namespace1`:

CopyExpand

```
spark.sql("CREATE NAMESPACE namespace1")
```

Show lessSee more

Scroll to top

## Use a namespace

Use the namespace `namespace1`:

CopyExpand

```
spark.sql("use namespace1").show()
```

Show lessSee more

Scroll to top

## Drop a namespace

Drop the namespace `namespace1` from the catalog:

CopyExpand

```
spark.sql("DROP NAMESPACE namespace1")
```

Show lessSee more

Scroll to top

## Create a table

Create a `customers` table under the parent namespace `namespace1`:

CopyExpand

```
spark.sql ("use namespace1");
spark.sql("CREATE OR REPLACE TABLE customers (id int, custnum int) using iceberg")
```

Show lessSee more

Scroll to top

## Query a table

Query the `customers` table:

CopyExpand

```
spark.sql ("use namespace1");
spark.sql("SELECT * FROM customers").show()
```

Show lessSee more

Scroll to top

## Show table properties

Show the table properties for the `customers` table:

CopyExpand

```
spark.sql("SHOW TBLPROPERTIES customers").show(50, False)
```

Show lessSee more

Scroll to top

## List tables

List the tables for the catalog you’re connected to:

CopyExpand

```
spark.sql("show tables").show()
```

Show lessSee more

Scroll to top

## Drop a table

Drop the `customers` table under parent namespace `namespace1`:

CopyExpand

```
spark.sql ("use namespace1");
spark.sql("DROP TABLE customers")
```

Show lessSee more

Scroll to top
