---
title: "Set up the Openflow Connector for Slack"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/slack/setup"
---

# Set up the Openflow Connector for Slack

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes the steps to set up the Openflow Connector for Slack.

## Prerequisites

1. Ensure that you have reviewed [About Openflow Connector for Slack](about).
2. Ensure that you have [Set up Openflow - BYOC](../../setup-openflow-byoc) or [Set up Openflow - Snowflake Deployments](../../setup-openflow-spcs).
3. If using Openflow - Snowflake Deployments, ensure that you’ve reviewed [configuring required domains](../../setup-openflow-spcs-sf-allow-list)
   and have granted access to the required domains for the [Slack](../../setup-openflow-spcs-sf-allow-list.html#label-openflow-domains-used-by-openflow-connectors-slack) connector.

## Set up a Slack App

Set up a Slack App in your Slack workspace. A Slack Admin is needed to
set up access to the Slack Workspace. This is done by creating or
supplying credentials to a Slack App and installing the App to the
Slack workspace and channels. You can create a Slack App by using the
JSON configuration:

1. Update the JSON manifest. Copy the JSON manifest text below. Change
   the name and display name properties from
   `EXAMPLE_NAME_CHANGE_THIS` to the desired name of your Slack App.
   It is recommended to use the same name and display name for your App.

   > CopyExpand
   >
   > ```
   > {
   >     "display_information": {
   >         "name": "EXAMPLE_NAME_CHANGE_THIS"
   >     },
   >     "features": {
   >         "bot_user": {
   >             "display_name": "EXAMPLE_NAME_CHANGE_THIS",
   >             "always_online": false
   >         }
   >     },
   >     "oauth_config": {
   >         "scopes": {
   >             "bot": [
   >                 "channels:history",
   >                 "channels:read",
   >                 "groups:history",
   >                 "groups:read",
   >                 "im:history",
   >                 "im:read",
   >                 "mpim:history",
   >                 "mpim:read",
   >                 "users.profile:read",
   >                 "users:read",
   >                 "users:read.email",
   >                 "files:read",
   >                 "app_mentions:read",
   >                 "reactions:read"
   >             ]
   >         }
   >     },
   >     "settings": {
   >         "event_subscriptions": {
   >             "bot_events": [
   >                 "message.channels",
   >                 "message.groups",
   >                 "message.im",
   >                 "message.mpim",
   >                 "reaction_added",
   >                 "reaction_removed",
   >                 "file_created",
   >                 "file_deleted",
   >                 "file_change"
   >             ]
   >         },
   >         "interactivity": {
   >             "is_enabled": true
   >         },
   >         "org_deploy_enabled": false,
   >         "socket_mode_enabled": true,
   >         "token_rotation_enabled": false
   >     }
   > }
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
2. Create a Slack app through the [Apps page](https://api.slack.com/apps).

   > 1. On the **Your Apps** page, select **Create New App**.
   > 2. Select **From a manifest**.
   > 3. Select the **Workspace** where you’ll be developing your app. You’ll be able to [distribute your app](<https://api.slack.com/distribution>) to other workspaces later if you choose.
   > 4. Copy the updated manifest JSON from step 1.
3. Generate an app-level token. You need to create an app-level token even after using the JSON manifest. Under **Basic Information**, scroll to the **App-level tokens** section and click the button to generate an [app-level token](<https://api.slack.com/concepts/token-types#app>). Include the `connections:write` scope to the token.
4. Install and authorize the app.

   > 1. Return to the **Basic Information** section of the app management page.
   > 2. Install your app by selecting the **Install to Workspace** button.
   > 3. You’ll now be sent through the Slack OAuth flow. Select **Allow** on the following screen.
   >
   > If you want to add your app to a different workspace besides your own, these steps would need to be performed by a user from that workspace.
   > After installation, navigate back to the **OAuth & Permissions** page. You’ll see an **access token** under **OAuth Tokens**.
   > Access tokens represent the permissions delegated to your app by the installing user. Keep it safe and secure. Avoid checking them into public version control. Instead, access them through an environment variable.
5. Adding the App to channels. Your app isn’t a member of any channels yet, so pick a channel to add some test messages in and `/invite` your app. For example, `/invite @Grocery Reminders`.

Note

Restart the processors to load the new channels. After the App is added to a new channel, the `Consume Slack Conversation` processor in the OpenFlow Runtime needs to be stopped and restarted.

## Setup necessary ingress rules

A Snowflake Admin should follow the [egress guide](../../../../../developer-guide/snowpark-container-services/service-network-communications.html#label-working-with-services-jobs-egress)
to apply egress rules to the endpoint `https://slack.com/api` and
enable WebSocket egress on `wss://wss.slack.com`. This is easiest
done by adding a rule to enable egress on the “slack.com” domain.

## Set up Snowflake account

As a Snowflake account administrator, perform the following tasks:

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

## Use case 1: Ingest Slack content only

Use the connector definition to:

> * Perform custom analysis on ingested Slack data (no Cortex Search processing).
> * Ingest Slack messages, reactions, file attachments, and member lists into Snowflake, and keep them up to date.

### Set up the connector

As a data engineer, perform the following tasks to configure the connector:

#### Install the connector

1. Create a database and schema in Snowflake for the connector to store ingested data. Grant required [Database privileges](../../../../security-access-control-privileges.html#label-database-privileges) to the role created in the first step. Substitute the role placeholder with the actual value and use the following SQL commands:

   > CopyExpand
   >
   > ```
   > CREATE DATABASE DESTINATION_DB;
   > CREATE SCHEMA DESTINATION_DB.DESTINATION_SCHEMA;
   > GRANT USAGE ON DATABASE DESTINATION_DB TO ROLE <CONNECTOR_ROLE>;
   > GRANT USAGE ON SCHEMA DESTINATION_DB.DESTINATION_SCHEMA TO ROLE <CONNECTOR_ROLE>;
   > GRANT CREATE TABLE ON SCHEMA DESTINATION_DB.DESTINATION_SCHEMA TO ROLE <CONNECTOR_ROLE>;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

To install the connector, do the following as a data engineer:

1. Navigate to the Openflow overview page. In the Featured connectors section, select View more connectors.
2. On the Openflow connectors page, find the connector and select Add to runtime.
3. In the Select runtime dialog, select your runtime from the Available runtimes drop-down list and click Add.

   Note

   Before you install the connector, ensure that you have created a database and schema in Snowflake for the connector to store ingested data.
4. Authenticate to the deployment with your Snowflake account credentials and select Allow when prompted to allow the runtime application to access your Snowflake account. The connector installation process takes a few minutes to complete.
5. Authenticate to the runtime with your Snowflake account credentials.

The Openflow canvas appears with the connector process group added to it.

#### Configure the connector

1. Right-click on the imported process group and select **Parameters**.
2. Enter the required parameter values as described in **Flow parameters: Ingest content only** below.
3. Right-click on the canvas and select **Enable all controller services**.
4. Right-click on the imported process group and select **Start**. The flow creates all required Snowflake objects and begins ingesting Slack data.

##### Flow parameters: Ingest content only

| Parameter | Description |
| --- | --- |
| App Token | Slack *App-level token* generated in the Slack App. |
| Bot Token | Slack *Bot token* generated in the Slack App. |
| Destination Database | Database to contain all connector objects (created if absent). |
| Destination Schema | Schema inside the database (created if absent). |
| Snowflake Account | Snowflake account identifier. |
| Snowflake Role | Role the flow assumes after authentication. |
| Snowflake User | Username the flow uses to connect. |
| Snowflake Private Key | RSA private key used for authentication (PKCS8 PEM format). Note that either Snowflake Private Key or Snowflake Private Key File must be defined. |
| Snowflake Private Key Password | Password for the encrypted private key (leave blank if unencrypted). |
| Snowflake Private Key File | File containing the RSA Private Key (PKCS8 PEM format). The header line starts with `-----BEGIN PRIVATE`. |
| Snowflake Warehouse | Warehouse used for SQL executed by the flow. |
| Upload Interval | Time to gather data before pushing to Snowflake. A longer interval reduces load on Snowflake but may increase latency and memory usage. |
| Refresh Slack Members | Minutes between Slack membership (ACL) refreshes. |

See moreShow less

Expand

## Use case 2: Ingest Slack content and enable Cortex

Use the connector definition to:

> * Make Slack data ready for conversational search with Snowflake Cortex.
> * Ensure Slack channel access controls are respected in search results.

### Set up the connector

As a data engineer, perform the following tasks to configure the connector:

#### Install the connector

1. Create a database and schema in Snowflake for the connector to store ingested data. Grant required [Database privileges](../../../../security-access-control-privileges.html#label-database-privileges) to the role created in the first step. Substitute the role placeholder with the actual value and use the following SQL commands:

   > CopyExpand
   >
   > ```
   > CREATE DATABASE DESTINATION_DB;
   > CREATE SCHEMA DESTINATION_DB.DESTINATION_SCHEMA;
   > GRANT USAGE ON DATABASE DESTINATION_DB TO ROLE <CONNECTOR_ROLE>;
   > GRANT USAGE ON SCHEMA DESTINATION_DB.DESTINATION_SCHEMA TO ROLE <CONNECTOR_ROLE>;
   > GRANT CREATE TABLE ON SCHEMA DESTINATION_DB.DESTINATION_SCHEMA TO ROLE <CONNECTOR_ROLE>;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

To install the connector, do the following as a data engineer:

1. Navigate to the Openflow overview page. In the Featured connectors section, select View more connectors.
2. On the Openflow connectors page, find the connector and select Add to runtime.
3. In the Select runtime dialog, select your runtime from the Available runtimes drop-down list and click Add.

   Note

   Before you install the connector, ensure that you have created a database and schema in Snowflake for the connector to store ingested data.
4. Authenticate to the deployment with your Snowflake account credentials and select Allow when prompted to allow the runtime application to access your Snowflake account. The connector installation process takes a few minutes to complete.
5. Authenticate to the runtime with your Snowflake account credentials.

The Openflow canvas appears with the connector process group added to it.

#### Configure the connector

1. Right-click on the imported process group and select **Parameters**.
2. Enter the required parameter values as described in **Flow parameters: Ingest content and enable Cortex** below.
3. Right-click on the canvas and select **Enable all controller services**.
4. Right-click on the imported process group and select **Start**.
5. Once the flow is running, proceed to [Query the Cortex Search service](#query-the-cortex-search-service) for testing.

##### Flow parameters: Ingest content and enable Cortex

| Parameter | Description |
| --- | --- |
| App Token | Slack *App-level token* generated in the Slack App. |
| Bot Token | Slack *Bot token* generated in the Slack App. |
| Destination Database | Database to contain all connector objects (created if absent). |
| Destination Schema | Schema inside the database (created if absent). |
| Upload Interval | Time to gather data before pushing to Snowflake. A larger value reduces load but increases data latency. |
| Snowflake Account | Snowflake account identifier. |
| Snowflake Role | Role the flow assumes after authentication. |
| Snowflake User | Username the flow uses to connect. |
| Snowflake Private Key | PEM-formatted private key for key-pair authentication. |
| Snowflake Private Key Password | Password for the encrypted private key (blank if unencrypted). |
| Snowflake Warehouse | Warehouse used for all SQL executed by the flow **and** by Cortex. |
| Refresh Slack Members | Minutes between Slack membership (ACL) refreshes. |

See moreShow less

Expand

## Enabling private-channel ACLs

No extra steps are required beyond **inviting the Slack App** to each private channel. The connector automatically refreshes the member list and stores it in the membership table at each **Refresh Slack Members** interval.

## Query the Cortex Search service

After Use case 2 is running and the Cortex Search service has been created, you can query it as follows:

CopyExpand

```
SELECT PARSE_JSON(
  SNOWFLAKE.CORTEX.SEARCH_PREVIEW(
    '<openflow_db>.<openflow_schema>.<<SLACK_CORTEX_SEARCH>',
    '{
      "query": "What is my vacation carry over policy?",
      "columns": ["text","channel","ts","username"],
      "filter": {"@contains": {"memberemails": "alice@example.com"}},
      "limit": 10
    }'
  )
)['results'] AS results;
```

Show lessSee more

Scroll to top

**Common searchable columns**

`text`, `type`, `subtype`, `channel`, `user`, `username`, `connectorId`, `workspaceId`, `ts`, `threadTs`

**Example: Query an AI assistant for human resources (HR) information**

You can use Cortex Search to query an AI assistant for employees to chat about the latest Slack posts. The messages
that are searched can come from informative Slack channels such as general or it-help.

SQLPythonREST API

Run the following in a [SQL worksheet](../../../../ui-snowsight-worksheets-gs.html#label-snowsight-worksheets-create-file) to query the Cortex Search service over messages ingested from Slack.

Replace the following:

* `cortex_db`: Name of the database containing the cortex search service, specified by the `Destination Database` parameter.
* `cortex_schema`: Name of the schema containing the cortex search service, specified by the `Destination Schema` parameter.
* `cortex_search_service_name`: Name of the cortex search service, specified by the `Cortex Search Name` parameter.
* `user_emailID`: Email ID of the user who you want to filter the responses for.

CopyExpand

```
SELECT PARSE_JSON(
     SNOWFLAKE.CORTEX.SEARCH_PREVIEW(
          '<cortex_db>.<cortex_schema>.<cortex_search_service_name>',
          '{
             "query": "What is my vacation carry over policy?",
             "columns": ["text", "channel", “ts”,”username”],
             "filter": {"@contains": {"memberemails": "<user_emailID>"} },
             "limit": 1
          }'
     )
 )['results'] AS results
```

Show lessSee more

Scroll to top

Run the following code in a [Python worksheet](../../../../ui-snowsight-worksheets-gs.html#label-snowsight-worksheets-create) to query the
Cortex Search service over messages ingested from Slack
Ensure that you add the `snowflake.core` package to your database.

Replace the following:

* `cortex_db`: Name of the database containing the cortex search service, specified by the `Destination Database` parameter.
* `cortex_schema`: Name of the schema containing the cortex search service, specified by the `Destination Schema` parameter.
* `cortex_search_service_name`: Name of the cortex search service, specified by the `Cortex Search Name` parameter.
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
     .databases["<cortex_db>"]
     .schemas["<cortex_schema>"]
     .cortex_search_services["<cortex_search_service_name>"]
   )

   # query service
   resp = my_service.search(
     query="What is my vacation carry over policy?",
     columns = ["text", "channel", "ts","username"],
     filter = {"@contains": {"memberemails": "<user_emailID>"} },
     limit=1
     )
   return (resp.to_json())
```

Show lessSee more

Scroll to top

Execute the following code in a command-line interface to query the Cortex Search
service over messages ingested from Slack.
You will need to authentication through key pair authentication and OAuth to access the
Snowflake REST APIs. For more information,
see [REST API](../../../../snowflake-cortex/cortex-search/query-cortex-search-service.html#label-cortex-search-query-syntax-rest)
and [Authenticating Snowflake REST APIs with Snowflake](../../../../../developer-guide/snowflake-rest-api/authentication).

Replace the following:

* `cortex_db`: Name of the database containing the cortex search service, specified by the `Destination Database` parameter.
* `cortex_schema`: Name of the schema containing the cortex search service, specified by the `Destination Schema` parameter.
* `cortex_search_service_name`: Name of the cortex search service, specified by the `Cortex Search Name` parameter.
* `account_url`: Your Snowflake account URL. For instructions on finding your account URL, see [Finding the organization and account name for an account](../../../../admin-account-identifier.html#label-account-name-find).

CopyExpand

```
curl --location "https://<account_url>/api/v2/databases/<cortex_db>/schemas/<cortex_schema>/cortex-search-services/<cortex_search_service_name>" \
     --header 'Content-Type: application/json' \
     --header 'Accept: application/json' \
     --header "Authorization: Bearer <CORTEX_SEARCH_JWT>" \
     --data '{
         "query": "What is my vacation carry over policy?",
         "columns": ["text", "channel"],
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
  "channel" : "dev notes",
  "text" : "Answer to the question asked."
  } ]
}
```

Show lessSee more

Scroll to top
