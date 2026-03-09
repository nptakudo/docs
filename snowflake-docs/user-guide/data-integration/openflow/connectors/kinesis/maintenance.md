---
title: "Maintain Openflow Connector for Kinesis"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/kinesis/maintenance"
---

# Maintain Openflow Connector for Kinesis

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes how to maintain the Openflow Connector for Kinesis connector, including how to manage and reset the connector state.

## Manage connector state

The Openflow Connector for Kinesis uses DynamoDB to store the consumer application state.

### DynamoDB tables created by the connector

For each Kinesis Application Name configured in the connector, the KCL creates three DynamoDB tables:

`<Kinesis Application Name>`
:   Stores the checkpointed sequence number for each shard in the stream.
    This tracks which records have been processed.

`<Kinesis Application Name>-CoordinatorState`
:   Used for coordination between workers when multiple processors share the same Application Name.

`<Kinesis Application Name>-WorkerMetricStats`
:   Used for workers to report metrics, which are used during work assignment.

In these table names, `<Kinesis Application Name>` is the value provided when you set up the connector.

If multiple processors use the same Application Name, they cooperate to consume data from the stream
and share these tables. If processors have different Application Names, each creates its own set of tables
to independently track consumed records.

For more information about DynamoDB tables, see the [AWS Kinesis Client Library documentation](https://docs.aws.amazon.com/streams/latest/dev/kcl-dynamoDB.html).

## Reset the connector state

If the connector state in DynamoDB becomes corrupted or inconsistent, you may need to reset it.
There are two approaches to reset the connector state.

### Reset by changing the Application Name

The simplest way to reset the connector state is to change the Kinesis Application Name parameter:

1. Stop the connector.
2. Navigate to the connector’s parameter context.
3. Change the `Kinesis Application Name` parameter value to a new value.
4. Start the connector.

The connector creates new DynamoDB tables with the new Application Name and begins consuming
records from the position specified by the [Kinesis Initial Stream Position](setup.html#label-kinesis-json-source-parameters) parameter.

Note

If your IAM policy restricts DynamoDB access to specific table names, you must update the policy
to allow access to the new table names. For more information on configuring IAM permissions,
see [Set up Openflow Connector for Kinesis for JSON data format](setup).

### Reset by deleting the DynamoDB tables

Alternatively, you can delete the existing DynamoDB tables to reset the state:

1. Stop the connector.
2. In the AWS Console or using the AWS CLI, delete the three DynamoDB tables associated with the Application Name:

   * `<Kinesis Application Name>`
   * `<Kinesis Application Name>-CoordinatorState`
   * `<Kinesis Application Name>-WorkerMetricStats`
3. Start the connector.

The connector recreates the tables and begins consuming records from the position specified by the [Kinesis Initial Stream Position](setup.html#label-kinesis-json-source-parameters) parameter.

Warning

Resetting the connector state causes the connector to reprocess records from the position specified by the
initial stream position. Depending on your [Kinesis Initial Stream Position](setup.html#label-kinesis-json-source-parameters) setting,
this may result in duplicate data being ingested into Snowflake or data not being ingested at all.
