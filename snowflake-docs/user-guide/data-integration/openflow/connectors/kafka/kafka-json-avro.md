---
title: "Apache Kafka for JSON/AVRO data format"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/kafka/kafka-json-avro"
---

# Apache Kafka for JSON/AVRO data format

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes the Apache Kafka connectors for JSON and AVRO data formats.
These are simplified connectors optimized for basic message ingestion with schema evolution
and topic-to-table mapping capabilities.

## Connector variants

### JSON data format connector

The Apache Kafka for JSON data format connector is designed for straightforward JSON message ingestion from Kafka topics to Snowflake tables.

Key features:

* JSON message format support
* Schema evolution
* Topic-to-table mapping
* SASL authentication

### AVRO data format connector

The Apache Kafka for AVRO data format connector is designed for AVRO message ingestion from Kafka topics to Snowflake tables with schema registry support.

Key features:

* AVRO message format support
* Schema registry integration
* Schema evolution
* Topic-to-table mapping
* SASL authentication

## Specific parameters

In addition to the common parameters described in [Set up the Openflow Connector for Kafka](setup), these connectors have specific parameter contexts.

### Schema registry parameters (AVRO connector only)

The AVRO connector includes additional parameters for schema registry integration:

| Parameter | Description | Required |
| --- | --- | --- |
| Schema Registry Authentication Type | The method of authenticating to schema registry if used. Otherwise, use *NONE*. One of: *NONE* / *BASIC*. Default: *NONE* | Yes |
| Schema Registry URL | The URL of Schema Registry. Required for *AVRO* message format. | No |
| Schema Registry Username | The username for Schema Registry. Required for *AVRO* message format. | No |
| Schema Registry Password | The password for Schema Registry. Required for *AVRO* message format. | No |
| AVRO Schema Access Strategy | The method of accessing the AVRO schema of a message. Required for *AVRO*. One of: *embedded-avro-schema* / *schema-reference-reader* / *schema-text-property*. Default: *embedded-avro-schema* | No |
| AVRO Schema | Avro schema in case schema-text-property is used in AVRO Schema Access Strategy with the AVRO message format. Note: this should only be used in case all messages consumed from the configured Kafka Topic(s) share the same schema. | No |

See moreShow less

Expand

## Limitations

These simplified connectors have the following limitations compared to the full-featured DLQ and metadata connector:

* **No RECORD\_METADATA column** - Kafka metadata is not stored in the target tables
* **No dead letter queue (DLQ)** - Failed messages are not routed to a DLQ topic
* **No Iceberg table support** - Only regular Snowflake tables are supported
* **Fixed schematization** - Schema detection is always enabled and cannot be disabled

Note

Schema detection is enabled by default in these connectors and cannot be disabled.
This means message fields are automatically flattened into individual table columns with automatic schema evolution.

## Use cases

These connectors are ideal for:

Simple data ingestion
:   When you only need the message content without Kafka metadata.

High-throughput scenarios
:   Where the simplified data structure improves performance.

Schema evolution use cases
:   Where automatic table schema updates are required

JSON or AVRO message formats
:   With consistent schemas

If you need Kafka metadata, DLQ support, or Iceberg table ingestion, use the [Apache Kafka with DLQ and metadata](kafka-dlq-metadata) connector instead.

## Schema detection and evolution

These connectors support automatic schema detection and evolution. The structure
of tables in Snowflake is defined and evolved automatically to support the structure
of new data loaded by the connector.

With schema detection enabled (which is always the case for these connectors),
Snowflake can detect the schema of the streaming data and load data into tables
that automatically match any user-defined schema. Snowflake also allows adding
new columns or dropping the `NOT NULL` constraint from columns missing in new data files.

Schema detection with the connector is supported with or without a provided schema registry.
If using schema registry (Avro), the column will be created with the data types defined
in the provided schema registry. If there is no schema registry (JSON), the data type will be inferred based on the data provided.

JSON ARRAY is not supported for further schematization.

### Schema evolution behavior

If the connector creates the target table, schema evolution is enabled by default.

If you want to enable or disable schema evolution on an existing table,
use the [ALTER TABLE](../../../../../sql-reference/sql/alter-table) command to set the `ENABLE_SCHEMA_EVOLUTION` parameter.
You must also use a role that has the `OWNERSHIP` privilege on the table. For more information, see [Enable automatic table schema evolution](../../../../data-load-schema-evolution).
