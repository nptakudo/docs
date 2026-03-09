---
title: "Set up Openflow Connector for Kinesis for JSON data format"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/kinesis/setup"
---

# Set up Openflow Connector for Kinesis for JSON data format

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes how to set up Openflow Connector for Kinesis for JSON data format.
This is a simplified connector optimized for basic message ingestion with schema evolution capabilities.

The Openflow Connector for Kinesis for JSON data format is designed for straightforward JSON message ingestion from Kinesis streams to Snowflake tables.

## Prerequisites

1. Review [About Openflow Connector for Kinesis](about).
2. Ensure that you have [set up Openflow with BYOC](../../setup-openflow-byoc) or [set up Openflow with Snowflake Deployments](../../setup-openflow-spcs).
3. If you are using Openflow - Snowflake Deployments, ensure that you have reviewed [configuring required domains](../../setup-openflow-spcs-sf-allow-list)
   and have granted access to the required domains for the [Kinesis](../../setup-openflow-spcs-sf-allow-list.html#label-openflow-domains-used-by-openflow-connectors-kinesis) connector.

Note

If you need the support of other data formats or features, such as DLQ, reach out to your Snowflake representative.

## Set up a Kinesis stream

As an AWS administrator, perform the following actions in your AWS account:

1. Ensure that you have an AWS User with [IAM permissions to access Kinesis Streams and DynamoDB](https://docs.aws.amazon.com/streams/latest/dev/kcl-iam-permissions.html).
2. Ensure that the AWS User has configured [Access Key credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html).

## Set up Snowflake account

As a Snowflake account administrator, perform the following tasks:

1. Create a new role or use an existing role and grant the [Database privileges](../../../../security-access-control-privileges.html#label-database-privileges).
2. Create a destination database and destination schema that will be used to create destination tables for storing the data.

   1. If you plan to use the connector’s capability to automatically create destination table if it does not already exist, make sure the user has the required privileges for creating and managing Snowflake objects:

      | Object | Privilege | Notes |
      | --- | --- | --- |
      | Database | USAGE |  |
      | Schema | USAGE . CREATE TABLE . | After the schema-level objects have been created, the CREATE `object` privileges can be revoked. |
      | Table | OWNERSHIP | Only required when using the Kinesis connector to ingest data into an existing table. . If the connector creates a new target table for records from the Kinesis stream, the default role for the user specified in the configuration becomes the table owner. |

      See moreShow less

      Expand

      You can use the following script to create and configure a custom role (requires SECURITYADMIN or equivalent):

      CopyExpand

      ```
      USE ROLE SECURITYADMIN;

      CREATE ROLE kinesis_connector_role;
      GRANT USAGE ON DATABASE kinesis_db TO ROLE kinesis_connector_role;
      GRANT USAGE ON SCHEMA kinesis_schema TO ROLE kinesis_connector_role;
      GRANT CREATE TABLE ON SCHEMA kinesis_schema TO ROLE kinesis_connector_role;

      -- Only for existing tables.
      GRANT OWNERSHIP ON TABLE existing_table TO ROLE kinesis_connector_role;
      ```

      Show lessSee more

      Scroll to top
3. Create a new Snowflake service user with the type as [SERVICE](../../../../../sql-reference/sql/create-user.html#label-user-type-property).
4. Grant the Snowflake service user the role you created in the previous steps.

   CopyExpand

   ```
   GRANT ROLE kinesis_connector_role TO USER kinesis_connector_user;
   ALTER USER kinesis_connector_user SET DEFAULT_ROLE = kinesis_connector_role;
   ```

   Show lessSee more

   Scroll to top
5. Configure with [key-pair auth](../../../../key-pair-auth) for the Snowflake SERVICE user from step 3.
6. Snowflake strongly recommends this step. Configure a secrets manager supported by Openflow, for example, AWS, Azure, and Hashicorp, and store the public and private keys in the secret store.

   Note

   If for any reason, you do not wish to use a secrets manager, then you are responsible for safeguarding the
   public key and private key files used for key-pair authentication according to the security policies of your organization.

   1. After the secrets manager is configured, determine how you will authenticate to it. On AWS, it’s recommended that you use the
      EC2 instance role associated with Openflow as this way no other secrets have to be persisted.
   2. In Openflow, configure a Parameter Provider associated with this Secrets Manager, from the hamburger menu in the upper right.
      Navigate to Controller Settings » Parameter Provider and then fetch your parameter values.
   3. At this point all credentials can be referenced with the associated parameter paths and no sensitive values need to be persisted within Openflow.
7. If any other Snowflake users require access to the ingested data and created tables (for example, for custom processing in Snowflake),
   grant those users the role created in step 2.

## Set up the connector

As a data engineer, perform the following tasks to install and configure the connector:

### Install the connector

To install the connector, do the following as a data engineer:

1. Navigate to the Openflow overview page. In the Featured connectors section, select View more connectors.
2. On the Openflow connectors page, find the connector and select Add to runtime.
3. In the Select runtime dialog, select your runtime from the Available runtimes drop-down list and click Add.

   Note

   Before you install the connector, ensure that you have created a database and schema in Snowflake for the connector to store ingested data.
4. Authenticate to the deployment with your Snowflake account credentials and select Allow when prompted to allow the runtime application to access your Snowflake account. The connector installation process takes a few minutes to complete.
5. Authenticate to the runtime with your Snowflake account credentials.

The Openflow canvas appears with the connector process group added to it.

### Configure the connector

1. Right-click the imported process group and select Parameters.
2. Populate the required parameter values as described in [Parameters](#parameters).

## Parameters

This section describes all parameters for the Openflow Connector for Kinesis for JSON data format.

The connector consists of several modules. To see the set, double-click the connector process group.
You can set the parameters for each module in the module’s parameter context.

### Snowflake destination parameters

| Parameter | Description | Required |
| --- | --- | --- |
| Destination Database | The database where data will be persisted. It must already exist in Snowflake. The name is case-sensitive. For unquoted identifiers, provide the name in uppercase. | Yes |
| Destination Schema | The schema where data will be persisted, which must already exist in Snowflake. The name is case-sensitive. For unquoted identifiers, provide the name in uppercase.  See the following examples:  * `CREATE SCHEMA SCHEMA_NAME` or `CREATE SCHEMA schema_name`: use `SCHEMA_NAME` * `CREATE SCHEMA "schema_name"` or `CREATE SCHEMA "SCHEMA_NAME"`: use `schema_name` or `SCHEMA_NAME`, respectively | Yes |
| Iceberg Enabled | Whether Iceberg is enabled for table operations. One of `true` / `false`. | Yes |
| Schema Evolution Enabled | Enables or disables schema evolution at the connector level. When enabled, allows automatic schema changes for tables. Note that schema evolution can also be controlled at the individual table level through table-specific parameters. One of: `true` / `false`. | Yes |
| Schema Evolution For New Tables Enabled | Controls whether schema evolution is enabled when creating new tables. When set to ‘true’, new tables will be created with ENABLE\_SCHEMA\_EVOLUTION = TRUE parameter. When set to ‘false’, new tables will be created with ENABLE\_SCHEMA\_EVOLUTION = FALSE parameter. Not applicable to Iceberg tables as they are not being created automatically. This setting only affects table creation, not existing tables. One of: `true` / `false`. | Yes |
| Snowflake Account Identifier | When using:   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Snowflake account name formatted as [organization-name]-[account-name] where data will be persisted. | Yes |
| Snowflake Authentication Strategy | When using:   * **Snowflake Openflow Deployment** or **BYOC**: Use SNOWFLAKE\_MANAGED\_TOKEN.   This token is managed automatically by Snowflake.   BYOC deployments must have previously configured   [runtime roles](../../setup-openflow-byoc.html#label-deployment-byoc-setup-runtime-role) to use SNOWFLAKE\_MANAGED\_TOKEN. * **BYOC:** Alternatively BYOC can use KEY\_PAIR as the value for authentication strategy. | Yes |
| Snowflake Private Key | When using:   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Must be the RSA private key used for authentication.  The RSA key must be formatted according to PKCS8 standards and have standard PEM headers and footers.   Note that either a Snowflake Private Key File or a Snowflake Private Key must be defined. | No |
| Snowflake Private Key File | When using:   * **Session token authentication strategy**: The private key file must be blank. * **KEY\_PAIR**: Upload the file that contains the RSA private key used for authentication to Snowflake,   formatted according to PKCS8 standards and including standard PEM headers and footers.   The header line begins with `-----BEGIN PRIVATE`.   To upload the private key file, select the Reference asset checkbox. | No |
| Snowflake Private Key Password | When using   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Provide the password associated with the Snowflake Private Key File. | No |
| Snowflake Role | When using   * **Session Token Authentication Strategy**: Use your Snowflake Role.   You can find your Snowflake Role in the Openflow UI, by navigating to View Details for your Runtime. * **KEY\_PAIR** Authentication Strategy: Use a valid role configured for your service user. | Yes |
| Snowflake Username | When using   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Provide the user name used to connect to the Snowflake instance. | Yes |

See moreShow less

Expand

### Kinesis JSON Source Parameters

| Parameter | Description | Required |
| --- | --- | --- |
| AWS Region Code | The AWS region where your Kinesis Stream is located, for example `us-west-2`. | Yes |
| AWS Access Key ID | The AWS Access Key ID to connect to your Kinesis Stream, DynamoDB, and, optionally, CloudWatch. | Yes |
| AWS Secret Access Key | The AWS Secret Access Key to connect to your Kinesis Stream, DynamoDB, and, optionally, CloudWatch. | Yes |
| Kinesis Application Name | The name that is used for DynamoDB table name for tracking application’s progress on Kinesis Stream consumption. | Yes |
| Kinesis Consumer Type | The strategy used to read records from a Kinesis Stream. Must be one of the following values: `SHARED_THROUGHPUT` or `ENHANCED_FAN_OUT`. For more information, see [Develop enhanced fan-out consumers](https://docs.aws.amazon.com/streams/latest/dev/enhanced-consumers.html). | Yes |
| Kinesis Initial Stream Position | The initial stream position from which the data starts replication.  Possible values are:   * `LATEST`: Latest stored record * `TRIM_HORIZON`: Earliest stored record | Yes |
| Kinesis Stream Name | AWS Kinesis Stream Name to consume data from. | Yes |
| Metrics Publishing | Specifies where Kinesis Client Library metrics are published to. Possible values: `DISABLED`, `LOGS`, `CLOUDWATCH`. | Yes |

See moreShow less

Expand

## Run the flow

1. Right-click the plane and select Enable all Controller Services.
2. Right-click the connector’s process group and select Start.

The connector starts the data ingestion.

### Table schema

The Snowflake table loaded by the connector contains columns named by the keys of your Kinesis messages.
The connector also adds a `KINESISMETADATA` column which stores metadata about the record.

Below is an example of a Snowflake table loaded by the connector:

| Row | ACCOUNT | SYMBOL | SIDE | QUANTITY | KINESISMETADATA |
| --- | --- | --- | --- | --- | --- |
| 1 | ABC123 | ZTEST | BUY | 3572 | { … KINESISMETADATA object … } |
| 2 | XYZ789 | ZABZX | SELL | 3024 | { … KINESISMETADATA object … } |
| 3 | XYZ789 | ZTEST | SELL | 799 | { … KINESISMETADATA object … } |
| 4 | ABC123 | ZABZX | BUY | 2033 | { … KINESISMETADATA object … } |

See moreShow less

Expand

The `KINESISMETADATA` column contains an object with the following fields:

| Field Name | Field Type | Example Value | Description |
| --- | --- | --- | --- |
| `stream` | String | `stream-name` | The name of the Kinesis stream the record came from. |
| `shardId` | String | `shardId-000000000001` | The identifier of the shard in the stream the record came from. |
| `approximateArrival` | String | `2025-11-05T09:12:15.300` | The approximate time that the record was inserted into the stream (ISO 8601 format). |
| `partitionKey` | String | `key-1234` | The partition key specified by the data producer for the record. |
| `sequenceNumber` | String | `123456789` | The unique sequence number assigned by Kinesis Data Streams to the record in the shard. |
| `subSequenceNumber` | Number | `2` | The subsequence number for the record (used for aggregated records with the same sequence number). |
| `shardedSequenceNumber` | String | `12345678900002` | A combination of the sequence number and the subsequence number for the record. |

See moreShow less

Expand

#### Schema evolution

The connector supports automatic schema detection and evolution. The structure
of tables in Snowflake is defined and evolved automatically to support the structure
of new data loaded by the connector.

Snowflake detects the schema of the incoming data and loads data into tables
that match any user-defined schema. Snowflake also allows adding
new columns or dropping the `NOT NULL` constraint from columns missing in new incoming records.

Schema detection with the connector infers data types based on the JSON data provided.

If the connector creates the target table, schema evolution is enabled by default.

If you want to enable or disable schema evolution on an existing table,
use the [ALTER TABLE](../../../../../sql-reference/sql/alter-table) command to set the `ENABLE_SCHEMA_EVOLUTION` parameter.
You must also use a role that has the `OWNERSHIP` privilege on the table. For more information, see [Enable automatic table schema evolution](../../../../data-load-schema-evolution).

However, if schema evolution is disabled for an existing table, then the connector
tries to send the rows with mismatched schemas to the configured failure output port.

## Iceberg table support

Openflow Connector for Kinesis can ingest data into a Snowflake-managed [Apache Iceberg™ table](../../../../tables-iceberg) when **Iceberg Enabled** is set to *true*.

### Requirements and limitations

Before you configure Openflow Connector for Kinesis for Iceberg table ingestion, note the following requirements and limitations:

* You must create an Iceberg table before running the connector.
* Make sure that the user has access to inserting data into the created tables.

### Configuration and setup

To configure Openflow Connector for Kinesis for Iceberg table ingestion, follow the steps in [Set up Openflow Connector for Kinesis for JSON data format](#) with a few differences noted in the following sections.

#### Enable ingestion into Iceberg table

To enable ingestion into an Iceberg table, you must set the `Iceberg Enabled` parameter to `true`.

#### Create an Iceberg table for ingestion

Before you run the connector, you must create an Iceberg table.
The initial table schema depends on your connector `Schema Evolution Enabled` property settings.

If schema evolution is enabled, you must create a table with a column named `kinesisMetadata`.
The connector automatically creates the columns for message fields and alters the `kinesisMetadata` column schema.

CopyExpand

```
CREATE OR REPLACE ICEBERG TABLE my_iceberg_table (
    kinesisMetadata OBJECT()
  )
  EXTERNAL_VOLUME = 'my_volume'
  CATALOG = 'SNOWFLAKE'
  BASE_LOCATION = 'my_location/my_iceberg_table'
  ENABLE_SCHEMA_EVOLUTION = true;
```

Show lessSee more

Scroll to top

If schema evolution is disabled, you must create the table with all fields the Kinesis message contains.
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

The following statement creates a table with all fields the Kinesis message contains:

CopyExpand

```
CREATE OR REPLACE ICEBERG TABLE my_iceberg_table (
    kinesisMetadata OBJECT(
        stream STRING,
        shardId STRING,
        approximateArrival STRING,
        partitionKey STRING,
        sequenceNumber STRING,
        subSequenceNumber INTEGER,
        shardedSequenceNumber STRING
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

Note

`kinesisMetadata` must always be created. Field names inside nested structures such as `dogs` or `cats` are case sensitive.
