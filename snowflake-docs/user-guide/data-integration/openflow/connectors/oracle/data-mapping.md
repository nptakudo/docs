---
title: "Openflow Connector for Oracle: Data mapping"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/oracle/data-mapping"
---

# Openflow Connector for Oracle: Data mapping

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

This topic describes how Oracle data types are mapped to Snowflake data types when replicating data.

## Oracle to Snowflake data type mapping

The following table shows how Oracle data types are mapped to Snowflake data types
when replicating data.

| Oracle type | Snowflake type | Notes |
| --- | --- | --- |
| NUMBER | NUMBER | If precision is undefined, mapped to NUMBER(38, 19). If precision or scale exceeds Snowflake limitations (precision > 38 or scale > 37), the value is stored as TEXT. |
| FLOAT | FLOAT |  |
| BINARY\_FLOAT | FLOAT |  |
| BINARY\_DOUBLE | FLOAT |  |
| CHAR | TEXT |  |
| VARCHAR2 | TEXT |  |
| NCHAR | TEXT |  |
| NVARCHAR2 | TEXT |  |
| CLOB | TEXT | Supported up to the maximum entry size in Snowflake (16 MB). |
| NCLOB | TEXT | Supported up to the maximum entry size in Snowflake (16 MB). |
| LONG | TEXT |  |
| DATE | TIMESTAMP\_NTZ |  |
| TIMESTAMP | TIMESTAMP\_NTZ |  |
| TIMESTAMP WITH TIME ZONE | TIMESTAMP\_TZ |  |
| TIMESTAMP WITH LOCAL TIME ZONE | TIMESTAMP\_LTZ |  |
| INTERVAL | TEXT |  |
| INTERVAL YEAR TO MONTH | TEXT |  |
| INTERVAL DAY TO SECOND | TEXT |  |
| RAW | BINARY |  |
| LONG RAW | BINARY |  |
| BLOB | BINARY | Supported up to the maximum entry size in Snowflake (16 MB). |
| BOOLEAN | BOOLEAN |  |
| JSON | VARIANT | Supported up to the maximum entry size in Snowflake (16 MB). |
| XMLTYPE | TEXT |  |

See moreShow less

Expand

Note

Any Oracle data types not listed in this table are mapped to TEXT by default.

## Next steps

Review [Set up tasks for the Openflow Connector for Oracle](setup-tasks) to set up the connector.
