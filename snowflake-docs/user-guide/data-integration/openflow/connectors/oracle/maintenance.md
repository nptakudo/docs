---
title: "Openflow Connector for Oracle: Maintenance"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/oracle/maintenance"
---

# Openflow Connector for Oracle: Maintenance

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

This topic describes maintenance tasks for the Openflow Connector for Oracle, such as reinstalling the
connector or setting the starting redo log position.

These operations are often used in conjunction with [Incremental replication with snapshots](incremental-replication).

## Reinstall the connector

This section provides instructions on how to reinstall the connector, and continue replicating data for
the same tables without having to snapshot them again.
It covers situations where the new connector is installed in the same runtime, as well as moved to a new runtime.

Warning

For the connector to continue replicating from the same CDC stream position where it stopped before reinstallation,
the source database must retain the archived redo logs long enough to cover the time after the prior connector was stopped
and before the new connector is started.
Ensure the archived redo log retention period of the Oracle database is high enough, and keep the reinstallation time to a minimum.

Typically a retention period of 24 hours is sufficient, however longer times might be appropriate to ensure time to reinstall.
For more information on configuring archived redo log retention, see [Openflow Connector for Oracle: Configure the Oracle database](setup-oracledb).

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
   8. Stop the topmost processor Read Oracle CDC Stream in the Incremental Load group.
   9. If you changed the value of the Merge Task Schedule CRON parameter, return it to `* * * * * ?`, otherwise queues will not be emptied until the next scheduled run.

      Wait until all FlowFiles in the connector have been processed, and all queues are empty.
      When all FlowFiles have been processed, the Queued value on the connector’s processor group becomes zero.
      If any items remain in the original connector’s queues, data gaps might occur when the new connector starts.
   10. Stop all processors and controller services in the connector.

   Caution

   The existing connector can remain in the runtime and doesn’t interfere with the new instance, as long as it remains stopped.
2. Create a new instance of the connector. If you’re using the same runtime as the original connector, you can choose to keep the existing parameter contexts and reuse the settings.
3. If you’re installing into a different runtime or you deleted the previous parameter contexts, enter the configuration settings into the new parameter contexts,
   including the table names and patterns as described in [Install and configure the Openflow Connector for Oracle](setup-connector).
4. Navigate to the `Oracle Ingestion Parameters` context, and set the following parameters:

   * Set the `Ingestion Type` parameter to `incremental`. For more information on the concerns see [Enable incremental replication without snapshots on an existing connector](incremental-replication.html#label-oracle-incremental-replication).
   * Set the `Starting Redo Log Position` parameter to `Earliest`.
     For more information and potential concerns see [Alter XStream outbound server](#label-oracle-alter-xstream-outbound-server).
5. Start the new connector.

### Usage notes

The new connector uses the existing destination tables that were created by the original connector, but the connector creates new journal tables.

## Alter XStream outbound server

The connector regularly updates the XStream server with the latest SCN position it processed. If the connector
is reinstalled and connects to the same XStream outbound server, it will resume reading from the SCN position where it left off.
This SCN number can be checked with:

CopyExpand

```
SELECT PROCESSED_LOW_SCN
FROM DBA_XSTREAM_OUTBOUND_PROGRESS
WHERE SERVER_NAME = 'XOUT1';
```

Show lessSee more

Scroll to top

If you want to re-read data from an earlier position, you must first change the start SCN of the XStream server:

CopyExpand

```
BEGIN
    DBMS_XSTREAM_ADM.ALTER_OUTBOUND(
        server_name => 'XOUT1',
        start_scn => <start_scn>
    );
END;
/
```

Show lessSee more

Scroll to top

The value of `<start_scn>` must be a valid SCN within the range of available redo logs. The lowest SCN that the start position can be reset to can be checked with:

CopyExpand

```
SELECT REQUIRED_CHECKPOINT_SCN
FROM DBA_CAPTURE
WHERE CLIENT_NAME = 'XOUT1';
```

Show lessSee more

Scroll to top

This is the lowest SCN for which the capture process requires redo information.

## Specify load from XStream position

The Openflow Connector for Oracle connector allows you to select the starting position where Oracle redo logs are read.
By default the connector reads from the latest available position. Alternatively, you can choose the earliest position available on the source instance.
Choosing to start from the earliest position is common when reinstalling the connector.
This allows the new instance to catch up and continue replicating existing tables without having to snapshot each again.

Note

Switching a running connector from latest to earliest position causes the entire available redo logs
to be re-read, re-processed, and re-applied to the destination table.

Warning

While the redo logs are being re-read, the columns and data in affected destination tables
can become out of sync with their sources until all events have been re-processed and merged.

The following parameters are available in the `Ingestion Parameters` context:

| Parameter | Description |
| --- | --- |
| Starting XStream Position | * `Latest` (default): CDC stream reading starts at the latest available position and continues from there. * `Earliest`: Switches the incremental load to start, or restart reading from the earliest available   XStream position. |
| Re-read Tables in State | * `New` (default):   While re-reading the redo logs, only those LCRs (Logical Change Records) will be processed   from new tables added to replication after the re-reading started.   Other LCRs are discarded until the connector reaches the position just before re-reading started. * `Any active`: Re-read and re-process events from any table currently in replication. |

See moreShow less

Expand

To determine whether the connector finished re-reading the redo logs:

1. Navigate to the Openflow canvas.
2. Open the Incremental Load process group.
3. Right-click the topmost processor named Read Oracle CDC Stream, then select View state.
4. Compare the state entries:

   * lcr.position.rewind: the latest position the processor read before re-reading of the redo logs started.
   * lcr.position.last: the current latest position read by the processor. As long as this value is lower than the rewind value above, the processor is still re-reading the redo logs.

### Usage notes

* After a running connector is switched to read from the earliest position, and starts running,
  the process can’t be reconfigured or cancelled, and continues until the currently-read position reaches the position from before it started.
* Switching to the earliest position on a running connector will, for any tables being re-processed,
  finish their existing journals, and create new journal tables.
* If the redo log contains events from a previous table that was dropped
  and re-created in the source database, the re-reading the stream re-processes all events in the current destination.
  The connector can’t distinguish between a previous and current source table if they share the same name.

Note

Schema changes (such as ALTER TABLE statements that add or drop columns) are not supported
while re-reading the redo logs from the earliest position. If any table’s schema was
altered between the earliest available SCN and the current position, that table should
be removed from replication and re-added with a fresh snapshot instead.
