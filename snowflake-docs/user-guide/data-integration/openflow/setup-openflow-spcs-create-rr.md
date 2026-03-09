---
title: "Set up Openflow - Snowflake Deployment: Create Snowflake role"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/setup-openflow-spcs-create-rr"
---

# Set up Openflow - Snowflake Deployment: Create Snowflake role

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../intro-regions.html#label-na-general-regions).

Openflow - Snowflake Deployment requires the creation of a number of resources which are specific not to a deployment
but to a specific runtime. Typically such resources include:

* Creation of Runtime specific Snowflake role
* Creation of Runtime specific network rules and External Access Integrations (EAI)

This topic describes the creation of these resources.

1. Create a Snowflake Role and associated privileges to write data to Snowflake Role for Runtimes on Snowflake Deployment Section
2. Associate Snowflake Role. See Snowflake Role for Runtimes in the Snowflake Deployment Section.
3. Create External Access Integrations and associate them to Runtimes.
   See [Creating External Access Integrations](#label-create-network-rules-and-external-access-integrations)
4. When Outbound PrivateLink connectivity is required to connect to a private system using SPCS Egress.

## Create a Snowflake role

When creating and editing Openflow Runtimes, Runtime Owners will have the ability to associate a role with the Runtime.
This role will be used for flows that execute within the Runtime.
For more information about Snowflake Roles, see [What is a Snowflake role?](about-spcs.html#label-openflow-spcs-what-is-runtime-role).

Creating a Snowflake role is a prerequisite for creating a Runtime and involves the following steps:

1. Create the role itself
2. Grant the role access to the warehouse used by the Runtime.
3. Grant the role access to the Snowflake objects used by the Runtime.
4. Grant the role access to the External Access Integrations used by the Runtime.

To create a Snowflake role:

1. Create the required Snowflake role.

   Note

   `<RUNTIMENAME>` denotes the name of the associated runtime.

   CopyExpand

   ```
   USE ROLE ACCOUNTADMIN;

   CREATE ROLE IF NOT EXISTS OPENFLOW_RUNTIME_ROLE_<RUNTIMENAME>;

   GRANT ROLE OPENFLOW_RUNTIME_ROLE_<RUNTIMENAME> TO USER <username>;
   ```

   Show lessSee more

   Scroll to top
2. Allow the Snowflake role to use an existing warehouse that you are planning to use for data ingestion.
   Use this warehouse later when configuring your connectors for runtimes where you will be using this Snowflake role.

   CopyExpand

   ```
   GRANT USAGE, OPERATE ON WAREHOUSE <OPENFLOW_INGEST_WAREHOUSE> TO ROLE OPENFLOW_RUNTIME_ROLE_<RUNTIMENAME>;
   ```

   Show lessSee more

   Scroll to top
3. Allow the Snowflake role to use, create or otherwise access Snowflake objects.

   Note

   Depending on the Openflow connector being created the required underlying objects will vary.
   The example below is for illustration purposes only.

   CopyExpand

   ```
   GRANT USAGE ON DATABASE <OPENFLOW_SPCS_DATABASE> TO ROLE OPENFLOW_RUNTIME_ROLE_<RUNTIMENAME>;
   GRANT USAGE ON SCHEMA <OPENFLOW_SPCS_SCHEMA> TO ROLE OPENFLOW_RUNTIME_ROLE_<RUNTIMENAME>;
   ```

   Show lessSee more

   Scroll to top

### Creating Network Rules and External Access Integrations

Snowflake’s security model provides secure access to specific endpoints and systems
external to Snowflake using [network policies](../../network-policies).

Two key aspects of network policies are [Network rules](../../network-rules) and
[External Access Integrations (EAI)](../../../developer-guide/external-network-access/external-network-access-overview).
Each of which is used to provide secure access to external resources required by the runtime.

There are three steps that are required to create network rules and external access integrations:

1. Create the network rule, grouping the network identifiers into logical areas.
2. Create the external access integration (EAI), specifying the list of network rules and assuring the Snowflake Role has USAGE on the EAI.
3. Associate the EAI with the Runtime in the Openflow UI when creating Runtimes.

To create the required network rule and EAI, perform the following steps:

Note

These examples use RUNTIME\_NAME as a placeholder for the name of the Runtime being created.

1. Create an appropriate network rule. See [CREATE NETWORK RULE](../../../sql-reference/sql/create-network-rule) for more information.

   Note

   `<OPENFLOW_DATABASE>` denotes the name of the database that will contain the network rule.
   Snowflake suggests creating a specific database for network rules and external access integrations related to Openflow.

   CopyExpand

   ```
   USE DATABASE <OPENFLOW_DATABASE>;

   CREATE NETWORK RULE IF NOT EXISTS OPENFLOW_<RUNTIME_NAME>_NETWORK_RULE
       MODE = EGRESS
       TYPE = HOST_PORT
       VALUE_LIST = ('comma separated list of host:port pairs');
   ```

   Show lessSee more

   Scroll to top
2. Create an external access integration, or add the network rule to an existing one.
   See [CREATE EXTERNAL ACCESS INTEGRATION](../../../sql-reference/sql/create-external-access-integration) for more information.

   To create a new EAI:

   CopyExpand

   ```
   USE ROLE ACCOUNTADMIN;

   CREATE EXTERNAL ACCESS INTEGRATION IF NOT EXISTS OPENFLOW_<RUNTIME_NAME>_EAI
      ALLOWED_NETWORK_RULES = (OPENFLOW_<RUNTIME_NAME>_NETWORK_RULE)
      ENABLED = TRUE;
   ```

   Show lessSee more

   Scroll to top

   To add the network rule to an existing EAI, first check which rules are already
   associated with it, then update the EAI to include both the existing and new rules:

   CopyExpand

   ```
   USE ROLE ACCOUNTADMIN;

   -- Check the current rules on the EAI
   DESCRIBE EXTERNAL ACCESS INTEGRATION OPENFLOW_<RUNTIME_NAME>_EAI;
   ```

   Show lessSee more

   Scroll to top

   In the output, find the `ALLOWED_NETWORK_RULES` property and note the existing rules.
   Then update the EAI, listing all existing rules along with the new one:

   CopyExpand

   ```
   ALTER EXTERNAL ACCESS INTEGRATION OPENFLOW_<RUNTIME_NAME>_EAI
      SET ALLOWED_NETWORK_RULES = (
         <EXISTING_RULE_1>,
         <EXISTING_RULE_2>,
         OPENFLOW_<RUNTIME_NAME>_NETWORK_RULE
      );
   ```

   Show lessSee more

   Scroll to top
3. Grant access to the EAI to the previously created Snowflake role.

   CopyExpand

   ```
   GRANT USAGE ON INTEGRATION OPENFLOW_<RUNTIME_NAME>_EAI TO ROLE OPENFLOW_RUNTIME_ROLE_<RUNTIME_NAME>;
   ```

   Show lessSee more

   Scroll to top

## Next steps

[Create runtime](setup-openflow-spcs-create-runtime)
