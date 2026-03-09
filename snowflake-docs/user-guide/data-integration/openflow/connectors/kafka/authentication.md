---
title: "Configure other authentication methods for Openflow Connector for Kafka"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/kafka/authentication"
---

# Configure other authentication methods for Openflow Connector for Kafka

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes how to configure other authentication methods for the
Openflow Connector for Kafka. The connector supports multiple authentication
mechanisms beyond basic SASL authentication.

Note

Basic SASL authentication is configured through parameter contexts as described in [Set up the Openflow Connector for Kafka](setup).
This page covers other authentication methods that require additional service configuration.

## Supported Authentication Methods

The Openflow Connector for Kafka supports the following authentication mechanisms:

* SASL with the following SASL mechanisms (configured via parameter contexts):

  + PLAIN
  + SCRAM-SHA-256
  + SCRAM-SHA-512
* SASL with AWS MSK IAM (extra configuration required via services)
* mTLS (extra configuration required via services)

## Configuring mTLS Authentication

mTLS (mutual Transport Layer Security) authentication requires both the client
and server to present certificates for mutual authentication.

### Prerequisites

Before configuring mTLS authentication, ensure you have:

1. Generated and configured the necessary certificates for both the connector and the Kafka broker
2. Created a keystore containing the connector’s private key and certificate
3. (Optional) Created a truststore containing the Kafka broker certificate or a certificate in the certification chain.
   This step is only required if the broker certificate is not signed by a trusted Certificate Authority (CA).
4. The supported keystore/truststore formats are PKCS12, JKS, and BCFKS

### Step 1: Configure SSL Context Service

1. From the NiFi canvas, access the Controller Services configuration:

   * Double click on the connector’s processing group
   * Right-click on the canvas and select Controller Services
2. Add a new StandardSSLContextService.

   * Click the + to add a new controller service.
   * Select StandardSSLContextService from the list.
   * Click Add.
3. Configure the SSL Context Service properties:

   | Property | Value |
   | --- | --- |
   | Keystore Filename | Full path to your keystore file (e.g., `/path/to/client-keystore.p12`), or Asset reference |
   | Keystore Password | Password for the keystore |
   | Keystore Type | Keystore format (`PKCS12`, `JKS`, or `BCFKS`) |
   | Key Password | Password for the private key (if the key is encrypted) |
   | Truststore Filename | Full path to your truststore file (e.g., `/path/to/client-truststore.p12`), or Asset reference |
   | Truststore Password | Password for the truststore |
   | Truststore Type | Truststore format (`PKCS12`, `JKS`, or `BCFKS`) |

   See moreShow less

   Expand
4. Enable the SSL Context Service:

   * Click Enable for the service.
   * Confirm that the service status shows as Enabled.

### Step 2: Configure Kafka3Connection Service

1. In the same Controller Services tab, locate the Kafka3Connection service.
2. Configure the following properties:

   | Property | Value |
   | --- | --- |
   | Security Protocol | `SSL` |
   | SSL Context Service | Select the SSL Context Service you created in Step 1 |

   See moreShow less

   Expand
3. Keep all other [Kafka3Connection service](../../controllers/kafka3connectionservice) settings unchanged
4. Verify the Kafka3Connection service:

   * Click Verify for the service.
   * Confirm that the service status shows as Verified.

## Configuring AWS MSK IAM Authentication

AWS MSK IAM authentication allows you to use AWS Identity and Access Management
(IAM) to authenticate to Amazon Managed Streaming for Apache Kafka (MSK).

### Prerequisites

1. Your Kafka cluster must be Amazon MSK with IAM authentication enabled.
2. You need to provide IAM credentials in Openflow with BYOC (bring your own cloud) configurations, deployed in your cloud.
3. The IAM role or user must have the necessary MSK permissions.

### Step 1: Create AmazonMSKConnectionService

1. From the NiFi canvas, access the Controller Services configuration:

   * Double click on the connector’s processing group
   * Right-click on the canvas and select Controller Services
2. Add a new [AmazonMSKConnectionService](../../controllers/amazonmskconnectionservice).

   * Click + to add a new controller service.
   * Select AmazonMSKConnectionService from the list.
   * Click Add
3. Configure the AmazonMSKConnectionService properties:

   | Property | Value |
   | --- | --- |
   | SASL Mechanism | `AWS_MSK_IAM` |
   | Security Protocol | `#{Kafka Security Protocol}` |
   | Bootstrap Servers | `#{Kafka Bootstrap Servers}` |

   See moreShow less

   Expand
4. Verify the AmazonMSKConnectionService:

   * Click Verify for the service
   * Confirm that the service status shows as Verified

### Step 2: Configure ConsumeKafka Processor

1. In your Kafka connector flow, locate the ConsumeKafka processor
2. Configure the processor to use the new connection service:

   * Set the Kafka Connection Service property to the AmazonMSKConnectionService you created in [Step 1: Create AmazonMSKConnectionService](#label-kafka-configure-aws-msk-iam-authentication).

### Step 3: Remove Old Kafka Connection Service

1. In the Controller Services tab, locate the old Kafka3Connection service.
2. Disable and remove the old service:

   * Click Disable for the old service.
   * Once disabled, click Delete to remove the old service.
