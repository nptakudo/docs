---
title: "Using the Snowflake Connector for Kafka with Apache Iceberg™ tables"
url: "https://docs.snowflake.com/en/user-guide/kafka-connector-iceberg"
---

# Using the Snowflake Connector for Kafka with Apache Iceberg™ tables

Beginning with version 3.0.0, the Snowflake Connector for Kafka can ingest data into a
Snowflake-managed [Apache Iceberg™ table](tables-iceberg).

## Requirements and limitations

Before you configure the Kafka connector for Iceberg table ingestion, note the following requirements and limitations:

* Iceberg table ingestion requires version 3.0.0 or later of the Kafka connector.
* Iceberg table ingestion is supported by the Kafka connector with Snowpipe Streaming. It’s not supported by the Kafka connector with Snowpipe.
* Iceberg table ingestion is not supported when `snowflake.streaming.enable.single.buffer` is set to `false`.
* You must create an Iceberg table before running the connector. For more information, see [Configuration and setup](#configuration-and-setup) in this topic.

### Schema evolution limitations

Schema evolution for Iceberg is fully supported for schematized data formats like AVRO or Protobuf.

For plain JSON without a schema, the connector considers the following message types invalid and sends them to
dead-letter queues (DLQ):

* Messages with a new column if the corresponding value is `null` or `[]`
* Messages with a new field in a structured object if the corresponding value is `null` or `[]`

To manually change the table schema so that the connector can ingest these message types, use an ALTER TABLE statement.

## Configuration and setup

To configure the Kafka connector for Iceberg table ingestion, you follow the
regular [setup steps for a Snowpipe Streaming-based connector](snowpipe-streaming/snowpipe-streaming-classic-kafka)
with a few differences noted in the following sections.

### Grant usage on an external volume

You must grant the USAGE privilege on the external volume associated with your Iceberg table to your role for the Kafka connector.

For example, if your Iceberg table uses the `kafka_external_volume` external volume
and the connector uses the role `kafka_connector_role`, run the following statement:

CopyExpand

```
USE ROLE ACCOUNTADMIN;
GRANT USAGE ON EXTERNAL VOLUME kafka_external_volume TO ROLE kafka_connector_role;
```

Show lessSee more

Scroll to top

### Create an Iceberg table for ingestion

Before you run the connector, you must create an Iceberg table.
The initial table schema depends on your connector `snowflake.enable.schematization` settings.

If you enable schematization, you can create a table with a column named `record_metadata`:

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

If you don’t enable schematization, you can create a table with a column named `record_content` of a type that matches the actual Kafka message content.
The connector automatically creates the `record_metadata` column.

When you create an Iceberg table, you can use Iceberg data types or [compatible Snowflake types](tables-iceberg-data-types).
The semi-structured VARIANT type isn’t supported. Instead, use a [structured OBJECT or MAP](../sql-reference/data-types-structured).

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

To create an Iceberg table for the example message, use the following statement:

> CopyExpand
>
> ```
> CREATE OR REPLACE ICEBERG TABLE my_iceberg_table (
>     record_content OBJECT(
>         id INT,
>         body_temperature FLOAT,
>         name STRING,
>         approved_coffee_types ARRAY(STRING),
>         animals_possessed OBJECT(dogs BOOLEAN, cats BOOLEAN),
>         date_added DATE
>     )
>   )
>   EXTERNAL_VOLUME = 'my_volume'
>   CATALOG = 'SNOWFLAKE'
>   BASE_LOCATION = 'my_location/my_iceberg_table';
> ```
>
> Show lessSee more
>
> Scroll to top

Note

Field names inside nested structures such as `dogs` or `cats` are case sensitive.

### Configuration properties

`snowflake.streaming.iceberg.enabled`
:   Specifies whether the connector ingests data into an Iceberg table. The connector fails if this property doesn’t match the actual table type.

    Values:

    * `true`
    * `false`

    Default:
    :   `false`
