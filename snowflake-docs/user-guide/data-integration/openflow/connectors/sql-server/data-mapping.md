---
title: "Openflow Connector for SQL Server: Data mapping"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/sql-server/data-mapping"
---

# Openflow Connector for SQL Server: Data mapping

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) [Preview Feature](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/preview-terms-of-service/)

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes how the SQL Server data types are mapped
to Snowflake data types.

## SQL Server to Snowflake data type mapping

The following table shows how SQL Server data types are mapped to Snowflake data types
when replicating data.

| SQL Server type | Snowflake type | Notes |
| --- | --- | --- |
| TINYINT | INT |  |
| SMALLINT | INT |  |
| INT | INT |  |
| BIGINT | INT |  |
| DECIMAL | NUMBER | If precision exceeds Snowflake limitations (precision > 38), the value is stored as TEXT. |
| NUMERIC | NUMBER | If precision exceeds Snowflake limitations (precision > 38), the value is stored as TEXT. |
| SMALLMONEY | NUMBER |  |
| MONEY | NUMBER |  |
| REAL | FLOAT |  |
| FLOAT | FLOAT |  |
| BIT | BOOLEAN |  |
| CHAR | TEXT |  |
| VARCHAR | TEXT |  |
| NCHAR | TEXT |  |
| NVARCHAR | TEXT |  |
| TEXT | TEXT |  |
| NTEXT | TEXT |  |
| DATE | DATE |  |
| TIME | TIME |  |
| SMALLDATETIME | TIMESTAMP\_NTZ |  |
| DATETIME | TIMESTAMP\_NTZ |  |
| DATETIME2 | TIMESTAMP\_NTZ |  |
| DATETIMEOFFSET | TIMESTAMP\_TZ |  |
| BINARY | BINARY |  |
| VARBINARY | BINARY |  |
| IMAGE | BINARY | Supported up to the maximum entry size in Snowflake (16 MB). |
| JSON | VARIANT | Supported up to the maximum entry size in Snowflake (16 MB). |
| VECTOR | VARIANT |  |
| XML | TEXT |  |
| UNIQUEIDENTIFIER | TEXT |  |
| ROWVERSION / TIMESTAMP | TEXT |  |
| SQL\_VARIANT | TEXT |  |
| GEOGRAPHY | TEXT | Values of this type are inserted as NULL. |
| GEOMETRY | TEXT | Values of this type are inserted as NULL. |

See moreShow less

Expand

Note

Any SQL Server data types not listed in this table are mapped to TEXT by default.
