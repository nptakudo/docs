---
title: "Openflow Connector for Salesforce Bulk API: Configure the connector"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/salesforce-bulk-api/configure-connector"
---

# Openflow Connector for Salesforce Bulk API: Configure the connector

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) [Preview Feature](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/preview-terms-of-service/)

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes the steps to configure the Openflow Connector for Salesforce Bulk API.

## Install the connector

Follow these steps to install the Openflow Connector for Salesforce Bulk API in an Openflow runtime:

1. Navigate to the Openflow Overview page. In the Featured connectors section, select View more connectors.
2. On the Openflow connectors page, find Openflow connector for Salesforce Bulk API and select Add to runtime.
3. In the Select runtime dialog, select your runtime from the Available runtimes drop-down.

The Openflow canvas appears with the connector process group added to it.

## Configure the connector

To configure the connector, perform the following steps:

1. Right-click on the imported process group and select Parameters.
2. Populate the required parameter values as described in the table below.

| Parameter | Description |
| --- | --- |
| Column Removal Strategy | Defines the strategy to adopt when a column should be removed in the destination table based on the latest received schema. Three possible values: `Drop Column`, `Rename Column`, `Ignore Column`.   * `Drop Column`: Drop the column from the Snowflake table. * `Rename Column`: Rename the column in the Snowflake table. * `Ignore Column`: Ignore the column, leaving it as is in the Snowflake table. |
| Connected App Key | Copy-paste the content of the `private.key` file generated during the Salesforce Setup. You may also use the next parameter if you want to upload the private key to the Openflow runtime instead. |
| Connected App Key File | You can directly upload the `private.key` file by clicking the checkbox **Reference asset**, then upload the file as an asset and select the asset as the value for the parameter. |
| Connected App Key Password | Password set on the private key file during the Salesforce Setup steps. |
| Destination Database | Name of the database in Snowflake where the Salesforce data will be replicated. The database must exist before starting the connector. |
| Destination Schema | Name of the schema, in the database above, into which the connector will create tables for the Salesforce data to be added. The schema must exist before starting the connector. |
| Filter | Comma-separated list of objects to replicate from Salesforce, or regular expression to apply against all existing objects. Example: `Account, Opportunity, Contact`.  Note  If left empty, all objects will be replicated. This is not recommended as there are usually thousands of objects in a Salesforce instance. |
| Incremental Offload | Whether the processor should perform incremental offload. If `true`, the processor will only fetch the records that have been modified since the last query job submission by using a `WHERE` clause on the appropriate timestamp field. If `false`, all records will be fetched at every execution of the connector. |
| Initial Load Chunking | If set to a value other than `NONE`, the initial data load will be split into multiple jobs based on this interval. On the first run for an object, the connector will query Salesforce to find the oldest record and use that as the starting point. Each subsequent job will query the next time chunk until caught up to the current time. Should be set with one of: `NONE`, `MONTHLY`, `QUARTERLY`, `YEARLY`.  This is useful for large datasets where loading all historical data in a single query may time out, exceed API limits, or exceed the storage size of the content repository of the runtime. Once caught up, the processor will continue with normal incremental offload behavior. |
| OAuth2 Audience | Audience to set in the JWT token. This is usually set to `https://login.salesforce.com`. |
| OAuth2 Client ID | Should be set to the Consumer Key value retrieved during the Salesforce Setup steps. |
| OAuth2 Subject | Should be set to the username of an admin-approved user for the application to interact with Salesforce APIs on behalf of this user. |
| OAuth2 Token Endpoint URL | Endpoint to negotiate tokens via the JWT Bearer Flow. Example: `https://myCompany.my.salesforce.com/services/oauth2/token`. |
| Object Fields Filter JSON | A JSON specifying which fields and field patterns should be included or excluded, per Salesforce object. Takes the form of an array with one item per object.  Example 1: This will include all fields that end with ‘name’ in the ‘Account’ Salesforce object:  `[ {"objectType":"Account", "includedPattern":".*name"} ]`  Example 2: This will include the fields Id, Name, and Revenue in the ‘Account’ Salesforce object:  `[ {"objectType":"Account", "included": ["Id", "Name", "Revenue"]} ]`  `excluded` and `excludedPattern` are also available for configuring the filters. |
| Object Identifier Resolution | Determines if schema / table / column names are treated as case-sensitive or case-insensitive. One of: `CASE_INSENSITIVE` / `CASE_SENSITIVE`.  Note  Changing this parameter value will require clearing the state and doing a full reload of all objects. |
| Removed Column Name Suffix | Suffix added to the column name when the parameter Column Removal Strategy is set to `Rename Column`. Default: `__deleted`. |
| Run Schedule | Frequency at which the connector will check for updates in Salesforce for configured objects via the Filter parameter. Default: `15 minutes`. |
| Salesforce Instance | Hostname of the Salesforce instance including the domain name. Do not include the protocol prefix (`https://`). For example, use `myCompany.my.salesforce.com`. |
| Snowflake Account Identifier | Snowflake account name formatted as `[organization-name]-[account-name]` where data will be persisted. Example: `PM-CONNECTORS`. |
| Snowflake Username | The name of the service user that the connector uses to connect to Snowflake. The service user is required only when using the `KEY_PAIR` authentication strategy (Openflow BYOC only). |
| Snowflake Private Key | The RSA Private Key that the connector use for authentication to Snowflake, formatted according to PKCS8 standards and including standard PEM headers and footers. The header line starts with `-----BEGIN PRIVATE`. This is required only when using the `KEY_PAIR` authentication strategy (Openflow BYOC only).  You may also use the next parameter to upload the private key to the Openflow runtime instead. |
| Snowflake Private Key File | The file containing the RSA Private Key that the connector uses for authentication to Snowflake, formatted according to PKCS8 standards and including standard PEM headers and footers. The header line starts with `-----BEGIN PRIVATE`. Required only when using the `KEY_PAIR` authentication strategy (Openflow BYOC only).  Select the Reference asset checkbox to upload the private key file and store it securely in the Openflow runtime. |
| Snowflake Private Key Password | The password associated with the Snowflake Private Key File (if encrypted). This is required only when using the `KEY_PAIR` authentication strategy (Openflow BYOC only). |
| Snowflake Role | Name of the Snowflake role used during query execution. When using `SNOWFLAKE_MANAGED`, this is the Snowflake Role for Openflow Runtimes . When using `KEY_PAIR` (Openflow BYOC only), this is the role assigned to the specified Snowflake username. |
| Snowflake Authentication Strategy | Authentication strategy for the connector to connect to Snowflake.  Using `SNOWFLAKE_MANAGED` (default) will make use of the Snowflake managed token associated with the specified Snowflake Runtime Role. If using Openflow BYOC, it is also possible to use `KEY_PAIR` in order to specify a specific user and role via a custom Key Pair. |
| Snowflake Warehouse | The Snowflake warehouse used to run queries. |
| Special Objects Filter | Comma-separated list of objects to offload from Salesforce (using direct API access), or regular expression to apply against all existing objects.  This filter should only be used for objects that are **not** supported by the Salesforce Bulk API such as knowledge data, for example. This parameter should not overlap with the parameter Filter.  Example: `Knowledge.*` |

See moreShow less

Expand

## Run the connector

Follow these steps to start the connector and begin replicating data from Salesforce to Snowflake:

1. Right-click on an empty area in the canvas and select Enable all Controller Services.
2. Right-click on the connector process group and select Start.

## Manage object replication

After the connector has been started and objects have been replicated, you can add new objects or remove existing objects from replication.

### Add new objects to replication

To add a new object to replication, update the Filter parameter (or Special Objects Filter parameter, if applicable) with the new object names. You do not need to stop the connector. The new object is replicated at the next scheduled execution.

For example, if the current Filter value is `Account, Opportunity` and you want to add the `Contact` object, change the value to `Account, Opportunity, Contact`.

### Remove objects from replication

Removing an object from replication requires stopping the connector and cleaning up both the connector state and the destination table in Snowflake:

1. Stop all processors in the flow by right-clicking on the connector process group and selecting Stop.
2. Ensure that no in-flight FlowFiles are being processed.
3. Right-click on the canvas and select Parameters, then remove the object name from the Filter parameter (or the Special Objects Filter parameter, if applicable).
4. Right-click on the canvas and select Disable all controller services.
5. Go to Controller services and open the state of the controller service named Salesforce Bulk Jobs State.
6. Select the trash icon next to the object type you removed to delete its state entry.
7. Right-click on the canvas and select Enable all controller services, then start all processors to resume the connector.
8. If applicable, drop the corresponding table from the Snowflake destination database to clean up the previously replicated data. For example:

   CopyExpand

   ```
   DROP TABLE <database_name>.<schema_name>.<object_name>;
   ```

   Show lessSee more

   Scroll to top

## Next steps

* To monitor and troubleshoot the connector, see [Troubleshooting the Openflow Connector for Salesforce Bulk API](troubleshoot).
