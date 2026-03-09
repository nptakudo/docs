---
title: "About Openflow Connector for Kinesis"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/kinesis/about"
---

# About Openflow Connector for Kinesis

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes the basic concepts of Openflow Connector for Kinesis, including its workflow and limitations.

You can use [Amazon Kinesis Data Streams](https://docs.aws.amazon.com/streams/latest/dev/introduction.html)
to collect and process large streams of data records in real time. Producers continually push data to
Kinesis Data Streams, and consumers process the data in real time.

A Kinesis data stream is a set of [shards](https://docs.aws.amazon.com/streams/latest/dev/key-concepts.html#shard). Each shard has a sequence of data records.
A data record is the unit of data stored in a Kinesis data stream. Data records are composed of
a sequence number, a partition key, and a data blob, which is an immutable sequence of bytes.

The Openflow Connector for Kinesis reads data from a Kinesis data stream and writes it to a Snowflake table using [Snowpipe Streaming](../../../../snowpipe-streaming/data-load-snowpipe-streaming-overview).

## Use cases

Use this connector if you want to ingest real‐time events from Amazon Kinesis Data Streams into Snowflake for near real-time analytics.

## Workflow

### AWS administrator tasks

1. Create credentials for the connector to connect with Kinesis Stream and the associated DynamoDB.
2. Set up IAM policies that have the permissions listed in [IAM permissions required for KCL consumer applications](https://docs.aws.amazon.com/streams/latest/dev/kcl-iam-permissions.html).
3. Record the stream name and application name and provide them to your Snowflake account administrator. These are required when setting up the connector in the runtime.

Snowflake account administrator tasks
————————————————————————————————===

1. Install the connector.
2. Configure the connector:
   :   1. Provide the AWS and Snowflake credentials and settings.
       2. Provide the Kinesis stream name.
       3. Set the database and schema names in the Snowflake account.
       4. Customize other parameters.
3. Run the connector in the Openflow canvas. Upon execution, the connector performs the following actions:
   :   1. Creates DynamoDB tables for storing Kinesis Stream checkpoints.
       2. Extracts stream data.
       3. Creates the configured destination table in the Snowflake database if at least one record was received from the stream.
       4. Loads the processed data into the specified Snowflake table.

Business user tasks
————————————————————————————————===

Perform operations on the data downloaded from Kinesis into the destination table.

## Limitations

* The connector supports only a single stream.
* If you use a manually created table:
  :   + The table name must match the stream of the data it holds precisely.
      + The table name must be uppercase.
* The connector supports only JSON message format.
* The connector supports only Amazon Access Key IAM authentication.
* The connector logs failed messages to the Snowflake logs and does not route them to a DLQ stream.

## Next steps

For information on how to set up the connector, see the following topic:

* [Set up Openflow Connector for Kinesis for JSON data format](setup)
