---
title: "Set up tasks for the Openflow Connector for Oracle"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/oracle/setup-tasks"
---

# Set up tasks for the Openflow Connector for Oracle

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

This topic describes the overall tasks required to set up, configure, and run the Openflow Connector for Oracle.

## Prerequisites

Before you set up the Openflow Connector for Oracle, verify that the following prerequisites are met:

1. Ensure that you have reviewed [About Openflow Connector for Oracle](about).
2. Ensure that you have set up an Openflow deployment:

   * [Set up Openflow - BYOC](../../setup-openflow-byoc)
   * [Set up Openflow - Snowflake Deployment](../../setup-openflow-spcs)
3. Ensure that you add only one connector instance per runtime.

## Tasks

Perform the following tasks to set up, configure, and run the Openflow Connector for Oracle.

| Order | Task | Description | Persona |
| --- | --- | --- | --- |
| 1 | Review [Prerequisites](#label-oracle-of-connector-prerequisites) | Review and confirm all required prerequisites. | **Snowflake account administrator** |
| 2 | [Enable the connector](manage-commercial-terms.html#label-oracle-enable-service) | Accept the Oracle XStream terms to make the connector visible in the list of available connectors. | **Organization administrator (ORGADMIN)** |
| 3 | [Configure the Oracle database](setup-oracledb) | Configure the Oracle database for Openflow Connector for Oracle including replication settings and credentials. | **Oracle database administrator** |
| 4 | [Set up Snowflake](setup-snowflake) | Create the destination database, service user, role, warehouse, and key pair authentication for the Openflow Connector for Oracle. | **Snowflake account administrator** |
| 5 | [Configure the connector](setup-connector) | Install, configure, and run the Openflow Connector for Oracle connector. | **Snowflake account administrator** |
| 6 | [Set up licensing](manage-commercial-terms.html#label-oracle-license-setup) | Configure your licensing model after the connector detects your source database inventory. | **Organization administrator (ORGADMIN)** |

See moreShow less

Expand

## Next steps

* [Monitor the flow](../../monitor).
* [Maintenance](maintenance) for reinstalling the connector or changing the XStream position.
