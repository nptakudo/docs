---
title: "Set up the Openflow Connector for Kafka"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/kafka/setup"
---

# Set up the Openflow Connector for Kafka

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

## Prerequisites

1. Ensure that you have reviewed [About Openflow Connector for Kafka](about).
2. Ensure that you have [Set up Openflow - BYOC](../../setup-openflow-byoc) or [Set up Openflow - Snowflake Deployments](../../setup-openflow-spcs).
3. If using Openflow - Snowflake Deployments, ensure that you’ve reviewed [configuring required domains](../../setup-openflow-spcs-sf-allow-list)
   and have granted access to the required domains for the [Kafka](../../setup-openflow-spcs-sf-allow-list.html#label-openflow-domains-used-by-openflow-connectors-kafka) connector.
4. Ensure that your Kafka cluster is running version 0.10.0.0 or later. Prior versions of Kafka are not supported.

### Required network rule (Snowflake Deployment)

If you are using Snowflake Deployment, your Kafka cluster must be reachable from within the Snowflake deployment. This requires creating a network rule that includes all Kafka broker host:port pairs, not only the bootstrap servers. For details on creating network rules and External Access Integrations, see [Creating Network Rules and External Access Integrations](../../setup-openflow-spcs-create-rr.html#label-create-network-rules-and-external-access-integrations).

## Connector types

The Openflow Connector for Kafka is available in three different configurations, each optimized for specific use cases. You can download these connector definitions from the connectors gallery:

Apache Kafka for JSON data format
:   Simplified connector for JSON message ingestion with schema evolution and topic-to-table mapping

Apache Kafka for AVRO data format
:   Simplified connector for AVRO message ingestion with schema evolution and topic-to-table mapping

Apache Kafka with DLQ and metadata
:   Full-featured connector with dead letter queue (DLQ) support, metadata handling, and feature parity with the [Snowflake connector for Kafka](../../../../kafka-connector-overview)

For detailed configuration of specific connector types, see:

* [Apache Kafka for JSON/AVRO data format](kafka-json-avro) - JSON/AVRO data format connectors
* [Apache Kafka with DLQ and metadata](kafka-dlq-metadata) - DLQ and metadata connector

### Which connector should you choose?

Choose the connector variant that best matches your data format, operational requirements, and feature needs:

Choose [Apache Kafka for JSON or AVRO data format](kafka-json-avro) when:

* Your Kafka messages are in JSON or AVRO format
* You need basic schema evolution capabilities
* You want a simple setup with minimal configuration
* You don’t require advanced error handling or dead letter queue functionality
* You’re setting up a new integration and want to get started quickly

*Format-specific considerations:*

* **JSON format**: More flexible for varied data structures, easier to debug and inspect
* **AVRO format**: Strongly typed data with built-in schema registry integration, better for structured data pipelines

Choose [Apache Kafka with DLQ and metadata](kafka-dlq-metadata) when:

* You’re migrating from the [Snowflake connector for Kafka](../../../../kafka-connector-overview) and need feature parity with compatible functionality
* You need robust error handling and dead letter queue support for failed messages
* You require detailed metadata about message ingestion (timestamps, offsets, headers)

#### Migration considerations

If you’re currently using the Snowflake connector for Kafka, choose the **Apache Kafka with DLQ and metadata** connector for a seamless migration experience with feature compatibility.

**Field name handling differences**: The Openflow Connector for Kafka handles special characters in field names differently from the Snowflake connector for Kafka. After migration, the Openflow Connector for Kafka may create new Snowflake columns with different names due to these naming convention differences. For detailed information about how field names are transformed, see [Field name mapping and special characters handling](about.html#label-field-mapping).

#### Performance considerations

* JSON and AVRO format connectors offer better performance for simple use cases due to their streamlined design
* The DLQ and metadata connector provides more comprehensive monitoring and error handling at the cost of slightly higher resource usage

## Set up Snowflake account

As a Snowflake account administrator, perform the following tasks:

1. Create a new Snowflake service user with the type as [SERVICE](../../../../../sql-reference/sql/create-user.html#label-user-type-property).
2. Create a new role or use an existing role and grant the [Database privileges](../../../../security-access-control-privileges.html#label-database-privileges).

   Since the connector has the capability to automatically create the destination table if it does not already exist, make sure the user has the required privileges for creating and managing Snowflake objects:

   | Object | Privilege | Notes |
   | --- | --- | --- |
   | Database | USAGE |  |
   | Schema | USAGE . CREATE TABLE . | After the schema-level objects have been created, the CREATE `object` privileges can be revoked. |
   | Table | OWNERSHIP | Only required when using the Kafka connector to ingest data into an existing table. . If the connector creates a new target table for records from the Kafka topic, the default role for the user specified in the configuration becomes the table owner. |

   See moreShow less

   Expand

   Snowflake recommends creating a separate user and role for each Kafka instance for better access control.

   You can use the following script to create and configure a custom role (requires SECURITYADMIN or equivalent):

   CopyExpand

   ```
   USE ROLE securityadmin;

   CREATE ROLE kafka_connector_role_1;
   GRANT USAGE ON DATABASE kafka_db TO ROLE kafka_connector_role_1;
   GRANT USAGE ON SCHEMA kafka_schema TO ROLE kafka_connector_role_1;
   GRANT CREATE TABLE ON SCHEMA kafka_schema TO ROLE kafka_connector_role_1;

   -- Only for existing tables
   GRANT OWNERSHIP ON TABLE existing_table1 TO ROLE kafka_connector_role_1;
   ```

   Show lessSee more

   Scroll to top

   Note that privileges must be granted directly to the connector role and cannot be inherited.
3. Grant the Snowflake service user the role you created in the previous steps.

   The role should be assigned as the default role for the user:

   CopyExpand

   ```
   GRANT ROLE kafka_connector_role_1 TO USER kafka_connector_user_1;
   ALTER USER kafka_connector_user_1 SET DEFAULT_ROLE = kafka_connector_role_1;
   ```

   Show lessSee more

   Scroll to top
4. Configure with [key-pair auth](../../../../key-pair-auth) for the Snowflake SERVICE user from step 1.
5. Snowflake strongly recommends this step. Configure a secrets manager supported by Openflow, for example, AWS, Azure, and Hashicorp, and store the public and private keys in the secret store.

   Note

   If for any reason, you do not wish to use a secrets manager, then you are responsible for safeguarding the
   public key and private key files used for key-pair authentication according to the security policies of your organization.

   1. Once the secrets manager is configured, determine how you will authenticate to it. On AWS, it’s recommended that you the
      EC2 instance role associated with Openflow as this way no other secrets have to be persisted.
   2. In Openflow, configure a Parameter Provider associated with this Secrets Manager, from the hamburger menu in the upper right.
      Navigate to Controller Settings » Parameter Provider and then fetch your parameter values.
   3. At this point all credentials can be referenced with the associated parameter paths and no sensitive values need to be persisted within Openflow.
6. If any other Snowflake users require access to the raw ingested documents and tables ingested by the connector (for example, for custom processing in Snowflake),
   then grant those users the role created in step 1.

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

1. Populate the process group parameters

   1. Right click on the imported process group and select **Parameters**.
   2. Fill out the required parameter values as described in [Common parameters](#common-parameters).

### Common parameters

All Kafka connector variants share common parameter contexts for basic connectivity and authentication.

#### Snowflake destination parameters

| Parameter | Description | Required |
| --- | --- | --- |
| Destination Database | The database where data will be persisted. It must already exist in Snowflake. The name is case-sensitive. For unquoted identifiers, provide the name in uppercase. | Yes |
| Destination Schema | The schema where data will be persisted, which must already exist in Snowflake. The name is case-sensitive. For unquoted identifiers, provide the name in uppercase.  See the following examples:  * `CREATE SCHEMA SCHEMA_NAME` or `CREATE SCHEMA schema_name`: use `SCHEMA_NAME` * `CREATE SCHEMA "schema_name"` or `CREATE SCHEMA "SCHEMA_NAME"`: use `schema_name` or `SCHEMA_NAME`, respectively | Yes |
| Snowflake Authentication Strategy | When using:   * **Snowflake Openflow Deployment** or **BYOC**: Use SNOWFLAKE\_MANAGED\_TOKEN.   This token is managed automatically by Snowflake.   BYOC deployments must have previously configured   [runtime roles](../../setup-openflow-byoc.html#label-deployment-byoc-setup-runtime-role) to use SNOWFLAKE\_MANAGED\_TOKEN. * **BYOC:** Alternatively BYOC can use KEY\_PAIR as the value for authentication strategy. | Yes |
| Snowflake Account Identifier | When using:   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Snowflake account name formatted as [organization-name]-[account-name] where data will be persisted. | Yes |
| Snowflake Private Key | When using:   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Must be the RSA private key used for authentication.  The RSA key must be formatted according to PKCS8 standards and have standard PEM headers and footers.   Note that either a Snowflake Private Key File or a Snowflake Private Key must be defined. | No |
| Snowflake Private Key File | When using:   * **Session token authentication strategy**: The private key file must be blank. * **KEY\_PAIR**: Upload the file that contains the RSA private key used for authentication to Snowflake,   formatted according to PKCS8 standards and including standard PEM headers and footers.   The header line begins with `-----BEGIN PRIVATE`.   To upload the private key file, select the Reference asset checkbox. | No |
| Snowflake Private Key Password | When using   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Provide the password associated with the Snowflake private key file. | No |
| Snowflake Role | When using   * **Session Token Authentication Strategy**: Use your Snowflake role.   You can find your Snowflake role in the Openflow UI, by navigating to View Details for your Runtime. * **KEY\_PAIR** Authentication Strategy: Use a valid role configured for your service user. | Yes |
| Snowflake Username | When using   * **Session Token Authentication Strategy**: Must be blank. * **KEY\_PAIR**: Provide the user name used to connect to the Snowflake instance. | Yes |
| Oversized Value Strategy | Determines how the connector handles values that exceed its internal size limits (16 MB) during replication. Possible values are:  * **Fail Table** (default): The table is marked as permanently failed, and replication stops for that table. * **Set Null**: The value is replaced with `NULL` in the destination table.   Use this to prevent table failures when it is acceptable to lose data in tables beyond the oversized value. | No |
| Snowflake Warehouse | Snowflake warehouse used to run queries. | Yes |

See moreShow less

Expand

#### Kafka source parameters (SASL authentication)

| Parameter | Description | Required |
| --- | --- | --- |
| Kafka Security Protocol | Security protocol used to communicate with brokers. Corresponds to Kafka Client security.protocol property. One of: *SASL\_PLAINTEXT* / *SASL\_SSL* | Yes |
| Kafka SASL Mechanism | SASL mechanism used for authentication. Corresponds to Kafka Client sasl.mechanism property. One of: *PLAIN* / *SCRAM-SHA-256* / *SCRAM-SHA-512* | Yes |
| Kafka SASL Username | The username to authenticate to Kafka | Yes |
| Kafka SASL Password | The password to authenticate to Kafka | Yes |
| Kafka Bootstrap Servers | A comma-separated list of Kafka broker to fetch data from, should contain port, for example kafka-broker:9092. The same instance is used for the DLQ topic. | Yes |

See moreShow less

Expand

#### Kafka ingestion parameters

| Parameter | Description | Required |
| --- | --- | --- |
| Kafka Topic Format | One of: *names* / *pattern*. Specifies whether the “Kafka Topics” provided are a comma separated list of names or a single regular expression. | Yes |
| Kafka Topics | A comma-separated list of Kafka topics or a regular expression. | Yes |
| Kafka Group Id | The ID of a consumer group used by the connector. Can be arbitrary but must be unique. | Yes |
| Kafka Auto Offset Reset | Automatic offset configuration applied when no previous consumer offset is found corresponding to Kafka `auto.offset.reset` property. One of: *earliest* / *latest.* Default: *latest* | Yes |
| Topic To Table Map | This optional parameter allows user to specify which topics should be mapped to which tables. Each topic and its table name should be separated by a colon (see example below). This table name must be a valid Snowflake unquoted identifier. The regular expressions cannot be ambiguous — any matched topic must match only a single target table. If empty or no matches found, topic name will be used as table name. Note: The mapping cannot contain spaces after commas. | No |

See moreShow less

Expand

`Topic To Table Map` example values:

* `topic1:low_range,topic2:low_range,topic5:high_range,topic6:high_range`
* `topic[0-4]:low_range,topic[5-9]:high_range`
* `.*:destination_table` - maps all topics to the **destination\_table**

## Configure variant-specific settings

After configuring the common parameters, you need to configure settings specific to your chosen connector variant:

For **Apache Kafka for JSON data format** and **Apache Kafka for AVRO data format** connectors:
:   See [Apache Kafka for JSON/AVRO data format](kafka-json-avro) for JSON/AVRO-specific parameters.

For **Apache Kafka with DLQ and metadata** connector:
:   See [Apache Kafka with DLQ and metadata](kafka-dlq-metadata) for advanced parameters including DLQ configuration, schematization settings, Iceberg table support, and message format options.

## Authentication

All connector variants support SASL authentication configured through parameter contexts as described in [Kafka source parameters (SASL authentication)](#kafka-source-parameters-sasl-authentication).

For other authentication methods including mTLS and AWS MSK IAM, see [Configure other authentication methods for Openflow Connector for Kafka](authentication).

## Run the flow

1. Right click on the plane and click **Enable all Controller Services**.
2. Right click on the plane and click **Start**. The connector starts data ingestion.
