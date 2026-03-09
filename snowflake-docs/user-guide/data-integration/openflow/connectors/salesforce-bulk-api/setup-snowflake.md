---
title: "Openflow Connector for Salesforce Bulk API: Set up Snowflake"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/salesforce-bulk-api/setup-snowflake"
---

# Openflow Connector for Salesforce Bulk API: Set up Snowflake

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) [Preview Feature](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/preview-terms-of-service/)

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes the steps to set up Snowflake for the Openflow Connector for Salesforce Bulk API.

## Prerequisites

Before you begin, ensure you have completed the following:

* Install Openflow (either BYOC or SPCS). For more information, see [About Openflow](../../about).
* Create an Openflow deployment. For more information, see [Set up Openflow - Snowflake Deployment: Create deployment](../../setup-openflow-spcs-deployment) or [Set up Openflow - BYOC](../../setup-openflow-byoc).
* Create an Openflow runtime. For more information, see [Set up Openflow - Snowflake Deployment: Create runtime](../../setup-openflow-spcs-create-runtime) or [Set up Openflow - BYOC](../../setup-openflow-byoc).
* Review the known limitations of the preview connector in [About the Openflow Connector for Salesforce Bulk API](about).

## Create a key pair

Create a key pair that will be used by the service account user in the connector to interact with the database.

Note

This step is only required if you are deploying the connector in Openflow BYOC. It is NOT needed when deploying the connector in Openflow SPCS.

1. Generate a private key. The example below shows how to generate an unencrypted private key.

   CopyExpand

   ```
   openssl genrsa 2048 | openssl pkcs8 -topk8 -inform PEM -out rsa_key.p8 -nocrypt
   ```

   Show lessSee more

   Scroll to top

   The content of the `rsa_key.p8` file will look like this:

   CopyExpand

   ```
   -----BEGIN PRIVATE KEY-----
   MIIE6T...
   -----END PRIVATE KEY-----
   ```

   Show lessSee more

   Scroll to top
2. Generate the public key by referencing the private key.

   CopyExpand

   ```
   openssl rsa -in rsa_key.p8 -pubout -out rsa_key.pub
   ```

   Show lessSee more

   Scroll to top

   The content of the `rsa_key.pub` file will look like this:

   CopyExpand

   ```
   -----BEGIN PUBLIC KEY-----
   MIIBIjANBgkqh...
   -----END PUBLIC KEY-----
   ```

   Show lessSee more

   Scroll to top

   Copy the contents of this file (without the `-----BEGIN PUBLIC KEY-----` and `-----END PUBLIC KEY-----` headers) to use when creating the user in the next section.

## Create objects and grant privileges

Create a service account, role, database, schema, and warehouse for the connector, and grant the appropriate permissions.

1. Use a role with `ACCOUNTADMIN` privileges to set the role:

   CopyExpand

   ```
   USE ROLE ACCOUNTADMIN;
   ```

   Show lessSee more

   Scroll to top
2. Create the destination Snowflake database, if it does not
   exist:

   CopyExpand

   ```
   CREATE DATABASE IF NOT EXISTS <my_salesforce_db>;
   ```

   Show lessSee more

   Scroll to top
3. Create the destination schema in the database, if it does
   not exist:

   CopyExpand

   ```
   CREATE SCHEMA IF NOT EXISTS <my_salesforce_db>.<my_salesforce_schema>;
   ```

   Show lessSee more

   Scroll to top
4. Create the role used by the Openflow connector:

   CopyExpand

   ```
   CREATE ROLE IF NOT EXISTS <Salesforce_connector_role_name>;
   ```

   Show lessSee more

   Scroll to top
5. Grant the privileges to the role to use the database:

   CopyExpand

   ```
   GRANT USAGE ON DATABASE <my_salesforce_db> TO ROLE <Salesforce_connector_role_name>;
   GRANT USAGE ON SCHEMA <my_salesforce_db>.<my_salesforce_schema> TO ROLE <Salesforce_connector_role_name>;
   GRANT CREATE TABLE ON SCHEMA <my_salesforce_db>.<my_salesforce_schema> TO ROLE <Salesforce_connector_role_name>;
   ```

   Show lessSee more

   Scroll to top
6. Create a warehouse for the connector (or use an existing one) and grant usage privileges to the connector role:

   CopyExpand

   ```
   -- Create a warehouse (skip if you wish to use an existing warehouse)
   CREATE OR REPLACE WAREHOUSE MY_WAREHOUSE WITH
    WAREHOUSE_SIZE = 'SMALL'
    AUTO_SUSPEND = 300
    AUTO_RESUME = TRUE;

   GRANT USAGE, OPERATE ON WAREHOUSE MY_WAREHOUSE TO ROLE <Salesforce_connector_role_name>;
   ```

   Show lessSee more

   Scroll to top
7. Create the service user and assign the role and public key:

   CopyExpand

   ```
   -- Create a service user that the connector will use to interact with Snowflake
   -- Set default role to <Salesforce_connector_role_name>
   -- Assign the public key generated with openssl in the previous step (only for BYOC)
   CREATE OR REPLACE USER <Salesforce_connector_user_name>
     TYPE = SERVICE
     DEFAULT_ROLE = <Salesforce_connector_role_name>
     RSA_PUBLIC_KEY = '<public_key_generated_by openssl_in_step_1>';

   -- Grant the role to the user
   GRANT ROLE <Salesforce_connector_role_name> TO USER <Salesforce_connector_user_name>;
   ```

   Show lessSee more

   Scroll to top

## Create a network rule (Openflow Snowflake Deployment only)

If you are deploying the connector in a runtime that is in an Openflow Snowflake Deployment, you must create a network rule and external access integration and set them on the runtime.

CopyExpand

```
USE ROLE SECURITYADMIN;

CREATE NETWORK RULE MY_OPENFLOW_SALESFORCE_NETWORK_RULE
   TYPE = HOST_PORT
   MODE = EGRESS
   VALUE_LIST = ('<salesforce_instance_host>:443');

CREATE EXTERNAL ACCESS INTEGRATION MY_OPENFLOW_SALESFORCE_EAI
   ALLOWED_NETWORK_RULES = (MY_OPENFLOW_SALESFORCE_NETWORK_RULE)
   ENABLED = TRUE
   COMMENT = 'External Access Integration to connect to Salesforce';

GRANT USAGE ON INTEGRATION MY_OPENFLOW_SALESFORCE_EAI TO ROLE <openflow_role_name>;
```

Show lessSee more

Scroll to top

## Next steps

Configure the connector in Openflow:

[Openflow Connector for Salesforce Bulk API: Configure the connector](configure-connector)
