---
title: "Apache Kafka with DLQ and metadata"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/kafka/kafka-dlq-metadata"
---

# Apache Kafka with DLQ and metadata

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes the Apache Kafka with DLQ and metadata connector. This is the full-featured connector that provides feature parity with the
legacy Snowflake connector for Kafka and includes advanced capabilities for production use cases.

## Key features

The Apache Kafka with DLQ and metadata connector provides comprehensive functionality:

* **Dead Letter Queue (DLQ)** support for failed message handling
* **RECORD\_METADATA** column with Kafka message metadata
* **Configurable schematization** - enable or disable schema detection
* **Iceberg table support** with schema evolution
* **Multiple message formats** - JSON and AVRO support
* **Schema registry integration** for AVRO messages
* **Topic-to-table mapping** with advanced patterns
* **SASL authentication** support

## Specific parameters

In addition to the common parameters described in [Set up the Openflow Connector for Kafka](setup), this connector includes additional parameter contexts for advanced features.

### Message format and schema parameters

| Parameter | Description | Required |
| --- | --- | --- |
| Message Format | The format of messages in Kafka. One of: *JSON* / *AVRO*. Default: *JSON* | Yes |
| AVRO Schema | Avro schema in case *schema-text-property* is used in AVRO Schema Access Strategy with the AVRO message format. Note: this should only be used in case all messages consumed from the configured Kafka Topic(s) share the same schema. | No |
| AVRO Schema Access Strategy | The method of accessing the AVRO schema of a message. Required for *AVRO*. One of: *embedded-avro-schema* / *schema-reference-reader* / *schema-text-property*. Default: *embedded-avro-schema* | No |

See moreShow less

Expand

### Schema registry parameters

| Parameter | Description | Required |
| --- | --- | --- |
| Schema Registry Authentication Type | The method of authenticating to schema registry if used. Otherwise, use *NONE*. One of: *NONE* / *BASIC*. Default: *NONE* | Yes |
| Schema Registry URL | The URL of Schema Registry. Required for *AVRO* message format. | No |
| Schema Registry Username | The username for Schema Registry. Required for *AVRO* message format. | No |
| Schema Registry Password | The password for Schema Registry. Required for *AVRO* message format. | No |

See moreShow less

Expand

### DLQ and advanced features parameters

| Parameter | Description | Required |
| --- | --- | --- |
| Kafka DLQ Topic | DLQ topic to send messages with parsing errors to | Yes |
| Schematization Enabled | Determines whether data is inserted into individual columns or a single RECORD\_CONTENT field. One of: *true* / *false*. Default: *true* | Yes |
| Iceberg Enabled | Specifies whether the processor ingests data into an Iceberg table. The processor fails if this property doesn’t match the actual table type. Default: *false* | Yes |

See moreShow less

Expand

## Schematization behavior

The connector’s behavior changes based on the **Schematization Enabled** parameter:

### Schematization enabled

When schematization is enabled, the connector:

* Creates individual columns for each field in the message
* Includes a **RECORD\_METADATA** column with Kafka metadata
* Automatically evolves the table schema when new fields are detected
* Flattens nested JSON/AVRO structures into separate columns

**Example table structure:**

| Row | RECORD\_METADATA | ACCOUNT | SYMBOL | SIDE | QUANTITY |
| --- | --- | --- | --- | --- | --- |
| 1 | {“timestamp”:1669074170090, “headers”: {“current.iter… | ABC123 | ZTEST | BUY | 3572 |
| 2 | {“timestamp”:1669074170400, “headers”: {“current.iter… | XYZ789 | ZABX | SELL | 3024 |

See moreShow less

Expand

### Schematization disabled

When schematization is disabled, the connector:

* Creates only two columns: **RECORD\_CONTENT** and **RECORD\_METADATA**
* Stores the entire message content as an OBJECT in **RECORD\_CONTENT**
* Does not perform automatic schema evolution
* Provides maximum flexibility for downstream processing

**Example table structure:**

| Row | RECORD\_METADATA | RECORD\_CONTENT |
| --- | --- | --- |
| 1 | {“timestamp”:1669074170090, “headers”: {“current.iter… | {“account”: “ABC123”, “symbol”: “ZTEST”, “side”:… |
| 2 | {“timestamp”:1669074170400, “headers”: {“current.iter… | {“account”: “XYZ789”, “symbol”: “ZABX”, “side”:… |

See moreShow less

Expand

Use the `Schematization Enabled` property in the connector configuration properties to enable or disable schema detection.

## Schema detection and evolution

The connector supports schema detection and evolution.
The structure of tables in Snowflake can be defined and evolved automatically to support the structure of new data loaded by the connector.

Without schema detection and evolution, the Snowflake table loaded by the connector
only consists of two `OBJECT` columns: `RECORD_CONTENT` and `RECORD_METADATA`.

With schema detection and evolution enabled, Snowflake can detect the schema of
the streaming data and load data into tables that automatically match any user-defined schema.
Snowflake also allows adding new columns or dropping the `NOT NULL` constraint from columns missing in new data files.

Schema detection with the connector is supported with or without a provided schema registry.
If using schema registry (Avro), the column will be created with the data types defined in the provided schema registry.
If there is no schema registry (JSON), the data type will be inferred based on the data provided.

JSON ARRAY is not supported for further schematization.

### Enabling schema evolution

If the connector creates the target table, schema evolution is enabled by default.

If you want to enable or disable schema evolution on the existing table, use
the [ALTER TABLE](../../../../../sql-reference/sql/alter-table) command to set the `ENABLE_SCHEMA_EVOLUTION` parameter.
You must also use a role that has the `OWNERSHIP` privilege on the table.
For more information, see [Enable automatic table schema evolution](../../../../data-load-schema-evolution).

However, if schema evolution is disabled for an existing table, then the connector
will try to send the rows with mismatched schemas to the configured dead-letter queues (DLQ).

### RECORD\_METADATA structure

The **RECORD\_METADATA** column contains important Kafka message metadata:

| Field | Description |
| --- | --- |
| offset | The message offset within the Kafka partition |
| topic | The Kafka topic name |
| partition | The Kafka partition number |
| key | The message key (if present) |
| timestamp | The message timestamp |
| SnowflakeConnectorPushTime | Timestamp when the connector fetched the message from Kafka |
| headers | Map of message headers (if present) |

See moreShow less

Expand

## Dead Letter Queue (DLQ)

The DLQ functionality handles messages that cannot be processed successfully:

### DLQ behavior

* **Parse failures** - Messages with invalid JSON/AVRO format are sent to the DLQ
* **Schema mismatches** - Messages that don’t match the expected schema when schema evolution is disabled
* **Processing errors** - Other processing failures during ingestion

## Iceberg table support

Openflow Connector for Kafka can ingest data into a Snowflake-managed [Apache Iceberg™ table](../../../../tables-iceberg) when **Iceberg Enabled** is set to *true*.

### Requirements and limitations

Before you configure the Openflow Kafka connector for Iceberg table ingestion, note the following requirements and limitations:

* You must create an Iceberg table before running the connector.
* Make sure that the user has access to inserting data into the created tables.

### Configuration and setup

To configure the Openflow Connector for Kafka for Iceberg table ingestion, follow
the steps in [Set up the Openflow Connector for Kafka](setup) with a few differences noted in the following sections.

#### Enable ingestion into Iceberg table

To enable ingestion into an Iceberg table, you must set the `Iceberg Enabled` parameter to `true`.

#### Create an Iceberg table for ingestion

Before you run the connector, you must create an Iceberg table.
The initial table schema depends on your connector `Schematization Enabled` property settings.

If you enable schematization, you must create a table with a column named `record_metadata`:

CopyExpand

```
CREATE OR REPLACE ICEBERG TABLE my_iceberg_table (
    record_metadata OBJECT()
  )
  EXTERNAL_VOLUME = 'my_volume'
  CATALOG = 'SNOWFLAKE'
  BASE_LOCATION = 'my_location/my_iceberg_table';
```

Show lessSee more

Scroll to top

The connector automatically creates the columns for message fields and alters the `record_metadata` column schema.

If you don’t enable schematization, you must create a table with a column named
`record_content` of a type that matches the actual Kafka message content.
The connector automatically creates the `record_metadata` column.

When you create an Iceberg table, you can use Iceberg data types or
[compatible Snowflake types](../../../../tables-iceberg-data-types).
The semi-structured VARIANT type isn’t supported. Instead, use a
[structured OBJECT or MAP](../../../../../sql-reference/data-types-structured).

For example, consider the following message:

CopyExpand

```
{
    "id": 1,
    "name": "Steve",
    "body_temperature": 36.6,
    "approved_coffee_types": ["Espresso", "Doppio", "Ristretto", "Lungo"],
    "animals_possessed":
    {
        "dogs": true,
        "cats": false
    },
    "date_added": "2024-10-15"
}
```

Show lessSee more

Scroll to top

### Iceberg table creation examples

**With schematization enabled:**

CopyExpand

```
CREATE OR REPLACE ICEBERG TABLE my_iceberg_table (
    RECORD_METADATA OBJECT(
        offset INTEGER,
        topic STRING,
        partition INTEGER,
        key STRING,
        timestamp TIMESTAMP,
        SnowflakeConnectorPushTime BIGINT,
        headers MAP(VARCHAR, VARCHAR)
    ),
    id INT,
    body_temperature FLOAT,
    name STRING,
    approved_coffee_types ARRAY(STRING),
    animals_possessed OBJECT(dogs BOOLEAN, cats BOOLEAN),
    date_added DATE
  )
  EXTERNAL_VOLUME = 'my_volume'
  CATALOG = 'SNOWFLAKE'
  BASE_LOCATION = 'my_location/my_iceberg_table';
```

Show lessSee more

Scroll to top

**With schematization disabled:**

CopyExpand

```
CREATE OR REPLACE ICEBERG TABLE my_iceberg_table (
    RECORD_METADATA OBJECT(
        offset INTEGER,
        topic STRING,
        partition INTEGER,
        key STRING,
        timestamp TIMESTAMP,
        SnowflakeConnectorPushTime BIGINT,
        headers MAP(VARCHAR, VARCHAR)
    ),
    RECORD_CONTENT OBJECT(
        id INT,
        body_temperature FLOAT,
        name STRING,
        approved_coffee_types ARRAY(STRING),
        animals_possessed OBJECT(dogs BOOLEAN, cats BOOLEAN),
        date_added DATE
    )
  )
  EXTERNAL_VOLUME = 'my_volume'
  CATALOG = 'SNOWFLAKE'
  BASE_LOCATION = 'my_location/my_iceberg_table';
```

Show lessSee more

Scroll to top

Note

RECORD\_METADATA must always be created. Field names inside nested structures such as `dogs` or `cats` are case sensitive.

## Use cases

This connector is ideal for:

* **Production environments** requiring DLQ
* **Data lineage and auditing** where Kafka metadata is important
* **Complex message processing** with schema evolution requirements
* **Iceberg table integration**

If you need simpler ingestion without metadata or DLQ features, consider
the [Apache Kafka for JSON/AVRO data format](kafka-json-avro) connectors instead.
