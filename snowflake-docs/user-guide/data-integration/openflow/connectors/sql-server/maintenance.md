---
title: "Openflow Connector for SQL Server: Maintenance"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/sql-server/maintenance"
---

# Openflow Connector for SQL Server: Maintenance

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) [Preview Feature](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/preview-terms-of-service/)

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes maintenance considerations and best practices for the Openflow Connector for SQL Server, such as reinstalling the connector or setting the change tracking starting position.

These operations are often used in conjunction with [Incremental replication with snapshots](incremental-replication).

## Reinstall the connector

This section provides instructions on how to reinstall the connector, and continue replicating data for
the same tables without having to snapshot them again.
It covers situations where the new connector is installed in the same runtime, as well as moved to a new runtime.

### Prerequisites

Review and note connector parameter context values.
If you reinstall the connector in the same runtime, you can reuse the existing context.
If the new instance is located in a different runtime, you must re-enter all parameters.

1. Finish processing all in-flight FlowFiles in the existing connector, then stop the connector.

   1. Sign in to [Snowsight](../../../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
   2. In the navigation menu, select Ingestion » Openflow.
   3. Select Launch Openflow.
   4. In the Openflow pane select the Runtimes tab.
   5. Select the runtime containing the connector.
   6. Select the connector.
   7. Stop the topmost processor Set Tables for Replication in the Snapshot Load group.
   8. Stop the topmost processor Read SQLServer Change Tracking tables in the Incremental Load group.
   9. If you changed the value of the Merge Task Schedule CRON parameter, return it to `* * * * * ?`, otherwise queues will not be emptied until the next scheduled run.

      Wait until all FlowFiles in the connector have been processed, and all queues are empty.
      When all FlowFiles have been processed, the Queued value on the connector’s processor group becomes zero.
      If there are any items left in the original connector’s queues, there may be data gaps when the new connector starts.
   10. Stop all processors and controller services in the connector.

   Caution

   The existing connector can remain in the runtime and doesn’t interfere with the new instance, as long as it remains stopped.
2. Create a new instance of the connector. If you use the same runtime as the original connector, you can choose to keep the existing parameter contexts and reuse the settings.
3. If you install into a different runtime or you deleted the previous parameter contexts, enter the configuration settings into the new parameter contexts,
   including the table names and patterns as described in [Set up the Openflow Connector for SQL Server](setup).
4. Navigate to the `SQLServer Ingestion Parameters` context, and set the following parameters:

   * Set the `Ingestion Type` parameter to `incremental`. For information, see [Enable incremental replication without snapshots](incremental-replication.html#label-sql-server-incremental-replication).
   * Set the `Starting Change Tracking Position` parameter to `Earliest`.
     For information, see [Specify load from change tracking table position](#label-sql-server-connector-start-restart-incremental-load-from-earliest-available-change-tracking-position).
5. Start the new connector.

### Usage notes

The new connector uses the existing destination tables created by the original connector, but creates new journal tables.

## Specify load from change tracking table position

The Openflow Connector for SQL Server connector lets you select the starting position where change tracking tables are read.
By default, the connector reads from the latest available position. Alternatively, you can choose the earliest position available on the source instance.
Choosing to start from the earliest position is common when reinstalling the connector.
This allows the new instance to catch up and continue replicating existing tables without having to snapshot each again.

Switching a running connector from latest to earliest position causes the contents of change tracking tables to be re-read, re-processed, and re-applied to the destination table.

Warning

While the change tracking tables are being re-read, the data in affected destination tables
can become out of sync with their sources until all events have been re-processed and merged.

The following parameters are available in the `Ingestion Parameters` context:

| Parameter | Description |
| --- | --- |
| Starting Change Tracking Position | * `Latest` (default): change tracking table reading starts at the latest available position and continues from there. * `Earliest`: Switches the incremental load to start, or restart reading from the earliest available   change tracking table positions. |
| Re-read Tables in State | * `New` (default):   Only new tables, added after the starting position was switched to `Earliest`, will have their change   tracking tables read from the earliest available positions. Tables that started replication before the   configuration change will continue reading from their last positions. * `Any active`: Re-read and re-process changes from any table currently in replication. |

See moreShow less

Expand

To determine whether the connector finished re-reading the change tracking tables:

1. Navigate to the Openflow canvas.
2. Open the Incremental Load process group.
3. Right-click the topmost processor named Read SQLServer Change Tracking tables, then select View state.
4. Check the state entries for every table with keys starting with `position.`. If a value is `0/0` then the connector has not yet finished re-reading the changes for this table.

### Usage notes

* After you switch a running connector to read from the earliest positions and start it,
  you cannot reconfigure or cancel the process, and it will continue until the currently-read positions reach the latest values.
* Switching to the earliest position on a running connector will, for any tables being re-processed,
  finish their existing journals, and create new journal tables.
