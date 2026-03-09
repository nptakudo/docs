---
title: "Openflow Connector for PostgreSQL Maintenance"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/postgres/maintenance"
---

# Openflow Connector for PostgreSQL Maintenance

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes important maintenance considerations and best practices for
maintaining the Openflow Connector for PostgreSQL when making changes to the source PostgreSQL database.
In addition this topic describes how to reinstall the connector.

## Upgrading PostgreSQL

Upgrading the connector requires a different approach depending on whether PostgreSQL is being upgraded to the next minor or major version.

Minor version upgrades

> * Are data safe.
> * Require no special treatment.
> * Require stopping the connector for the duration of the upgrade to avoid reporting connectivity issues.
> * Continue replicating, after the upgrade, with no data loss.

Major version upgrades

> * Require the PostgreSQL server to drop replication slots, including any used by the connector.
> * Cannot preserve, or migrate replication slots to the new version. See also [PostgresSQL 17 and later versions upgrades](#label-postgres-upgrade-note).
> * Restart replicating all tables from the prior snapshot phase.

To perform a minor version upgrade, do the following:

1. Stop the connector, including all Processors and Controller Services.
2. Upgrade PostgreSQL.
3. Restart the connector.

To perform a major version upgrade, do the following:

1. Remove all tables from replication in the connector.
2. Wait until all queues in the connector are empty.
3. Stop the connector, including all Processors and Controller Services.
4. Open the Incremental Load group in the connector.
5. Right-click the top Processor in the group, Read PostgreSQL CDC Stream, and select View state.
6. Click Clear state.
7. Click Close.
8. Upgrade PostgreSQL.
9. Restart the connector. A new replication slot will be created.
10. Re-add all tables to begin replication.

### PostgresSQL 17 and later versions upgrades

PostgreSQL 17 improved upgrading such that it no longer requires dropping replication slots when upgrading to later versions such as 17.1 » 18.0.
Upgrading to PostgreSQL 17.0 or later from prior versions (16 and earlier) drops replications slots and should be treated as a major upgrade.
Future versions of PostgreSQL may also improve the upgrade process further.

## Reinstall the connector

This section describes how to reinstall the connector.
It covers situations where the new connector is installed in the same runtime, or when it is moved to a new runtime.
Reinstall is often used in conjunction with [Incremental replication with snapshots](incremental-replication).

Warning

For the connector to be able to continue replicating from the same CDC stream position where it stopped before reinstallation,
the source database must retain the WAL long enough to cover the time since the old connector is stopped and the new connector is started.
Ensure the `max_wal_size` parameter of the PostgreSQL server is high enough, depending on your traffic, and keep the reinstallation time to a minimum.

### Prerequisites

Review and note connector parameter context values.
If you’re reinstalling the connector in the same runtime, you can reuse the existing context.
If the new instance will be located in a different runtime, you will have to re-enter all parameters.

To reinstall the connector:

1. Finish processing all in-flight FlowFiles in the existing connector, and then stop the connector.

   1. Sign in to [Snowsight](../../../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
   2. In the navigation menu, select Ingestion » Openflow.
   3. Select Launch Openflow.
   4. In the Openflow pane select the Runtimes tab.
   5. Select the runtime containing the connector.
   6. Select the connector.
   7. Stop the topmost processor Set Tables for Replication in the Snapshot Load group.
   8. Stop the topmost processor Read PostgreSQL CDC Stream in the Incremental Load group.
   9. If you changed the value of the Merge Task Schedule CRON parameter, return it to `* * * * * ?`, otherwise queues will not be emptied until the next scheduled run.

      Wait until all FlowFiles in the connector have been processed, and all queues are empty.
      When all FlowFiles have been processed, the Queued value on the connector’s processor group becomes zero.
      If there are any items left in the original connector’s queues, there may be data gaps when the new connector starts.
   10. Stop all processors and controller services in the connector.
2. Find and copy the name of the replication slot used by the original connector,
   by viewing the state of the topmost processor in the `Incremental Load` group with name `Read PostgreSQL CDC Stream`.
   The replication slot name is stored under the key `replication.slot.name`.
   Copy the value of the key to a text editor.
3. Create a new instance of the connector. If you’re using the same runtime as the original connector, you can choose to keep the existing parameter contexts, and reuse the settings.

   Caution

   The existing connector can remain in the runtime and doesn’t interfere with the new instance, as long as it remains stopped.
4. If you’re installing into a different runtime, or you deleted the previous parameter contexts, enter all the configuration settings into the new parameter contexts,
   including the table names and patterns as described in [Set up the Openflow Connector for PostgreSQL](setup).
5. Open the `PostgreSQL Ingestion Parameters` context, and set `Ingestion Type` parameter to `incremental`.
   For more information on the concerns see [Enable incremental replication without snapshots](incremental-replication.html#label-postgres-incremental-replication).
6. Open the `PostgreSQL Source Parameters` context, and set the `Replication Slot Name` parameter to the value you copied earlier.
7. Start the new connector.

### Usage notes

The new connector will use the same, existing destination tables that created by the original connector, but will create new journal tables.
