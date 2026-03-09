---
title: "User access and settings for agents"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/snowflake-intelligence/deploy-agents"
---

# User access and settings for agents

This topic provides information about the permissions required for users to interact with agents in Snowflake Intelligence and about the settings available for the Snowflake Intelligence interface and advanced access control features.

If you don’t have an agent for use with Snowflake Intelligence, create one using the [Build agents](build-agents) guide.

## Customize the Snowflake Intelligence interface

To customize the Snowflake Intelligence interface that users interact with Cortex Agents through, follow these steps:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » Agents.
3. Select Open settings.
4. Under Snowflake Intelligence, modify the following settings:

   * Display name: The name of the Snowflake Intelligence interface that is displayed to users.
   * Welcome message: The message that is displayed when users first open the Snowflake Intelligence interface.
   * Color theme: The color theme of the Snowflake Intelligence interface.
     :   You can provide a custom primary color in hexadecimal format.
   * Full-length logo and Compact logo: The logos that are displayed when the navigation pane is expanded or collapsed, respectively.
   * Compact logo: The icon that is displayed in the browser tab.
5. Select Save.

## User privileges and access control

Users must have the following privileges to view agents in Snowflake Intelligence:

| Privilege | Object | Notes |
| --- | --- | --- |
| USAGE | Database, schema | Required to view the agent. |
| USAGE | Agent | Required to query the Cortex Agent to generate responses. |

See moreShow less

Expand

To access the tools attached to an agent, users must have the following privileges:

| Privilege | Object | Notes |
| --- | --- | --- |
| USAGE | Database, schema | Required to access the objects associated with any tools to attach to the agent. |
| USAGE | Cortex Search service | Required to run the Cortex Search services in the Cortex Agents request. |
| SELECT | Table | Required to access the objects referenced in the agent’s semantic view/model. |
| USAGE | Tools | Required to access all of the custom tools that the agent can use to generate responses. For example, if the custom tool is a stored procedure, then the user must have USAGE on the procedure. |
| USAGE | Semantic view/model | Required to access the semantic view/model referenced by the agent. |

See moreShow less

Expand

**Limit access to specific roles**

The CORTEX\_USER role gives users access to all Cortex features, including agents. By default, this role is granted to the PUBLIC role, which is automatically granted to all users and roles. If you don’t want all users to have this privilege, you can revoke it from the PUBLIC role and grant access to specific roles only. For more information, see [Cortex LLM privileges](../aisql.html#label-cortex-llm-privileges).

After the CORTEX\_USER role is revoked from the PUBLIC role, you can grant the CORTEX\_AGENT\_USER role. This role gives users access to only the Cortex Agents API, which allows them to use Snowflake Intelligence, but not the other Cortex features.

* To provide selective access to Cortex Agents so that only a subset of users have access to the feature, first revoke access to the PUBLIC role, and then grant the CORTEX\_AGENT\_USER role to specific users:

  CopyExpand

  ```
  GRANT DATABASE ROLE SNOWFLAKE.CORTEX_AGENT_USER TO ROLE <role_name>;
  ```

  Show lessSee more

  Scroll to top

For more information, see [Access control requirements](../cortex-agents.html#label-cortex-agents-access-control).

## Configure Snowflake Intelligence with private connectivity

Snowflake Intelligence supports integration with AWS Privatelink and Azure Private Link to establish a private connection between your virtual private cloud (VPC) or virtual network (VNet) and Snowflake Intelligence. Configuring private connectivity requires setting up the correct DNS resolution to direct traffic to the Snowflake Intelligence service through this private connection.

Note that AWS PrivateLink and Azure Private Link are not services provided by Snowflake. They are an AWS service and Microsoft service, respectively, that Snowflake supports to use with your Snowflake account.

### Prerequisites

Complete the following prerequisites before connecting to Snowflake Intelligence with private connectivity.

* Do one of the following:
  :   + To set up AWS PrivateLink, follow the instructions in [AWS PrivateLink and Snowflake](../../admin-security-privatelink).
      + To set up Azure Private Link, follow the instructions in [Azure Private Link and Snowflake](../../privatelink-azure).
* To ensure that a `regionless-snowsight-privatelink-url` is available, using the ACCOUNTADMIN system role, call the [SYSTEM$GET\_PRIVATELINK\_CONFIG](../../../sql-reference/functions/system_get_privatelink_config) function.

Important

Snowflake Intelligence exclusively uses the regionless URL format for private connectivity access. Unlike with other private connectivity URLs used for Snowflake, you should not include a region identifier, such as `us-west-2,` in the hostname. Any attempts to connect using a region-specific URL will fail.

### Connect to Snowflake Intelligence

Connect to Snowflake Intelligence by configuring the DNS for Snowflake Intelligence to use the subdomain.

* Create a CNAME record in your private DNS zone, `privatelink.snowflakecomputing.com`, that maps the following URL to the DNS name of your VPC or VNET endpoint:

  CopyExpand

  ```
  si-<org-acct>.privatelink.snowflakecomputing.com
  ```

  Show lessSee more

  Scroll to top

After the configuration is complete, users within your network can access Snowflake Intelligence by navigating to the following URL:

> CopyExpand
>
> ```
> https://si-<org-acct>.privatelink.snowflakecomputing.com
> ```
>
> Show lessSee more
>
> Scroll to top

The connection is routed securely over the private connection.

### User authentication with private connectivity

Users accessing Snowflake Intelligence with private connectivity use the standard Snowflake authentication process, which requires them to provide their account identifier, username, and password on the sign-in page.

## Redirect users to your identity provider

An account administrator can configure all user URLs to redirect to your identity provider (IdP) when an unauthenticated user accesses Snowflake Intelligence.
This process eliminates a step from the user’s sign-in flow.

* To redirect unauthenticated users from URLs to your IdP, execute the following SQL command, replacing `your_security_integration` with the name of the security integration that is configured for your IdP:

  CopyExpand

  ```
  ALTER ACCOUNT SET LOGIN_IDP_REDIRECT = (SNOWFLAKE_INTELLIGENCE = <your_security_integration>);
  ```

  Show lessSee more

  Scroll to top

Note

* To use IdP redirecting when Snowflake Intelligence is accessed with private connectivity, you must configure the DNS to direct traffic to the Snowflake Intelligence service using the following URL format:

  CopyExpand

  ```
  https://si-<org-acct>.privatelink.snowflakecomputing.com
  ```

  Show lessSee more

  Scroll to top

For more information, see [Configure Snowflake Intelligence with private connectivity](#label-snowflake-intelligence-configure-privatelink).

For more information about configuring your Snowflake account to use an IdP, see the following topics:

* [Configuring Snowflake to use federated authentication](../../admin-security-fed-auth-security-integration)
* [Configuring an identity provider (IdP) for Snowflake](../../admin-security-fed-auth-configure-idp)

## Limit a user’s access to only Snowflake Intelligence

To restrict a user to only access Snowflake Intelligence and prevent them from accessing other parts of Snowflake, you can use either the [ALTER USER](../../../sql-reference/sql/alter-user) SQL command or the `allowedInterfaces` SCIM attribute. If a value other than `ALL` is specified using either method, then users can only access the interface specified and cannot interact with any Snowflake data outside of the interface specified.

* To restrict a user to only access Snowflake Intelligence, use the [ALTER USER](../../../sql-reference/sql/alter-user) SQL command:

  > CopyExpand
  >
  > ```
  > ALTER USER <user_name> SET ALLOWED_INTERFACES = (SNOWFLAKE_INTELLIGENCE);
  > ```
  >
  > Show lessSee more
  >
  > Scroll to top
* If you’re provisioning users with SCIM APIs, to set the same restriction, use the custom attribute `allowedInterfaces`.

For more information about SCIM custom attributes, see [Custom attributes](../../scim-user-api-reference.html#label-scim-custom-attributes).

### Limitations

Snowflake Intelligence currently has these limitations for Snowflake Intelligence-only users:

* Custom branding logos and icons don’t work for Snowflake Intelligence-only users and default to the Snowflake logo and icon.
* Snowflake Intelligence-only users cannot upload files.

## Snowflake Intelligence object

A Snowflake Intelligence object is an account-level object used to manage all agents in Snowflake Intelligence and their settings for your account. The Snowflake Intelligence object offers the following benefits:

* **Flexibility:** Create and manage agents anywhere in your account without needing to centralize them in a single schema.
* **Agent visibility management:** Use a single object to control which agents appear to all users.
* **Improved permission management:** Separate the ability to create agents from the ability to control which agents are shown in Snowflake Intelligence.

Note

Using a Snowflake Intelligence object is an advanced configuration option and is not required to manage agents in Snowflake Intelligence. If an account has a Snowflake Intelligence object, then the agent must be added to that object to be visible. If not added, the agent can only be accessed using a [direct link](#label-snowflake-intelligence-use-agent) or the Snowsight UI.

### Set up a Snowflake Intelligence object

Note

The role must have the CREATE SNOWFLAKE INTELLIGENCE ON ACCOUNT privilege to create a Snowflake Intelligence object.

To set up a Snowflake Intelligence object for your users, follow this process, which is expanded in the following sections:

* Create a Snowflake Intelligence object. The Snowflake Intelligence object is a single object meant to manage all agents used with Snowflake Intelligence in your account. You can only have one Snowflake Intelligence object in your account.
* Add agents to the Snowflake Intelligence object.
* GRANT the USAGE privilege on the Snowflake Intelligence object.

### Create a Snowflake Intelligence object

You can use either Snowsight or SQL to create a Snowflake Intelligence object.

> Snowsight UISQL
>
> Snowflake automatically creates the Snowflake Intelligence object when you modify the Snowflake Intelligence settings for the first time. When created using the UI, the Snowflake Intelligence object is named `SNOWFLAKE_INTELLIGENCE_OBJECT_DEFAULT`. You can’t specify a different name.
>
> 1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
> 2. In the navigation menu, select AI & ML » Agents.
> 3. On the Snowflake Intelligence tab, select Open settings.
>    The Snowflake Intelligence object is created automatically if it doesn’t already exist. You can then add agents to the object.
>
> -To create a Snowflake Intelligence object, use the following command:
>
> > CopyExpand
> >
> > ```
> > CREATE SNOWFLAKE INTELLIGENCE SNOWFLAKE_INTELLIGENCE_OBJECT_DEFAULT;
> > ```
> >
> > Show lessSee more
> >
> > Scroll to top

### Adding agents

The Snowflake Intelligence object is an account-level object that contains a list of agents. You can add or remove agents from this object to create a curated list of agents for your users.
For more information about adding or removing agents, see [Configure the visibility of agents in Snowflake Intelligence](#label-snowflake-intelligence-agent-discovery).

### Grant Snowflake Intelligence privileges

The following privileges control access to Snowflake Intelligence objects:

* **CREATE SNOWFLAKE INTELLIGENCE on the account:** Privilege that allows creating a Snowflake Intelligence object. This privilege is granted to ACCOUNTADMIN by default.

  To grant this privilege to another role, run the following command:

  CopyExpand

  ```
  GRANT CREATE SNOWFLAKE INTELLIGENCE ON ACCOUNT TO ROLE <role_name>;
  ```

  Show lessSee more

  Scroll to top
* **USAGE on the Snowflake Intelligence object:** Privilege that allows users to view the list of agents added to the Snowflake Intelligence object and see configuration values.

  To grant this privilege, run the following command:

  CopyExpand

  ```
  GRANT USAGE ON SNOWFLAKE INTELLIGENCE SNOWFLAKE_INTELLIGENCE_OBJECT_DEFAULT TO ROLE <role_name>;
  ```

  Show lessSee more

  Scroll to top
* **MODIFY on the Snowflake Intelligence object:** Privilege that allows users to add or remove agents from the Snowflake Intelligence object and change configuration values. Account administrators have this privilege by default.

  To grant this privilege, run the following command:

  CopyExpand

  ```
  GRANT MODIFY ON SNOWFLAKE INTELLIGENCE SNOWFLAKE_INTELLIGENCE_OBJECT_DEFAULT TO ROLE <role_name>;
  ```

  Show lessSee more

  Scroll to top
* To make the Snowflake Intelligence object visible to all of your users, grant the USAGE privilege on the object to the PUBLIC role:

  CopyExpand

  ```
  GRANT USAGE ON SNOWFLAKE INTELLIGENCE SNOWFLAKE_INTELLIGENCE_OBJECT_DEFAULT TO ROLE PUBLIC;
  ```

  Show lessSee more

  Scroll to top

If you are using the ACCOUNTADMIN role, you also have the MODIFY privilege on the Snowflake Intelligence object. This allows you to add or remove agents from the object to create a curated list of agents for your users.

To set up Snowflake Intelligence for your users, you must configure agent privileges. For information about the privileges required for agents, see [Access control requirements](../cortex-agents.html#label-cortex-agents-access-control).

Important

By default, Snowflake Intelligence uses the default role and the default warehouse of the user.
When you invite others to use Snowflake Intelligence, ensure that they have a default role and warehouse.

Note

All of the queries from Snowflake Intelligence use the user’s credentials. All role-based access control and data-masking policies associated with the user automatically apply to all interactions and conversations with the agent.

## Configure the visibility of agents in Snowflake Intelligence

In some cases, you might want to limit the agents that users can see in Snowflake Intelligence. For example, you might want to only show agents that are relevant to a specific user or group of users.

If you haven’t created a Snowflake Intelligence object and added agents to it, users automatically see all agents they have access to in your account.

* To control which agents appear in the Snowflake Intelligence interface for all users, create a curated list of agents by adding them to the Snowflake Intelligence object.

### Verify the Snowflake Intelligence object

* To see whether the Snowflake Intelligence object has been created in your account, use the following command:

  CopyExpand

  ```
  SHOW SNOWFLAKE INTELLIGENCES;
  ```

  Show lessSee more

  Scroll to top

Note

Only one Snowflake Intelligence object can exist in an account.

### Manage agents with the Snowflake Intelligence object

* To add agents to the Snowflake Intelligence object, use the following command:

  CopyExpand

  ```
  ALTER SNOWFLAKE INTELLIGENCE SNOWFLAKE_INTELLIGENCE_OBJECT_DEFAULT ADD AGENT <db.schema.agent_name>;
  ```

  Show lessSee more

  Scroll to top
* To remove agents from the Snowflake Intelligence object, use the following command:

  CopyExpand

  ```
  ALTER SNOWFLAKE INTELLIGENCE SNOWFLAKE_INTELLIGENCE_OBJECT_DEFAULT DROP AGENT <db.schema.agent_name>;
  ```

  Show lessSee more

  Scroll to top

Note

Any user or admin with the correct database and schema privileges can create agents. However, agents are not automatically added to the Snowflake Intelligence object: to add an agent to the Snowflake Intelligence object, users must have the ALTER privilege on the Snowflake Intelligence object and USAGE privileges on the agent.

Administrators must have the USAGE privilege on the agent to add it to the Snowflake Intelligence object.

### Migrate from managing agent visibility with the SNOWFLAKE\_INTELLIGENCE.AGENTS schema to the Snowflake Intelligence object

Important

The `SNOWFLAKE_INTELLIGENCE.AGENTS` schema is deprecated as a mechanism for managing agent visibility. If you’re currently using this schema, we recommend migrating to the Snowflake Intelligence object.

If you’re using the `SNOWFLAKE_INTELLIGENCE.AGENTS` schema, your agents will continue to work, as detailed in [Configure the visibility of agents in Snowflake Intelligence](#label-snowflake-intelligence-agent-discovery). However, migrating to the Snowflake Intelligence object provides the following benefits:

> * **Flexibility:** Create and manage agents anywhere in your account without needing to centralize them in a single schema.
> * **Improved permission management:** Separate the ability to create agents from the ability to make them visible in Snowflake Intelligence.
> * **Fewer naming conflicts:** Eliminate potential conflicts with the `SNOWFLAKE_INTELLIGENCE.AGENTS` schema name.
> * **Easier agent visibility management:** Use a single object to control which agents appear to all users.

You must create a Snowflake Intelligence object before you migrate your agents. For information about creating a Snowflake Intelligence object, see [Snowflake Intelligence object](#label-snowflake-intelligence-setup).

* To add an agent to the Snowflake Intelligence object, use the following code:

  > CopyExpand
  >
  > ```
  > ALTER SNOWFLAKE INTELLIGENCE SNOWFLAKE_INTELLIGENCE_OBJECT_DEFAULT ADD AGENT SNOWFLAKE_INTELLIGENCE.AGENTS.<agent_name>;
  > ```
  >
  > Show lessSee more
  >
  > Scroll to top

## Access the agent

After you’ve created an agent, users can ask it questions to get insights from your data.
The agent can answer questions such as these:

* What is the average sales amount for the last quarter?
* What product sold the most units last month?
* Can you show me the sales trend for the last year?

It can also provide the following visualizations:

* Bar chart
* Line chart
* Pie chart
* Scatter plot

To use the agent, follow these steps:

> Method 1: Access without private connectivityMethod 2: Access with private connectivityMethod 3: Access with a direct link
>
> To access Snowflake Intelligence without private connectivity, navigate to the following URL:
>
> CopyExpand
>
> ```
> https://ai.snowflake.com
> ```
>
> Show lessSee more
>
> Scroll to top
>
> To access Snowflake Intelligence with private connectivity, navigate to the following URL:
>
> CopyExpand
>
> ```
> https://si-<org-acct>.snowflakecomputing.com
> ```
>
> Show lessSee more
>
> Scroll to top
>
> To access Snowflake Intelligence with a direct link, follow these steps:
>
> 1. In the navigation menu, select AI & ML » Agents.
> 2. From the list of agents, select the agent that you want to access.
> 3. Select Preview in Snowflake Intelligence.
> 4. Copy the URL.

Note

You can switch between agents in the same conversation thread to retain context across agent interactions.

## Monitoring agent usage and feedback

You can view logs for an agent to see details about the interactions that users have had with the agent. The logs include information such as the prompts that users have sent to the agent, the responses that the agent has provided, and any errors that have occurred. For more information about viewing logs for agents, see [Monitor Cortex Agent requests](../cortex-agents-monitor).

When users in your organization interact with agents, they can provide feedback about the responses that the agents provide. This feedback gives high-level insights about the satisfaction of users. To view user feedback for your agents, see [Monitor Cortex Agent requests](../cortex-agents-monitor).
