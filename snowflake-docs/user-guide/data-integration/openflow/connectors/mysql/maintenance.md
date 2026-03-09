---
title: "Openflow Connector for MySQL: Maintenance"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/mysql/maintenance"
---

# Openflow Connector for MySQL: Maintenance

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes important maintenance considerations and best practices for
maintaining the Openflow Connector for MySQL such as reinstalling the connector or setting the starting binary log position for loading.

These operations are often used in conjunction with [Incremental replication with snapshots](incremental-replication).

## Reinstall the connector

This section provides instructions on how to reinstall the connector, and continue replicating data for
the same tables without having to snapshot them again.
It covers situations where the new connector is installed in the same runtime, as well as moved to a new runtime.

Warning

For the connector to continue replicating from the same CDC stream position where it stopped before reinstallation,
the source database must retain the binary log long enough to cover the time since the prior connector was stopped
and the new connector is started.
Make sure the `binlog_expire_logs_seconds` parameter of the MySQL server is high enough, and keep the reinstallation time to a minimum.

The value of `binlog_expire_logs_seconds` needs to be longer than the expected time expected to reinstall the connector.
Typically 86400s, a day is seconds, is sufficient, however longer times might be appropriate to ensure time to reinstall.

### Prerequisites

Review and note connector parameter context values.
If you’re reinstalling the connector in the same runtime, you can reuse the existing context.
If the new instance is located in a different runtime, you must re-enter all parameters.

1. Finish processing all in-flight FlowFiles in the existing connector, then stop the connector.

   1. Sign in to [Snowsight](../../../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
   2. In the navigation menu, select Ingestion » Openflow.
   3. Select Launch Openflow.
   4. In the Openflow pane select the Runtimes tab.
   5. Select the runtime containing the connector.
   6. Select the connector.
   7. Stop the topmost processor Set Tables for Replication in the Snapshot Load group.
   8. Stop the topmost processor Read MySQL CDC Stream in the Incremental Load group.
   9. If you changed the value of the Merge Task Schedule CRON parameter, return it to `* * * * * ?`, otherwise queues will not be emptied until the next scheduled run.

      Wait until all FlowFiles in the connector have been processed, and all queues are empty.
      When all FlowFiles have been processed, the Queued value on the connector’s processor group becomes zero.
      If there are any items left in the original connector’s queues, there may be data gaps when the new connector starts.
   10. Stop all processors and controller services in the connector.

   Caution

   The existing connector can remain in the runtime and doesn’t interfere with the new instance, as long as it remains stopped.
2. Create a new instance of the connector. If you’re using the same runtime as the original connector, you can choose to keep the existing parameter contexts and reuse the settings.
3. If you’re installing into a different runtime or you deleted the previous parameter contexts, enter the configuration settings into the new parameter contexts,
   including the table names and patterns as described in [Set up the Openflow Connector for MySQL](setup).
4. Navigate to the `MySQL Ingestion Parameters` context, and set the following parameters:

   * Set the `Ingestion Type` parameter to `incremental`. For more information on the concerns see [Enable incremental replication without snapshots](incremental-replication.html#label-mysql-incremental-replication).
   * Set the `Starting Binlog Position` parameter to `Earliest`.
     For more information and potential concerns see [Specify load from binary log position](#label-mysql-connector-start-restart-incremental-load-from-earliest-available-binary-log-position).
5. Start the new connector.

### Usage notes

The new connector uses the existing destination tables that were created by the original connector, but the connector creates new journal tables.

## Specify load from binary log position

The Openflow Connector for MySQL connector allows you to select the starting position where MySQL binary logs are read.
By default the connector reads from the latest available position. Alternatively, you can choose the earliest position available on the source instance.
Choosing to start from the earliest position is common when reinstalling the connector.
This allows the new instance to catch up and continue replicating existing tables without having to snapshot each again.

Note that switching a running connector from latest to earliest position cause the entire available binary log
to be re-read, re-processed, and re-applied to the destination table.

Warning

While the binary log is being re-read, the columns and data in affected destination tables
can become out of sync with their sources until all events have been re-processed and merged.

The following parameters control snapshot loads are available in the `Ingestion Parameters` context:

| Parameter | Description |
| --- | --- |
| Starting Binlog Position | * `Latest` (default): CDC stream reading starts at the latest available position and continues from there. * `Earliest`: Switches the incremental load to start, or restart reading from the earliest available   binary log position. |
| Re-read Tables in State | * `New` (default):   While re-reading the binary log, only those events will be processed   from new tables added to replication after the re-reading started.   Other events are discarded until the connector reaches the position just before re-reading started. * `Any active`: Re-read and re-process events from any table currently in replication. |

See moreShow less

Expand

To determine whether the connector finished re-reading the binary log:

1. Navigate to the Openflow canvas.
2. Open the Incremental Load process group.
3. Right-click the topmost processor named Read MySQL CDC Stream, then select View state.
4. Compare the state entries:

   * binlog.position.rewind: the latest position the processor read before re-reading of the binary log started.
   * binlog.position.dml: the current latest position read by the processor. As long as this value is lower than the rewind value above, the processor is still re-reading the binary log.

### Usage notes

* After a running connector is switched to read from the earliest position, and starts running,
  the process cannot be reconfigured or cancelled, and will continue until the currently-read position reaches the position from before it started.
* Switching to the earliest position on a running connector will, for any tables being re-processed,
  finish their existing journals, and create new journal tables.
* If the binary log contains events from a previous table that was dropped
  and re-created in the source database, the re-reading the stream re-processes all events in the current destination.
  The connector cannot distinguish between a previous and current source table if they share the same name.
