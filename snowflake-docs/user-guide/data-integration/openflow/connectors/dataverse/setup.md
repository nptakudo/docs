---
title: "Set up the Openflow Connector for Microsoft Dataverse"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/dataverse/setup"
---

# Set up the Openflow Connector for Microsoft Dataverse

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes the steps to set up the Openflow Connector for Microsoft Dataverse.

## Prerequisites

1. Ensure that you have reviewed [About Openflow Connector for Microsoft Dataverse](about).
2. Ensure that you have [Set up Openflow - BYOC](../../setup-openflow-byoc) or [Set up Openflow - Snowflake Deployments](../../setup-openflow-spcs).
3. If using Openflow - Snowflake Deployments, ensure that you’ve reviewed [configuring required domains](../../setup-openflow-spcs-sf-allow-list)
   and have granted access to the required domains for the [Microsoft Dataverse](../../setup-openflow-spcs-sf-allow-list.html#label-openflow-domains-used-by-openflow-connectors-dataverse) connector.

## Get the credentials

As a Microsoft Dataverse administrator, perform the following steps:

1. Ensure you have a Dataverse Environment to work with, and you have
   access to that environment through
   <https://admin.powerplatform.microsoft.com/>.
2. Ensure that you have an application registered in Microsoft Entra ID in portal.azure.com. This application must have
   access to the tenant we have our Dataverse Environment available. To register the application follow
   [this guide](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/walkthrough-register-app-azure-active-directory).
3. Generate and store ClientID and Client Secret within that application.
4. Go to Power Apps Admin Center and configure your Dataverse Environment to be accessed via applications registered before.
   To do that, go to Manage » Environments and select the environment to configure. Then go to
   Settings » Users & permissions » Application users. Previously created applications
   must be added and granted with privileges necessary to read data from Microsoft Dataverse.
5. Copy and save the Environment URL of the selected Dataverse
   Environment from <https://admin.powerplatform.microsoft.com/>.

## Set up Snowflake account

As a Snowflake account administrator, perform the following tasks:

1. Create a Snowflake user with the type as [SERVICE](../../../../../sql-reference/sql/create-user.html#label-user-type-property).
   :   Create a database and schema to store the replicated data, and set up
       privileges for the service user to create tables in destination schema by granting the [USAGE and CREATE TABLE privileges](../../../../security-access-control-privileges.html#label-database-privileges).

       CopyExpand

       ```
       CREATE DATABASE <destination_database>;
       CREATE SCHEMA <destination_database>.<destination_schema>;
       CREATE USER <openflow_user> TYPE=SERVICE COMMENT='Service user for automated access of Openflow';
       CREATE ROLE <openflow_role>;
       GRANT ROLE <openflow_role> TO USER <openflow_user>;
       GRANT USAGE ON DATABASE <destination_database> TO ROLE <openflow_role>;
       GRANT USAGE ON SCHEMA <destination_database>.<destination_schema> TO ROLE <openflow_role>;
       GRANT CREATE TABLE ON SCHEMA <destination_database>.<destination_schema> TO ROLE <openflow_role>;
       CREATE WAREHOUSE <openflow_warehouse>
            WITH
                WAREHOUSE_SIZE = 'SMALL'
                AUTO_SUSPEND = 300
                AUTO_RESUME = TRUE;
       GRANT USAGE, OPERATE ON WAREHOUSE <openflow_warehouse> TO ROLE <openflow_role>;
       ```

       Show lessSee more

       Scroll to top

   1. Create a pair of secure keys (public and private). Store the private key for the user in a file to supply to the connector’s configuration.
      Assign the public key to the Snowflake service user:

      CopyExpand

      ```
      ALTER USER <openflow_user> SET RSA_PUBLIC_KEY = 'thekey';
      ```

      Show lessSee more

      Scroll to top

      For more information, see [pair of keys](../../../../key-pair-auth).
2. Snowflake strongly recommends this step. Configure a secrets manager supported by Openflow, for example, AWS, Azure, and Hashicorp, and store the public and private keys in the secret store.

   Note

   If for any reason, you do not wish to use a secrets manager, then you are responsible for safeguarding the
   public key and private key files used for key-pair authentication according to the security policies of your organization.

   1. Once the secrets manager is configured, determine how you will authenticate to it. On AWS, it’s recommended that you the
      EC2 instance role associated with Openflow as this way no other secrets have to be persisted.
   2. In Openflow, configure a Parameter Provider associated with this Secrets Manager, from the hamburger menu in the upper right.
      Navigate to Controller Settings » Parameter Provider and then fetch your parameter values.
   3. At this point all credentials can be referenced with the associated parameter paths and no sensitive values need to be persisted within Openflow.
3. If any other Snowflake users require access to the raw ingested documents and tables ingested by the connector (for example, for custom processing in Snowflake),
   then grant those users the role created in step 1.
4. Designate a warehouse for the connector to use. Grant the USAGE privilege on the warehouse to the role created before. Start with the smallest warehouse size, then experiment with size depending on the number of tables being replicated,
   and the amount of data transferred. Large table numbers typically scale better with
   [multi-cluster warehouses](../../../../warehouses-multicluster), rather than larger warehouse sizes.

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

1. Right-click on the imported process group and select Parameters.
2. Populate the required parameter values as described in [Flow parameters](#flow-parameters).

### Flow parameters

This section describes the flow parameters that you can configure based on the following parameter contexts:

* [Dataverse Source Parameters](#dataverse-source-parameters): Used to establish connection with Dataverse.
* [Dataverse Destination Parameters](#dataverse-destination-parameters): Used to establish connection with Snowflake.
* [Dataverse Ingestion Parameters](#dataverse-ingestion-parameters): Used to define the configuration of data downloaded from Dataverse.

#### Dataverse Source Parameters

| Parameter | Description |
| --- | --- |
| Source Dataverse Environment URL | The main identifier of a source system to fetch data. The URL indicates a namespace where Dataverse tables exist. It also lets you create a scope parameter for OAuth. |
| Source Tenant ID | Microsoft Azure Tenant ID. It’s used to create OAuth URLs. Microsoft Dataverse Environment must belong to this tenant. |
| Source OAuth Client ID | Microsoft Azure Client ID used to access Microsoft Dataverse API. [Microsoft Dataverse Web API](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/webapi/overview) uses OAuth authentication to secure access, and the connector uses the client credentials flow. To learn about client ID and how to find it in Microsoft Entra, see [Application ID (client ID)](https://learn.microsoft.com/en-us/azure/healthcare-apis/register-application#application-id-client-id). |
| Source OAuth Client Secret | Microsoft Azure Client Secret used to access Microsoft Dataverse API. [Microsoft Dataverse Web API](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/webapi/overview) uses OAuth authentication to secure access, and the connector uses the client credentials flow. To learn about client secret and how to find it in Microsoft Entra, see [Certificates & secrets](https://learn.microsoft.com/en-us/azure/healthcare-apis/register-application#certificates--secrets). |

See moreShow less

Expand

#### Dataverse Destination Parameters

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

#### Dataverse Ingestion Parameters

| Parameter | Description |
| --- | --- |
| Scheduling Interval | Interval to be used as a triggering interval for the processor fetching list of tables and initializing ingestion. |
| Source Tables Filter Strategy | Strategy for filtering tables to be ingested. Can be one of REGEXP and LIST. |
| Source Tables Filter Value | Value of the tables filter. When Source Tables Filter Strategy is set to REGEXP - this is the regular expression to be matching selected tables. When LIST is provided, then it is a comma separated list of table names. |
| Column Filter JSON | Optional. A JSON containing a list of fully-qualified table names and a regex pattern for column names that should be included into replication. For example: `[ {"table": "table1", "includedPattern": ".*name"} ]` will include all columns that end with `name` in `table1`. |

See moreShow less

Expand

Note

When filtering tables, use the **entity set name** rather than the table name displayed
in the Microsoft Dataverse interface. To find the entity set name for a table, go to
[Power Apps](https://make.powerapps.com), select Tables, find your table,
then select Advanced » Tools » Copy set name.

## Run the flow

1. Right-click on the plane and select Enable all Controller Services.
2. Right-click on the imported process group and select Start. The connector starts the data ingestion.

### Replicate a subset of columns in a table

The connector can filter the data replicated per table to a subset of configured columns.

To apply filters to columns, modify the Replication Parameters context `Column Filter` property to specify a JSON filter.
Add an array of configurations, one entry for every table to which you want to apply a filter.

Columns can be included or excluded by name or pattern. You can apply a single condition per table,
or combine multiple conditions, with exclusions taking precedence over inclusions.

The following example shows the fields that are available. The `table` field is mandatory. One or
more of `included`, `excluded`, `includedPattern`, `excludedPattern` is required.

CopyExpand

```
[
    {
        "table" : "<source table name>",
        "included": ["<column name>", "<column name>"],
        "excluded": ["<column name>", "<column name>"],
        "includedPattern": "<regular expression>",
        "excludedPattern": "<regular expression>",
    }
]
```

Show lessSee more

Scroll to top
