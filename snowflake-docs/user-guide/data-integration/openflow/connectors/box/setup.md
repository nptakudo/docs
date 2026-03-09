---
title: "Set up the Openflow Connector for Box"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/box/setup"
---

# Set up the Openflow Connector for Box

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) [Preview Feature](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/preview-terms-of-service/)

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes the steps to set up the Openflow Connector for Box.

## Prerequisites

1. Ensure that you have reviewed [About Openflow Connector for Box](about).
2. Ensure that you have [Set up Openflow - BYOC](../../setup-openflow-byoc) or [Set up Openflow - Snowflake Deployments](../../setup-openflow-spcs).
3. If using Openflow - Snowflake Deployments, ensure that you have reviewed [configuring requireddomains](../../setup-openflow-spcs-sf-allow-list)
   and have granted access to the required domains for the [Box](../../setup-openflow-spcs-sf-allow-list.html#label-openflow-domains-used-by-openflow-connectors-box) connector.

## Get the credentials

As a **Box developer** or **Box administrator**, create a [Box Platform application](https://developer.box.com/guides/applications/app-types/platform-apps/) as follows:

1. Navigate to [Box Developer Console](https://app.box.com/developers/console).
2. Select Create Platform App.
3. Select Custom App as the application type.
4. Provide a name and description for the app, and select a purpose from the drop-down list.
5. Select Server Authentication (with JWT) as the authentication method.
6. Select Create App.
7. To configure the app, navigate to the Configuration tab.
8. In the App Access Level section, select App + Enterprise Access.
9. In the Application Scopes section, select the following options:

   > * Read all files and folders stored in Box.
   > * Write all files and folders stored in Box: To download files and folders. Note that the connector can’t upload any files.
   >   Snowflake recommends granting the service account with only the Viewer role.
   >   To grant the application access to files in Box, select a folder that you want to synchronize. Share it with the app service account using the email of the service account from step n.
   >   Openflow Connector for Box is able to discover and download files from the specified folder and all its subfolders, but it cannot modify the files.
   > * Manage users: To read users in the enterprise.
   > * Manage groups: To read groups and their members in the enterprise.
   > * Manage enterprise properties: To read enterprise events.
10. In the Add and Manage Public Keys section, generate a public/private key pair. Box downloads a JSON configuration file with a private key.
11. Save the changes.
12. Navigate to the Authorization tab, and submit the app for authorization for access to the enterprise.
13. Request your enterprise administrator to approve the app.
14. After the approval is granted, go to the General Settings tab and save the app service account email address.

    For more information, see [Setup with JWT](https://developer.box.com/guides/authentication/jwt/jwt-setup/).

## Set up Snowflake account

As a Snowflake account administrator, perform the following tasks manually
or by using the script included below:

1. Create a new role or use an existing role and grant the [Database privileges](../../../../security-access-control-privileges.html#label-database-privileges).
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
7. Designate a warehouse for the connector to use. Start with the smallest warehouse size, then experiment with size depending on the number of tables being replicated,
   and the amount of data transferred. Large table numbers typically scale better with
   [multi-cluster warehouses](../../../../warehouses-multicluster), rather than larger warehouse sizes.

### Example setup

> CopyExpand
>
> ```
> --The following script assumes you'll need to create all required roles, users, and objects.
> --However, you may want to reuse some that are already in existence.
>
> --Create a Snowflake service user to manage the connector
> USE ROLE USERADMIN;
> CREATE USER <openflow_service_user> TYPE=SERVICE COMMENT='Service user for Openflow automation';
>
> --Create a pair of secure keys (public and private). For more information, see
> --key-pair authentication. Store the private key for the user in a file to supply
> --to the connector’s configuration. Assign the public key to the Snowflake service user:
> ALTER USER <openflow_service_user> SET RSA_PUBLIC_KEY = '<pubkey>';
>
>
> --Create a role to manage the connector and the associated data and
> --grant it to that user
> USE ROLE SECURITYADMIN;
> CREATE ROLE <openflow_connector_admin_role>;
> GRANT ROLE <openflow_connector_admin_role> TO USER <openflow_service_user>;
>
>
> --The following block is for the use case: Ingest files and perform processing with Cortex
> --Create a role for read access to the cortex search service created by this connector.
> --This role should be granted to any role that will use the service
> CREATE ROLE <cortex_search_service_read_only_role>;
> GRANT ROLE <cortex_search_service_read_only_role> TO ROLE <whatever_roles_will_access_search_service>;
>
> --Create the database the data will be stored in and grant usage to the roles created
> USE ROLE ACCOUNTADMIN; --use whatever role you want to own your DB
> CREATE DATABASE IF NOT EXISTS <destination_database>;
> GRANT USAGE ON DATABASE <destination_database> TO ROLE <openflow_connector_admin_role>;
>
> --Create the schema the data will be stored in and grant the necessary privileges
> --on that schema to the connector admin role:
> USE DATABASE <destination_database>;
> CREATE SCHEMA IF NOT EXISTS <destination_schema>;
> GRANT USAGE ON SCHEMA <destination_schema> TO ROLE <openflow_connector_admin_role>;
> GRANT CREATE TABLE, CREATE DYNAMIC TABLE, CREATE STAGE, CREATE SEQUENCE, CREATE CORTEX
> SEARCH SERVICE ON SCHEMA <destination_schema> TO ROLE <openflow_connector_admin_role>;
>
> --The following block is for use case: Ingest files and perform processing with Cortex
> --Grant the Cortex read-only role access to the database and schema
> GRANT USAGE ON DATABASE <destination_database> TO ROLE <cortex_search_service_read_only_role>;
> GRANT USAGE ON SCHEMA <destination_schema> TO ROLE <cortex_search_service_read_only_role>;
>
> --Create the warehouse this connector will use if it doesn't already exist. Grant the
> --appropriate privileges to the connector admin role. Adjust the size according to your needs.
> CREATE WAREHOUSE <openflow_warehouse>
> WITH
>    WAREHOUSE_SIZE = 'MEDIUM'
>    AUTO_SUSPEND = 300
>    AUTO_RESUME = TRUE;
> GRANT USAGE, OPERATE ON WAREHOUSE <openflow_warehouse> TO ROLE <openflow_connector_admin_role>;
> ```
>
> Show lessSee more
>
> Scroll to top

## Use cases

You can configure the connector for the following use cases:

* [Ingest files only](#ingest-files-only)
* [Ingest files and perform processing with Cortex](#ingest-files-and-perform-processing-with-cortex)
* [Extract Box metadata using Box AI and ingest it into a Snowflake table](#extract-box-metadata-using-box-ai-and-ingest-it-into-a-snowflake-table)
* [Synchronize Box file metadata instances with a Snowflake table](#synchronize-box-file-metadata-instances-with-a-snowflake-table)

### Ingest files only

Use the connector definition to:

* Process the ingested files with [Document AI](../../../../snowflake-cortex/document-ai/overview).
* Perform custom processing on ingested files.

#### Set up the connector

As a data engineer, perform the following tasks to install and configure the connector:

##### Install the connector

To install the connector, do the following as a data engineer:

1. Navigate to the Openflow overview page. In the Featured connectors section, select View more connectors.
2. On the Openflow connectors page, find the connector and select Add to runtime.
3. In the Select runtime dialog, select your runtime from the Available runtimes drop-down list and click Add.

   Note

   Before you install the connector, ensure that you have created a database and schema in Snowflake for the connector to store ingested data.
4. Authenticate to the deployment with your Snowflake account credentials and select Allow when prompted to allow the runtime application to access your Snowflake account. The connector installation process takes a few minutes to complete.
5. Authenticate to the runtime with your Snowflake account credentials.

The Openflow canvas appears with the connector process group added to it.

##### Configure the connector

1. Right-click on the imported process group and select Parameters.
2. Enter the required parameter values as described in [Box ingestion parameters](#box-ingestion-parameters), [Box destination parameters](#box-destination-parameters) and [Box source parameters](#box-source-parameters).

###### Box source parameters

| Parameter | Description |
| --- | --- |
| Box App Config JSON | An application JSON configuration that was downloaded during the app creation. |
| Box App Config File | An application json file that was downloaded during the app creation. Either “Box App Config File” or “Box App Config JSON” has to be set. Select the Reference asset checkbox to upload the config file. |

See moreShow less

Expand

###### Box destination parameters

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

###### Box ingestion parameters

| Parameter | Description |
| --- | --- |
| Box Folder ID | The ID of the folder to read the files from. Set this to `0` to synchronize all folders the Box app has access to. It can be retrieved from the URL, for example <https://app.box.com/folder/FOLDER_ID>. |
| File Extensions To Ingest | A comma-separated list that specifies file extensions to ingest. The connector tries to convert the files to PDF format first, if possible. Nonetheless, the extension check is performed on the original file extension. If some of the specified file extensions are not supported by Cortex Parse Document, then the connector ignores those files, logs a warning message in an event log, and continues processing other files. |
| Snowflake File Hash Table Name | Name of the table to store file hashes to determine if the content has changed. This parameter should generally not be changed. |

See moreShow less

Expand

#### Run the flow

1. Right-click on the plane and select Enable all Controller Services.
2. Right-click on the imported process group and select Start. The connector starts the data ingestion.

After starting the connector, it retrieves all files from the specified folder, and then consumes `admin_logs_streaming` events within the last 14 days.
This is done to capture data that may otherwise have been missed during the initialization process.
During that time, `not found` errors may occur, which are caused by files that appear in the events but are no longer present.

### Ingest files and perform processing with Cortex

Use the connector definition to:

* Create AI assistants for public documents within your organization’s Box enterprise
* Enable your AI assistants to adhere to access controls specified in your organization’s Box enterprise

#### Set up the connector

As a data engineer, perform the following tasks to install and configure the connector:

##### Install the connector

To install the connector, do the following as a data engineer:

1. Navigate to the Openflow overview page. In the Featured connectors section, select View more connectors.
2. On the Openflow connectors page, find the connector and select Add to runtime.
3. In the Select runtime dialog, select your runtime from the Available runtimes drop-down list and click Add.

   Note

   Before you install the connector, ensure that you have created a database and schema in Snowflake for the connector to store ingested data.
4. Authenticate to the deployment with your Snowflake account credentials and select Allow when prompted to allow the runtime application to access your Snowflake account. The connector installation process takes a few minutes to complete.
5. Authenticate to the runtime with your Snowflake account credentials.

The Openflow canvas appears with the connector process group added to it.

##### Configure the connector

1. Right-click on the imported process group and select Parameters.
2. Populate the required parameter values as described in [Box Cortex Connect Ingestion Parameters](#box-cortex-connect-ingestion-parameters), [Box Cortex Connect Destination Parameters](#box-cortex-connect-destination-parameters) and [Box Cortex Connect Source Parameters](#box-cortex-connect-source-parameters).

###### Box Cortex Connect Source Parameters

| Parameter | Description |
| --- | --- |
| Box App Config JSON | An application JSON configuration that was downloaded during the app creation. |
| Box App Config File | An application json file that was downloaded during the app creation. Either “Box App Config File” or “Box App Config JSON” has to be set. Select the Reference asset checkbox to upload the config file. |

See moreShow less

Expand

###### Box Cortex Connect Destination Parameters

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

###### Box Cortex connect ingestion parameters

| Parameter | Description |
| --- | --- |
| Box Folder ID | The ID of the folder to read the files from. Set this to `0` to synchronize all folders the Box app has access to. It can be retrieved from the URL, for example <https://app.box.com/folder/FOLDER_ID>. |
| File Extensions To Ingest | A comma-separated list that specifies file extensions to ingest. The connector tries to convert the files to PDF format first, if possible. Nonetheless, the extension check is performed on the original file extension. If some of the specified file extensions are not supported by Cortex Parse Document, then the connector ignores those files, logs a warning message in an event log, and continues processing other files. |
| Snowflake File Hash Table Name | Name of the table to store file hashes to determine if the content has changed. This parameter should generally not be changed. |
| OCR Mode | The OCR mode to use when parsing files with [Parsing documents with AI\_PARSE\_DOCUMENT](../../../../snowflake-cortex/parse-document) function. The value can be `OCR` or `LAYOUT`. |
| Snowflake Cortex Search Service User Role | An identifier of a role that is assigned usage permissions on the Cortex Search service. |
| Snowflake File Hash Table Name | Name of the table to store file hashes to determine if the content has changed. This parameter should generally not be changed. |

See moreShow less

Expand

#### Run the flow

1. Right-click on the plane and select Enable all Controller Services.
2. Right-click on the imported process group and select Start. The connector starts the data ingestion.

After starting the connector, it retrieves all files from the specified folder, and then consumes `admin_logs_streaming` events within the last 14 days.
This is done to capture any data that may have been missed during the initialization process.
During that time, `not found` errors may occur, caused by the files that appear in the events but are no longer present.

#### Query the Cortex Search service

You can use the [Cortex Search](../../../../snowflake-cortex/cortex-search/cortex-search-overview) service to build chat
and search applications to chat with or query your documents in Box.

After you install and configure the connector and it begins
ingesting content from Box, you can query the Cortex Search service.
For more information about using Cortex Search, see [Query a Cortex Search service](../../../../snowflake-cortex/cortex-search/query-cortex-search-service).

**Filter responses**

To restrict responses from the Cortex Search service to documents that a specific user
has access to in Box, you can specify a filter containing the user ID or email address of the user
when you query Cortex Search. For example, `filter.@contains.user_ids` or `filter.@contains.user_emails`.
The name of the Cortex Search service created by the connector is `search_service` in the schema `Cortex`.

Run the following SQL code in a SQL worksheet to query
the Cortex Search service with files ingested from your Box site.

Replace the following:

* `application_instance_name`: Name of your database and connector application instance.
* `user_emailID`: Email ID of the user who you want to filter the responses for.
* `your_question`: The question that you want to get responses for.
* `number_of_results`: Maximum number of results to return in the response. The maximum value is 1,000 and the default value is 10.

CopyExpand

```
SELECT PARSE_JSON(
  SNOWFLAKE.CORTEX.SEARCH_PREVIEW(
    '<application_instance_name>.cortex.search_service',
      '{
        "query": "<your_question>",
         "columns": ["chunk", "web_url"],
         "filter": {"@contains": {"user_emails": "<user_emailID>"} },
         "limit": <number_of_results>
       }'
   )
)['results'] AS results
```

Show lessSee more

Scroll to top

Here is a complete list of values that you can enter for `columns`:

| Column name | Type | Description |
| --- | --- | --- |
| `full_name` | String | A full path to the file from the Box site documents root. Example: `folder_1/folder_2/file_name.pdf`. |
| `web_url` | String | A URL that displays an original Box file in a browser. |
| `last_modified_date_time` | String | Date and time when the item was most recently modified. |
| `chunk` | String | A piece of text from the document that matched the Cortex Search query. |
| `user_ids` | Array | An array of user IDs that have access to the document. |
| `user_emails` | Array | An array of user email IDs that have access to the document. It also includes user email IDs from all the Microsoft 365 groups that are assigned to the document. |

See moreShow less

Expand

**Example: Query an AI assistant for human resources (HR) information**

You can use Cortex Search to query an AI assistant for employees to chat with the latest versions of
HR information, such as onboarding, code of conduct, team processes, and organization policies.
Using response filters, you can also allow HR team members to query employee contracts while adhering to access controls configured in Box.

SQLPythonREST API

Run the following in a [SQL worksheet](../../../../ui-snowsight-worksheets-gs.html#label-snowsight-worksheets-create-file) to query the Cortex Search service with files ingested from Box.
Select the database as your application instance name and schema as **Cortex**.

Replace the following:

* `application_instance_name`: Name of your database and connector application instance.
* `user_emailID`: Email ID of the user who you want to filter the responses for.

CopyExpand

```
SELECT PARSE_JSON(
     SNOWFLAKE.CORTEX.SEARCH_PREVIEW(
          '<application_instance_name>.cortex.search_service',
          '{
             "query": "What is my vacation carryover policy?",
             "columns": ["chunk", "web_url"],
             "filter": {"@contains": {"user_emails": "<user_emailID>"} },
             "limit": 1
          }'
     )
 )['results'] AS results
```

Show lessSee more

Scroll to top

Run the following code in a [Python worksheet](../../../../ui-snowsight-worksheets-gs.html#label-snowsight-worksheets-create) to query the
Cortex Search service with files ingested from Box.
Ensure that you add the `snowflake.core` package to your database.

Replace the following:

* `application_instance_name`: Name of your database and connector application instance.
* `user_emailID`: Email ID of the user who you want to filter the responses for.

CopyExpand

```
import snowflake.snowpark as snowpark
from snowflake.snowpark import Session
from snowflake.core import Root

def main(session: snowpark.Session):

   root = Root(session)

   # fetch service
   my_service = (root
     .databases["<application_instance_name>"]
     .schemas["cortex"]
     .cortex_search_services["search_service"]
   )

   # query service
   resp = my_service.search(
     query="What is my vacation carryover policy?",
     columns = ["chunk", "web_url"],
     filter = {"@contains": {"user_emails": "<user_emailID>"} },
     limit=1
   )
   return (resp.to_json())
```

Show lessSee more

Scroll to top

Execute the following code in a command-line interface to query the Cortex Search
service with files ingested from your Box.
Access to the Snowflake REST APIs requires authentication via both key pair authentication and OAuth.
For more information,
see [REST API](../../../../snowflake-cortex/cortex-search/query-cortex-search-service.html#label-cortex-search-query-syntax-rest)
and [Authenticating Snowflake REST APIs with Snowflake](../../../../../developer-guide/snowflake-rest-api/authentication).

Replace the following:

* `application_instance_name`: Name of your database and connector application instance.
* `account_url`: Your Snowflake account URL. For instructions on finding your account URL, see [Finding the organization and account name for an account](../../../../admin-account-identifier.html#label-account-name-find).

CopyExpand

```
curl --location "https://<account_url>/api/v2/databases/<application_instance_name>/schemas/cortex/cortex-search-services/search_service" \
     --header 'Content-Type: application/json' \
     --header 'Accept: application/json' \
     --header "Authorization: Bearer <CORTEX_SEARCH_JWT>" \
     --data '{
         "query": "What is my vacation carryover policy?",
         "columns": ["chunk", "web_url"],
         "limit": 1
     }'
```

Show lessSee more

Scroll to top

Sample response:

Expand

```
{
  "results" : [ {
  "web_url" : "https://<domain>.box.com/sites/<site_name>/<path_to_file>",
  "chunk" : "Answer to the question asked."
  } ]
}
```

Show lessSee more

Scroll to top

### Extract Box metadata using Box AI and ingest it into a Snowflake table

Use the connector definition to:

* Extract metadata about your Box files and ingest them to into a Snowflake table
* Perform operations on the metadata of your files stored in Box

#### Create a Snowflake table for storing the Box metadata

1. Ensure that Box AI is enabled for the extraction of metadata to occur. For more information, see [Configuring Box AI](https://support.box.com/hc/en-us/articles/22166647877011-Configuring-Box-AI).
2. Create a Snowflake table where the metadata will be sent

   For the connector to know what kind of metadata to extract, you must create a Snowflake table in your database and schema with the column names of the fields you would like to extract.
   Add descriptions to each column to improve the performance of the model used to extract the metadata from the files.
3. In the table created in the previous step, ensure that there is a column to store the Box file ID and that it is of type VARCHAR.

   The name of this column is required to be entered as the Box File Identifier Column parameter in later steps.
   The list of supported columns types for the metadata table is VARCHAR, STRING, TEXT, FLOAT, DOUBLE, and DATE.

Here is an example of the table that you can create for this connector:

CopyExpand

```
CREATE OR REPLACE TABLE OPENFLOW.BOX_METADATA_SCHEMA.LOAN_AGREEMENT_METADATA (
  BOX_FILE_ID               VARCHAR    COMMENT 'Box file identifier column',
  LOAN_ID                   STRING     COMMENT 'Unique loan agreement identifier (e.g. L-2025-0001)',
  BORROWER_NAME             STRING     COMMENT 'Name of the borrower entity or individual',
  LENDER_NAME               STRING     COMMENT 'Name of the lending institution',
  LOAN_AMOUNT               DOUBLE     COMMENT 'Principal amount of the loan (in USD)',
  INTEREST_RATE             FLOAT      COMMENT 'Annual interest rate (%)',
  EFFECTIVE_DATE            DATE       COMMENT 'Date on which the loan becomes effective',
  MATURITY_DATE             DATE       COMMENT 'Scheduled loan maturity date',
  LOAN_TERM_MONTHS          FLOAT      COMMENT 'Original term length in months',
  COLLATERAL_DESCRIPTION    TEXT       COMMENT 'Description of collateral securing the loan',
  CREDIT_SCORE              FLOAT      COMMENT 'Borrower credit score',
  JURISDICTION              STRING     COMMENT 'Governing law jurisdiction (e.g. NY, CA)'
);
```

Show lessSee more

Scroll to top

#### Set up the connector

As a data engineer, perform the following tasks to install and configure the connector:

##### Install the connector

To install the connector, do the following as a data engineer:

1. Navigate to the Openflow overview page. In the Featured connectors section, select View more connectors.
2. On the Openflow connectors page, find the connector and select Add to runtime.
3. In the Select runtime dialog, select your runtime from the Available runtimes drop-down list and click Add.

   Note

   Before you install the connector, ensure that you have created a database and schema in Snowflake for the connector to store ingested data.
4. Authenticate to the deployment with your Snowflake account credentials and select Allow when prompted to allow the runtime application to access your Snowflake account. The connector installation process takes a few minutes to complete.
5. Authenticate to the runtime with your Snowflake account credentials.

The Openflow canvas appears with the connector process group added to it.

##### Configure the connector

1. Right-click on the imported process group and select Parameters.
2. Populate the required parameter values as described in [Box Ingest Metadata Source Parameters](#box-ingest-metadata-source-parameters), [Box Ingest Metadata Destination Parameters](#box-ingest-metadata-destination-parameters) and [Box Ingest Metadata Ingestion Parameters](#box-ingest-metadata-ingestion-parameters).

###### Box Ingest Metadata Source Parameters

| Parameter | Description |
| --- | --- |
| Box App Config JSON | An application JSON configuration that was downloaded during the app creation. |
| Box App Config File | An application json file that was downloaded during the app creation. Either “Box App Config File” or “Box App Config JSON” has to be set. Select the Reference asset checkbox to upload the config file. |

See moreShow less

Expand

###### Box Ingest Metadata Destination Parameters

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

###### Box Ingest Metadata Ingestion Parameters

| Parameter | Description |
| --- | --- |
| Box Folder ID | The ID of the folder to read the files from. Set this to `0` to synchronize all folders the Box app has access to. The ID can be retrieved from the URL, for example <https://app.box.com/folder/FOLDER_ID>. |
| Box File Identifier Column | The column of the metadata table that will store the Box file ID to associate the given metadata with a file. This column must be of type VARCHAR and be part of the table created in [Create a Snowflake table for storing the Box metadata](#create-a-snowflake-table-for-storing-the-box-metadata). |
| Destination Metadata Table | The Snowflake table you created in [Create a Snowflake table for storing the Box metadata](#create-a-snowflake-table-for-storing-the-box-metadata), which has the columns of the metadata you want to collect. |

See moreShow less

Expand

#### Run the flow

1. Right-click on the plane and select Enable all Controller Services.
2. Right-click on the imported process group and select Start. The connector starts the data ingestion.

After starting the connector, it retrieves all files from the specified folder, and then consumes `admin_logs_streaming` events from the last 14 days.
This is done to capture any data that may have been missed during the initialization process.
During that time, `not found` errors may occur, caused by the files that appear in the events but are no longer present.

### Synchronize Box file metadata instances with a Snowflake table

Use the connector definition to perform a data transformation on metadata
from Box in a Snowflake table and add the changes back to a Box metadata instance.

#### Create a Snowflake stream for storing the Box metadata

1. Create a Snowflake stream for the metadata table you want to use. The stream is used to monitor any changes that occur to the table with which you want to synchronize your Box files.
   To learn how to create a table for storing Box metadata, see [Create a Snowflake table for storing the Box metadata](#create-a-snowflake-table-for-storing-the-box-metadata).
   If the connector is stopped beyond the data retention time and the stream becomes stale, then you must recreate a stream and replace the previous one. To learn more about managing streams, see [Manage streams](../../../../streams-manage).

   Here is an example of a stream that you can create for this connector:

   CopyExpand

   ```
   CREATE OR REPLACE STREAM OPENFLOW.BOX_METADATA_SCHEMA.LOAN_AGREEMENT_METADATA_STREAM
   ON TABLE OPENFLOW.BOX_METADATA_SCHEMA.LOAN_AGREEMENT_METADATA
   ```

   Show lessSee more

   Scroll to top
2. In the metadata table, ensure that there is a column to store the Box file ID and that it is of type VARCHAR.

   The name of this column is required to be entered as the Box File Identifier Column parameter in later steps.
   The list of supported columns types for the metadata table is VARCHAR, STRING, TEXT, FLOAT, DOUBLE, and DATE.

#### Set up the connector

As a data engineer, perform the following tasks to install and configure the connector:

##### Install the connector

To install the connector, do the following as a data engineer:

1. Navigate to the Openflow overview page. In the Featured connectors section, select View more connectors.
2. On the Openflow connectors page, find the connector and select Add to runtime.
3. In the Select runtime dialog, select your runtime from the Available runtimes drop-down list and click Add.

   Note

   Before you install the connector, ensure that you have created a database and schema in Snowflake for the connector to store ingested data.
4. Authenticate to the deployment with your Snowflake account credentials and select Allow when prompted to allow the runtime application to access your Snowflake account. The connector installation process takes a few minutes to complete.
5. Authenticate to the runtime with your Snowflake account credentials.

The Openflow canvas appears with the connector process group added to it.

##### Configure the connector

1. Right-click on the imported process group and select Parameters.
2. Populate the required parameter values as described in [Box Publish Metadata Source Parameters](#box-publish-metadata-source-parameters), [Box Publish Metadata Destination Parameters](#box-publish-metadata-destination-parameters) and [Box Publish Metadata Ingestion Parameters](#box-publish-metadata-ingestion-parameters).

###### Box Publish Metadata Source Parameters

| Parameter | Description |
| --- | --- |
| Source Database | Snowflake Database that contains the schema that contains the Snowflake Stream that ingests the changes |
| Source Schema | Schema that contains the Snowflake Stream that ingests the changes |
| Snowflake Account Identifier | Leave this blank when using Session Token for your Authentication Strategy. When using KEY\_PAIR, provide your Snowflake account name formatted as [organization-name]-[account-name] where data will be persisted. |
| Snowflake Authentication Strategy | When using:   * **Snowflake Openflow Deployment** or **BYOC**: Use SNOWFLAKE\_MANAGED\_TOKEN.   This token is managed automatically by Snowflake.   BYOC deployments must have previously configured   [runtime roles](../../setup-openflow-byoc.html#label-deployment-byoc-setup-runtime-role) to use SNOWFLAKE\_MANAGED\_TOKEN. * **BYOC:** Alternatively BYOC can use KEY\_PAIR as the value for authentication strategy. |
| Snowflake Private Key | Leave this blank when using Session Token for your Authentication Strategy. When using KEY\_PAIR, provide the RSA private key used for authentication. The RSA key must be formatted according to PKCS8 standards and have standard PEM headers and footers. Note that either Snowflake Private Key File or Snowflake Private Key must be defined. |
| Snowflake Private Key File | Leave this blank when using Session Token for your Authentication Strategy. When using KEY\_PAIR, upload the file that contains the RSA Private Key used for authentication to Snowflake, formatted according to PKCS8 standards and having standard PEM headers and footers. The header line begins with `-----BEGIN PRIVATE`. Select the Reference asset checkbox to upload the private key file. |
| Snowflake Private Key Password | Leave this blank when using Session Token for your Authentication Strategy. When using KEY\_PAIR, provide the password associated with the Snowflake Private Key File. |
| Snowflake Role | When using Session Token for your Authentication Strategy, use your Snowflake Role. You can find your Snowflake Role in the Openflow UI, by going to View Details for your Runtime. When using Key Pair for your Authentication Strategy, use a valid role configured for your service user. |
| Snowflake Username | Leave this blank when using Session Token for your Authentication Strategy. When using KEY\_PAIR, provide the user name used to connect to Snowflake instance. |
| Snowflake Warehouse | Snowflake warehouse used to run queries |
| Snowflake Stream Name | Snowflake stream name used for ingestion of changes from the source Snowflake table. You must create it before starting the connector and link to the table. |

See moreShow less

Expand

###### Box Publish Metadata Destination Parameters

| Parameter | Description |
| --- | --- |
| Box App Config JSON | An application JSON configuration that was downloaded during the app creation. |
| Box App Config File | An application json file that was downloaded during the app creation. Either “Box App Config File” or “Box App Config JSON” has to be set. Select the Reference asset checkbox to upload the config file. |

See moreShow less

Expand

###### Box Publish Metadata Ingestion Parameters

| Parameter | Description |
| --- | --- |
| Box File Identifier Column | The column of the metadata table that will store the Box file ID to associate the given metadata with a file. This column must be of type VARCHAR and be part of the table created in [Create a Snowflake table for storing the Box metadata](#create-a-snowflake-table-for-storing-the-box-metadata). |
| Box Metadata Template Name | Template name of the Box metadata template that will be added to the Box files. You don’t need to manually create a template before starting the connector. If you enter a value in this parameter, a template is automatically created with this template name. The name provided should not overlap with any template that you have already created in your Box environment. |
| Box Metadata Template Key | The Box template key of the Box metadata template that will be added to the Box files. This is the key that will be used to reference the template in the Box API. You don’t need to manually create a template before starting the connector. If you enter a value in this parameter, a template is automatically created with this template key. The key provided should not overlap with any template that you have already created in your Box environment. |

See moreShow less

Expand

#### Run the flow

1. Right-click on the plane and select Enable all Controller Services.
2. Right-click on the imported process group and select Start. The connector starts the data ingestion.

After running the flow, you can query the Cortex Search service. For information on how to query the Cortex Search service, see [Query the Cortex Search service](#query-the-cortex-search-service).

### Finding files in stage

Files stored in the stage may have unreadable names. To find specific files, use the metadata
tables as your source of truth. These tables contain the mapping between file names and their
corresponding file IDs in the stage.

For Cortex-enabled setups, use the following query to find files:

CopyExpand

```
SELECT DISTINCT METADATA:id FROM DOCS_CHUNKS WHERE METADATA:fullName LIKE '%<file_name>';
```

Show lessSee more

Scroll to top

For non-Cortex setups, use the following query:

CopyExpand

```
SELECT FILE_ID FROM DOC_METADATA WHERE FILE_NAME = '<file_name>';
```

Show lessSee more

Scroll to top

Replace `<file_name>` with the name or partial name of the file you’re looking for.

The files in the stage start with the ID returned from these queries.
