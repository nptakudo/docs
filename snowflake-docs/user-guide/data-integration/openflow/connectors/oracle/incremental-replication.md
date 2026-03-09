---
title: "Openflow Connector for Oracle: Set up incremental replication without snapshots"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/oracle/incremental-replication"
---

# Openflow Connector for Oracle: Set up incremental replication without snapshots

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

This topic describes how to configure the Openflow Connector for Oracle connector to start replicating incremental changes for newly added tables immediately, bypassing snapshots. This configuration is useful when you reinstall the connector over previously replicated data and want to continue replication without snapshotting every table again.

You can enable incremental replication on either a new or an existing connector instance.

## Enable incremental replication without snapshots on a new connector

To enable incremental replication on a new connector instance:

1. Set up the connector as described in [Install and configure the Openflow Connector for Oracle](setup-connector).
2. In the `Oracle Ingestion Parameters` context, set the `Ingestion Type` parameter to `incremental`.

## Enable incremental replication without snapshots on an existing connector

To enable incremental replication on an existing connector:

1. Sign in to [Snowsight](../../../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Ingestion » Openflow.
3. In the Openflow pane select the Runtimes tab.
4. Select the runtime containing the connector.
5. Select the connector.
6. In the `Ingestion Parameters` context, specify `Ingestion Type` = `incremental`.
7. Add new replication tables. These tables immediately switch to their incremental load.

Note

To return to replicating tables with the snapshot load, change Ingestion Type from `incremental` to `full`.

### Usage notes

* Changing the value of Ingestion Type does not impact any tables that have begun replicating data.
  Tables currently in the snapshot phase continue until the snapshot load is complete.
* While Ingestion Type is set to `incremental`, new tables added to the list of replicated tables bypass the snapshot phase.
  This includes new tables added to the source database that match the `Included Table Regex` parameter.
  Ensure that the ingestion type is set to `incremental` to bypass the snapshot phase.

  Note

  Connectors should only remain in `incremental` mode as long as required as it bypasses snapshots.
  Once customer needs for incremental updates have been satisfied the connector should be returned to `full` mode.
* For tables that bypass snapshot load, the connector creates a destination table in Snowflake,
  by executing `CREATE TABLE IF NOT EXISTS`, only if no destination table already exists.
  Tables going through the snapshot require that no destination table exist.
