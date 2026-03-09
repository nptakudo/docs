---
title: "Openflow Connector for MySQL: Data mapping"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/mysql/data-mapping"
---

# Openflow Connector for MySQL: Data mapping

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes MySQL data types are mapped
to Snowflake data types.

## MySQL to Snowflake data type mapping

The following table shows how MySQL data types are mapped to Snowflake data types
when replicating data.

| MySQL type | Snowflake type | Notes |
| --- | --- | --- |
| DECIMAL / NUMERIC | NUMBER | The maximum number of digits in DECIMAL format for MySQL is 65. For Snowflake, the maximum is 38. Precision is lost when exceeded. |
| INT / INTEGER | INT |  |
| TINYINT / BOOL | INT |  |
| SMALLINT | INT |  |
| MEDIUMINT | INT |  |
| BIGINT | INT |  |
| YEAR | INT |  |
| FLOAT | FLOAT |  |
| DOUBLE | FLOAT |  |
| VARCHAR | TEXT |  |
| CHAR | TEXT | Trailing spaces are not preserved. |
| TINYTEXT | TEXT |  |
| TEXT | TEXT |  |
| MEDIUMTEXT | TEXT | Supported up to the maximum entry size in Snowflake (16 MB). |
| LONGTEXT | TEXT | Supported up to the maximum entry size in Snowflake (16 MB). |
| ENUM | TEXT | Stored as a string value. For example, for `ENUM('one', 'two')` the possible values are `'one'` and `'two'`. |
| SET | TEXT | Stored as a comma-separated string in column declaration order. For example, for `SET('one', 'two')` the possible values are `''`, `'one'`, `'two'`, and `'one,two'`. |
| BIT | TEXT | Represented as a hexadecimal string. For example: `'83060c183060c183'`. |
| DATE | DATE |  |
| DATETIME | TIMESTAMP\_NTZ |  |
| TIMESTAMP | TIMESTAMP\_TZ | Values are stored in UTC. |
| TIME | TIME |  |
| BINARY | BINARY |  |
| VARBINARY | BINARY |  |
| TINYBLOB | BINARY |  |
| BLOB | BINARY |  |
| MEDIUMBLOB | BINARY | Supported up to the maximum entry size in Snowflake (16 MB). |
| LONGBLOB | BINARY | Supported up to the maximum entry size in Snowflake (16 MB). |
| JSON | VARIANT | Supported up to the maximum entry size in Snowflake (16 MB). |

See moreShow less

Expand

Note

Any MySQL data types not listed in this table are mapped to TEXT by default.
