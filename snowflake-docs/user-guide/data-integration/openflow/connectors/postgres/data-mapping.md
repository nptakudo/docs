---
title: "Openflow Connector for PostgreSQL: Data mapping"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/postgres/data-mapping"
---

# Openflow Connector for PostgreSQL: Data mapping

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes how PostgreSQL data types are mapped
to Snowflake data types.

## PostgreSQL to Snowflake data type mapping

The following table shows how PostgreSQL data types are mapped to Snowflake data types
when replicating data.

| PostgreSQL type | Snowflake type | Notes |
| --- | --- | --- |
| SMALLINT / INT2 | INT |  |
| INTEGER / INT / INT4 | INT |  |
| BIGINT / INT8 | INT |  |
| SMALLSERIAL / SERIAL2 | INT |  |
| SERIAL / SERIAL4 | INT |  |
| BIGSERIAL / SERIAL8 | INT |  |
| NUMERIC / DECIMAL | NUMBER | Scale and precision are preserved within Snowflake limitations. Negative scale is converted to scale 0 with adjusted precision. |
| REAL / FLOAT4 | FLOAT |  |
| DOUBLE PRECISION / FLOAT8 | FLOAT |  |
| MONEY | FLOAT |  |
| BOOLEAN / BOOL | BOOLEAN |  |
| CHARACTER / CHAR / BPCHAR | TEXT |  |
| CHARACTER VARYING / VARCHAR | TEXT |  |
| TEXT | TEXT |  |
| BYTEA | BINARY | Supported up to the maximum entry size in Snowflake (16 MB). |
| DATE | DATE |  |
| TIME / TIME WITHOUT TIME ZONE | TIME |  |
| TIME WITH TIME ZONE / TIMETZ | TIMESTAMP\_TZ |  |
| TIMESTAMP / TIMESTAMP WITHOUT TIME ZONE | TIMESTAMP\_NTZ |  |
| TIMESTAMP WITH TIME ZONE / TIMESTAMPTZ | TIMESTAMP\_LTZ |  |
| INTERVAL | TEXT |  |
| JSON | VARIANT | Supported up to the maximum entry size in Snowflake (16 MB). |
| JSONB | VARIANT | Supported up to the maximum entry size in Snowflake (16 MB). |
| UUID | TEXT |  |
| XML | TEXT |  |
| BIT | TEXT |  |
| BIT VARYING / VARBIT | TEXT |  |
| POINT | TEXT |  |
| LINE | TEXT |  |
| LSEG | TEXT |  |
| BOX | TEXT |  |
| PATH | TEXT |  |
| POLYGON | TEXT |  |
| CIRCLE | TEXT |  |
| CIDR | TEXT |  |
| INET | TEXT |  |
| MACADDR | TEXT |  |
| MACADDR8 | TEXT |  |
| TSVECTOR | TEXT |  |
| TSQUERY | TEXT |  |
| PG\_LSN | TEXT |  |

See moreShow less

Expand

Note

Any PostgreSQL data types not listed in this table are mapped to TEXT by default.
