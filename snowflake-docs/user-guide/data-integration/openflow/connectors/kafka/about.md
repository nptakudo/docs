---
title: "About Openflow Connector for Kafka"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/kafka/about"
---

# About Openflow Connector for Kafka

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes the basic concepts of Openflow Connector for
Kafka and limitations.

Apache Kafka software uses a publish and subscribe model to write and
read streams of records, similar to a message queue or enterprise
messaging system. Kafka allows processes to read and write messages
asynchronously. A subscriber does not need to be connected directly to a
publisher; a publisher can queue a message in Kafka for the subscriber
to receive later.

An application publishes messages to a topic, and an application
subscribes to a topic to receive those messages. Kafka can process, as
well as transmit, messages; however, that is outside the scope of this
document. Topics can be divided into partitions to increase scalability.

The Openflow Connector for Kafka reads data from Kafka topics and writes
it into Snowflake tables using the [Snowpipe Streaming](../../../../snowpipe-streaming/data-load-snowpipe-streaming-overview) mechanism.

Use this connector if you’re looking to do the following:

* Ingest real‐time events from Apache Kafka into Snowflake for near real-time analytics

## Limitations

* If the `Topic To Table Map` parameter is not set:

  + Table names must precisely match the topic of the data they hold.
  + Table names must be in uppercase format.
* If the `Topic To Table Map` parameter is set:

  + Table names must match the table names specified in the mapping. The table names must be a valid Snowflake unquoted identifier. For information about valid table names, see [Identifier requirements](../../../../../sql-reference/identifiers-syntax).
* Only JSON and AVRO formats are supported.
* Only Confluent Schema Registry is supported.
* *PLAINTEXT*, *SASL\_PLAIN*, *SSL*, and *SASL\_SSL* security protocols are
  supported.
* *PLAIN*, *SCRAM-SHA-256*, *SCRAM-SHA-512* and *AWS\_MSK\_IAM* SASL mechanisms are
  supported.
* *mTLS* and *AWS MSK IAM* authentication methods require extra configuration via services. See [Configure other authentication methods for Openflow Connector for Kafka](authentication) for more details.
* In case of data insertion failure into a table, the connector will
  keep retrying infinitely.

## Field name mapping and special characters handling

When mapping field names from Kafka messages to Snowflake column names, the connector applies the following transformations to ensure compatibility with Snowflake naming conventions:

1. **First character**: The first character of the field name must be a letter. If it is not a letter, it is changed to an underscore.
2. **Other characters**: All other characters must be letters, numbers, or underscores. Any other characters are changed to underscores.

## Next steps

* [Set up the Openflow Connector for Kafka](setup)
* [Performance Tuning of the Openflow Connector for Kafka](performance-tuning)
