---
title: "Set up the Openflow Connector for Jira Cloud"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/jira-cloud/setup"
---

# Set up the Openflow Connector for Jira Cloud

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes the steps to set up the Openflow Connector for Jira Cloud.

## Prerequisites

1. Ensure that you have reviewed [About Openflow Connector for Jira Cloud](about).
2. Ensure that you have [Set up Openflow - BYOC](../../setup-openflow-byoc) or [Set up Openflow - Snowflake Deployments](../../setup-openflow-spcs).
3. If using Openflow - Snowflake Deployments, ensure that you’ve reviewed [configuring required domains](../../setup-openflow-spcs-sf-allow-list)
   and have granted access to the required domains for the [Jira Cloud](../../setup-openflow-spcs-sf-allow-list.html#label-openflow-domains-used-by-openflow-connectors-jira-cloud) connector.

## Get the credentials

As a Jira Cloud administrator, perform the following tasks in your Atlassian account:

1. Navigate to the [API tokens page](https://id.atlassian.com/manage-profile/security/api-tokens).
2. Select Create API token with scopes.
3. In the Create an API token dialog box, provide a descriptive name for the API token and select an expiration date for the API token. This can range from 1 to 365 days.
4. Select the Api token app Jira.
5. Select jira scopes `read:jira-work` and `read:jira-user`.
6. Select Create token.
7. In the Copy your API token dialog box, select Copy to copy your generated API token and then paste the token to the connector parameters, or save it securely.
8. Select Close to close the dialog box.

## Set up Snowflake account

As a Snowflake account administrator, perform the following tasks:

1. Create a new role or use an existing role.
2. Create a new Snowflake service user with the type as [SERVICE](../../../../../sql-reference/sql/create-user.html#label-user-type-property).
3. Grant the Snowflake service user the role you created in the previous steps.
4. Configure with [key-pair auth](../../../../key-pair-auth) for the Snowflake SERVICE user from step 2.
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
7. Create a database and schema in Snowflake for the connector to store ingested data. Grant the following [Database privileges](../../../../security-access-control-privileges.html#label-database-privileges) to the role created in the first step.

   > CopyExpand
   >
   > ```
   > CREATE DATABASE jira_destination_db;
   > CREATE SCHEMA jira_destination_db.jira_destination_schema;
   > GRANT USAGE ON DATABASE jira_destination_db TO ROLE <jira_connector_role>;
   > GRANT USAGE ON SCHEMA jira_destination_db.jira_destination_schema TO ROLE <jira_connector_role>;
   > GRANT CREATE TABLE, CREATE VIEW ON SCHEMA jira_destination_db.jira_destination_schema TO ROLE <jira_connector_role>;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
8. Create a warehouse that will be used by the connector or use an existing one. Start with the smallest warehouse size, then experiment with size depending on the number of tables being replicated,
   and the amount of data transferred. Large table numbers typically scale better with
   [multi-cluster warehouses](../../../../warehouses-multicluster), rather than larger warehouse sizes.
9. Ensure that the user with role used by the connector has the required privileges to use the warehouse. If that’s not the case then grant the required privileges to the role.

   > CopyExpand
   >
   > ```
   > CREATE WAREHOUSE jira_connector_warehouse WITH WAREHOUSE_SIZE = 'X-Small';
   > GRANT USAGE ON WAREHOUSE jira_connector_warehouse TO ROLE <jira_connector_role>;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

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

* [Jira Cloud Source Parameters](#jira-cloud-source-parameters): Used to establish connection with Jira API.
* [Jira Cloud Destination Parameters](#jira-cloud-destination-parameters): Used to establish connection with Snowflake.
* [Jira Cloud Ingestion Parameters](#jira-cloud-ingestion-parameters): Used to define the configuration of data downloaded from Jira.

Note

Modifying the parameters related to ingestion configuration (for example, Search Type, JQL Query, Project Names, and Created After) will reset the state of the `FetchJiraIssues` processor,
allowing it to fetch all issues again. This is useful if you want to change the issue query criteria or restart the ingestion from scratch. This reset action does not truncate the destination table.

#### Jira Cloud Source Parameters

| Parameter | Description |
| --- | --- |
| Jira Email | Email address for the Atlassian account. |
| Jira API Token | API access token for your Atlassian Jira account with the necessary scopes (`read:jira-work` and `read:jira-user`). |
| Environment URL | URL to the Atlassian Jira environment. For example, `https://your-domain.atlassian.net`. |
| Connection Method | Must be set to `DIRECT` unless otherwise instructed by Snowflake. |

See moreShow less

Expand

#### Jira Cloud Destination Parameters

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

#### Jira Cloud Ingestion Parameters

| Parameter | Description |
| --- | --- |
| Search Type | Type of search to perform. It has one of these possible values `SIMPLE` and `JQL`. Default value: `SIMPLE`. |
| Destination Table | The Snowflake table where data is stored. It will be created if it doesn’t exist. The name of the table must be unquoted and must be provided in uppercase. Additionally to the destination table, a flattened view based on destination table is created. The view name is a concatenation of the table name and the suffix `_VIEW` |
| JQL Query | A JQL query used to search for Jira issues to fetch. It should be used only when Search Type is `JQL`. |
| Project Names | List of projects from which the issues should be fetched. You can search for issues belonging to a particular project by project name, project key, or project ID. It should be used only when Search Type is `SIMPLE`. Provide a list of items, separated by commas. For example: `Project1, Project2`. |
| Status Category | Status category filter for simple search. It should be used only when Search Type is `SIMPLE`. Example values are: `Done`, `In Progress`, `To Do`. |
| Updated After | Filter issues updated after a specified date and time. It should be used only when Search Type is `SIMPLE`. It should be in the yyyy-MM-dd format, such as 2023-10-01. |
| Created After | Filter issues created after a specified date and time. It should be used only when Search Type is `SIMPLE`. It should be in the yyyy-MM-dd format, such as 2023-10-01. |
| Issue Fields | A list of fields to return for each issue, which is used to retrieve a subset of fields. IDs of custom fields can be obtained by following [this guide](https://confluence.atlassian.com/jirakb/get-custom-field-ids-for-jira-and-jira-service-management-744522503.html). This parameter accepts a comma-separated list. You can use special values: `*all` to fetch all fields, `*navigable` to fetch navigable fields, field prefixed with minus (`-`) to exclude field. For example, `*all,-description` returns all fields except description. Default value: `*all`. |
| Fetch All Worklogs | Determines whether to fetch all worklogs for each issue. Default value: `false`.   * When set to `true`, the connector enriches issues with all associated worklogs, beyond the default 20 worklogs per issue returned by the Jira Cloud REST API. * When set to `false`, only the first 20 worklogs per issue are fetched.   Note  Setting this parameter to `true` can impact performance due to the increased number of API calls required to fetch all worklogs for issues with more than 20 worklogs. |
| Maximum Page Size | Maximum number of issues to return per request, with a default and maximum value of `1000`. Note that the Jira API may return fewer results depending on the total response size. |

See moreShow less

Expand

## Run the flow

1. Right-click on the plane and select Enable all Controller Services.
2. Right-click on the imported process group and select Start. The connector starts the data ingestion.

If you need to change the issue query criteria or want to restart the ingestion from scratch, perform the following steps to ensure that the data in the destination table is consistent:

1. Right-click on the FetchJiraIssues processor and stop it.
2. Right-click on the FetchJiraIssues processor and then select View State.
3. In the State dialog box, select Clear State. This action clears the state of the processor and allows it to fetch all issues again.
4. Optional: If you want to change the issue query criteria, right-click on the imported process group and select Parameters. Update the parameters as needed.
5. Optional: If you want to change the destination table name, right-click on the imported process group and select Parameters. Update the `Destination Table` parameter.
6. Right-click on the FetchJiraIssues processor and select Start. The connector starts the data ingestion.
7. After ingestion, the data is available in the Snowflake destination table and in a flattened format in the destination view. The view includes all fields available in the Jira instance.

## Accessing the data

Data fetched from Jira is available in the destination table. All fields fetched for Jira issue is available in the
`ISSUE` column as an object in raw form fetched from the API.

To help with querying the data, a flattened view is created based on the destination table. The view name is a
concatenation of the table name and the suffix `_VIEW`. For example, if the destination table is named `JIRA_ISSUES`,
then the view will be named `JIRA_ISSUES_VIEW`. In the view, all issue fields are extracted and available as separate
columns. The column name is set to the field label. If there are many issues with the same label, a suffix with field ID
is added to the column name to ensure uniqueness. For example, if there are two fields with IDs `customfield_1`,
`customfield_2`, the label set in both fields to `Custom Field`, then the columns in the view will be named
`Custom Field (customfield_1)`, `Custom Field (customfield_2)`.
