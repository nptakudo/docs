---
title: "Cortex Agents"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-agents"
---

# Cortex Agents

Get started with Cortex Agents

[Try it in Snowsight](https://app.snowflake.com/_deeplink/#/agents?utm_source=docs&utm_medium=growth&utm_campaign=-us-en-all&utm_content=-app-user-guide-snowflake-cortex-cortex-agents)

## Overview

Cortex Agents orchestrate across both structured and unstructured data sources to deliver insights. They plan tasks, use tools to execute these tasks, and generate responses. Agents use Cortex Analyst (structured) and Cortex Search (unstructured) as tools, along with LLMs, to analyze data. Cortex Search extracts insights from unstructured sources, while Cortex Analyst generates SQL to process structured data. In addition, you can use stored procedures and user defined functions (UDFs) to implement custom tools. A comprehensive support for tool identification and tool execution enables delivery of sophisticated applications grounded in enterprise data.

The workflow involves four key components:

1. **Planning**: Applications often switch between processing data from structured and unstructured sources. For example, consider a conversational app designed to answer user queries. A business user may first ask for top distributors by revenue (structured) and then switch to inquiring about a contract (unstructured). Cortex Agents can parse a request to orchestrate a plan and arrive at the solution or response.

   1. **Explore options**: When the user poses an ambiguous question (for example, “Tell me about Acme Supplies”), the agent considers different permutations - products, location, or sales personnel - to disambiguate and improve accuracy.
   2. **Split into subtasks**: Cortex Agents can split a task or request (for example, “What are the differences between contract terms for Acme Supplies and Acme Stationery?”) into multiple parts for a more precise response.
   3. **Route across tools**: The agent selects the right tool - Cortex Analyst or Cortex Search - to ensure governed access and compliance with enterprise policies.
2. **Tool use**: With a plan in place, the agent retrieves data efficiently. Cortex Search extracts insights from unstructured sources, while Cortex Analyst generates SQL to process structured data. A comprehensive support for tool identification and tool execution enables delivery of sophisticated applications grounded in enterprise data.
3. **Reflection**: After each tool use, the agent evaluates results to determine the next steps - asking for clarification, iterating, or generating a final response. This orchestration allows it to handle complex data queries while ensuring accuracy and compliance within Snowflake’s secure perimeter.
4. **Monitor and iterate**: After deployment, customers can track metrics, analyze performance and refine behavior for continuous improvements. On the client application developers can use TruLens to monitor the Agent interaction. By continuously monitoring and refining governance controls, enterprises can confidently scale AI agents while maintaining security and compliance.

For tutorials to help you get started, see [Cortex Agents tutorials](cortex-agents-tutorials).

Note

While Snowflake strives to provide high quality responses, the accuracy of the LLM responses or
the citations provided are not guaranteed. You should review all answers from the Agents API before serving them to your users.

## Access control requirements

To make a request to Cortex Agent via agent:run API, you can use a role that has the
SNOWFLAKE.CORTEX\_USER or SNOWFLAKE.CORTEX\_AGENT\_USER role granted. The CORTEX\_USER provides
access to all Covered AI features including Cortex Agents whereas CORTEX\_AGENT\_USER provides access to
the Agents feature.

Note

You must use the user’s default role when calling or updating Cortex Agents. To allow another role to edit the agent, grant USAGE on the database, schema, and agent to that role.

CopyExpand

```
GRANT USAGE ON DATABASE <database_name> to ROLE <role_name>;
GRANT USAGE ON SCHEMA <database_name>.<schema_name> to ROLE <role_name>;
GRANT USAGE ON AGENT <database_name>.<schema_name>.<agent_name> to ROLE <role_name>;
```

Show lessSee more

Scroll to top

To use Cortex Agents with a semantic model, you also need the following privileges:

| Privilege | Object | Notes |
| --- | --- | --- |
| CREATE AGENT | Schema | Required to create the Cortex Agent. |
| USAGE | Cortex Search service | Required to run the Cortex Search services in the Cortex Agents request. |
| USAGE | Database, schema, table | Required for access the objects referenced in the Cortex Agents semantic model. |
| OWNERSHIP | Agent | OWNERSHIP is a special privilege on an object that is automatically granted to the role that created the object, but can also be transferred using the [GRANT OWNERSHIP](../../sql-reference/sql/grant-ownership) command to a different role by the owning role (or any role with the MANAGE GRANTS privilege). In a managed access schema, only the schema owner (for example. the role with the OWNERSHIP privilege on the schema) or a role with the MANAGE GRANTS privilege can grant or revoke privileges on objects in the schema, including future grants. |
| MODIFY | Agent | Required to update the Cortex Agent. |
| MONITOR | Agent | Required to view threads, logs, and traces of the Cortex Agent. |
| USAGE | Agent | Required to query the Cortex Agent to generate responses. |

See moreShow less

Expand

Requests to the Cortex Agents API must include an authorization token. For details on how to authenticate to
the API, see [Authenticating Snowflake REST APIs with Snowflake](../../developer-guide/snowflake-rest-api/authentication). Note that the example in this topic uses a
session token to authenticate to a Snowflake account.

**Limiting access to specific roles**

By default, the CORTEX\_USER role is granted to the PUBLIC role. The PUBLIC role is automatically granted to all
users and roles. If you don’t want all users to have this privilege, you can revoke access to the PUBLIC role and
grant access to specific roles. For more information, see [Cortex LLM privileges](aisql.html#label-cortex-llm-privileges).

To provide selective access to Cortex Agents so that only a subset of users have access to the
feature, use the CORTEX\_AGENTS\_USER role.

**Limiting access using the Cortex Agents user role**

To provide selective access to Cortex Agents for specific users, use the SNOWFLAKE.CORTEX\_AGENT\_USER database role.
This role includes the privileges needed to call the Cortex Agent API.

Important

If your user roles have the CORTEX\_USER role, you must revoke access to the CORTEX\_USER role.
To revoke the CORTEX\_USER database role from your user roles, run the following command using the
ACCOUNTADMIN role:

CopyExpand

```
REVOKE DATABASE ROLE SNOWFLAKE.CORTEX_USER FROM ROLE agent;
```

Show lessSee more

Scroll to top

To provide access to Cortex Agents, use the ACCOUNTADMIN role to do the following:

1. Grant the SNOWFLAKE.CORTEX\_AGENT\_USER database role to a custom role.
2. Assign this custom role to users.

Note

You can’t grant database roles directly to users. For more information, see [GRANT DATABASE ROLE](../../sql-reference/sql/grant-database-role).

The following example:

1. Creates the custom role, `cortex_agent_user_role`.
2. Grants it the CORTEX\_AGENT\_USER database role.
3. Assigns this role to `example_user`.

CopyExpand

```
USE ROLE ACCOUNTADMIN;
CREATE ROLE cortex_agent_user_role;
GRANT DATABASE ROLE SNOWFLAKE.CORTEX_AGENT_USER TO ROLE cortex_agent_user_role;

GRANT ROLE cortex_agent_user_role TO USER example_user;
```

Show lessSee more

Scroll to top

You can also grant access to Cortex Agents through existing roles. For example, if you have an `agent` role
used by agents in your organization, you can grant access with a single GRANT statement:

CopyExpand

```
GRANT DATABASE ROLE SNOWFLAKE.CORTEX_AGENT_USER TO ROLE agent;
```

Show lessSee more

Scroll to top

## Authentication

Snowflake REST APIs support authentication via programmatic access tokens (PATs),
key pair authentication using JSON Web Tokens (JWTs), and OAuth.
For details, see [Authenticating Snowflake REST APIs with Snowflake](../../developer-guide/snowflake-rest-api/authentication).

Important

Cortex Agents uses models that might not be available in all regions. To access these models, you will have to enable cross-region inference, if feasible. For more information, see [Regional availability](aisql.html#label-cortex-llm-availability).

Important

Cortex Agent APIs are not supported from within a Streamlit in Snowflake (SiS) application using a warehouse runtime.
To call Cortex Agent APIs from a SiS app, use a container runtime instead. For more information, see
[Runtime environments for Streamlit apps](../../developer-guide/streamlit/app-development/runtime-environments).

## Cost considerations

> Cortex Agents incur charges for the orchestration and use of tools.
>
> * The orchestration usage is charged based on the tokens used.
> * Cortex Analyst is charged per token.
> * Cortex Search charges depend on the size of the index and the time it has persisted.
> * Warehouse charges depend on the size of the warehouse and how long it runs.

For more information, see the [Snowflake Service Consumption Table](https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf). Also, use of custom tools may incur [warehouse costs](../cost-understanding-compute).

## Models

You can use the following models with Cortex Agents. If the model is not available in the local region, you must use cross-region inference.

When creating an agent, we recommend selecting auto for the model. With this option, Cortex automatically selects the highest quality model for your account, and the quality automatically improves as new models become available.

* `auto`
* `claude-haiku-4-5`
* `claude-sonnet-4-5`
* `claude-4-sonnet`
* `claude-3-7-sonnet`
* `claude-3-5-sonnet`
* `openai-gpt-5`
* `openai-gpt-4-1`

The following tables show the models that are available for each region:

Cross-region and Cross-cloudNorth America

| Model | Cross-cloud  (Any region) | AWS US  (Cross-region) | AWS EU  (Cross-region) | AWS APJ  (Cross-region) | Azure US  (Cross-region) |
| --- | --- | --- | --- | --- | --- |
| `claude-haiku-4-5` | \* | \* |  |  |  |
| `claude-sonnet-4-5` | ✔ | ✔ | ✔ |  |  |
| `claude-4-sonnet` | ✔ | ✔ | ✔ | ✔ |  |
| `claude-3-7-sonnet` | ✔ | ✔ |  |  |  |
| `claude-3-5-sonnet` | ✔ | ✔ |  |  |  |
| `openai-gpt-5` | \* |  |  |  | \* |
| `openai-gpt-4.1` | ✔ |  |  |  | ✔ |

See moreShow less

Expand

We recommend using one of the models in the Cross-region and Cross-cloud tab. We do not recommend using `claude-3-5-sonnet` limited to the North America region.

| Model | AWS US West 2  (Oregon) | AWS US East 1  (N. Virginia) | Azure East US 2  (Virginia) |
| --- | --- | --- | --- |
| `claude-3-5-sonnet` | ✔ | ✔ |  |

See moreShow less

Expand

**\*** Indicates a preview function or model. Preview features are not suitable for production workloads.

## Cortex Agent Concepts

Cortex Agents use Cortex Analyst, Cortex Search and custom tools to plan tasks and generate responses. You can influence the orchestration with instructions. You can also specify attributes to dynamically select a tool based on business logic.

During an interaction, Agents use a thread to maintain context. A thread provides an easy retrieval of the entire conversation context for use in application logic.

You can collect feedback from end-users as you continuously iterate and refine the Agent. An explicit feedback mechanism (positive/negative rating) coupled with subjective feedback (text) allows you to capture user inputs throughout the lifecycle of the Agent.

### Agent object

The agent configuration includes all metadata, orchestration settings, and tool details that are stored in the agent object. You can use the agent object to interact with the agent.

### Threads

Threads persist the context of your interactions with the agent, so you don’t have to maintain context on the client application. To use threads, you create a thread object and reference the thread ID in the agent interactions.

### Orchestration

Cortex Agents use LLM-based orchestration to plan tasks and generate responses. You can control the orchestration with the following settings:

#### Models

For information about the models you can use with Cortex Agents for orchestration, see [Models](#label-cortex-agents-models).

#### Instructions

Response instructions allow you to configure the agent responses to a brand and tone of your preference.

#### Sample questions

You can use these questions to seed the conversation in your client application. These are common questions that can get users started with the interaction.

### Tools

Cortex Agents can orchestrate across both structured and unstructured data. Also, custom tools allow agents to interact with other backend systems or implement custom logic.

#### Cortex Analyst semantic view

You can use Cortex Analyst to create SQL queries from natural language. To use Cortex Analyst, you must create a Semantic Model. For more information, see [Create a semantic model](cortex-analyst.html#label-copilot-create-semantic-model).

#### Cortex Search Service

Use Cortex Search to search through your data. For more information, see [CREATE CORTEX SEARCH SERVICE](../../sql-reference/sql/create-cortex-search).

Agents can dynamically adjust the following search parameters if the user’s query requires it: filter conditions, metadata columns to retrieve, number of results,
per-index queries for multi-index services, and time-decay settings.

Note

The DEFAULT\_ROLE of the querying user must have USAGE privilege on the Cortex Search Service, as well as the database and schema
in which it resides.

#### Custom tools

You can use stored procedures and user defined functions (UDF) to implement custom business logic as a tool. For more information, see [Stored procedures overview](../../developer-guide/stored-procedure/stored-procedures-overview) and [User-defined functions overview](../../developer-guide/udf/udf-overview).

### Thinking and reflection

The Agent emits events throughout the interaction, providing insights into the reasoning process. These steps cover the initial splitting of tasks, sequencing into sub-tasks, and selection of tools for the sub-task. In addition, the agent also surfaces its reflections about tool results and how these influence further orchestration.

### Monitor and iterate

You can collect feedback from the end user as a rating (positive/negative), along with any subjective inputs (as text). These can be used to refine and improve the Agent over the lifecycle.

## Web search

Before providing web search access to your agents, an ACCOUNTADMIN role must first enable web search access at the account level. To properly enable web search:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » Agents.
3. Select Settings.
4. Select the Web search toggle to enable the feature, as shown below.

![Enable web search toggle](../../_images/enable-websearch.png)

After enabling web search at the account level, you can use the web search tool in your agents. For more information, see [Create an agent](cortex-agents-manage.html#label-snowflake-agents-create).

Snowflake will process your inputs according to the [Snowflake Privacy Notice](https://www.snowflake.com/en/legal/privacy/privacy-policy/#2) (§2). Web search may not be used for the purpose of redistributing or creating a competing web search service.

## Interact with agents

Cortex Agents support two distinct methods of interacting with agents through the REST API:

* **Configure an agent object to interact with the agent**: With this method, you first configure an agent object that can be reused for the entire interaction. Configuring an agent object simplifies client code and enables CI/CD for enterprise-ready applications.
* **Interact without an agent object**: With this method, you must pass the agent configuration as part of every interaction request. Interaction without an agent object allows you to quickly try out use cases and experiment with different scenarios.

For information about these methods, see [Configure and interact with Agents](cortex-agents-manage).

## Legal notices

Where your configuration of Cortex Agents uses a model provided on the
[Model and Service Flow-down Terms](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/ai-features/open-source-model-flow-down-terms/),
your use of that model is further subject to the terms for that model on that page.

The data classification of inputs and outputs are as set forth in the following table.

| Input data classification | Output data classification | Designation |
| --- | --- | --- |
| Usage Data | Customer Data | Covered AI Features [[1]](#id3) |

See moreShow less

Expand

[[1](#id2)]

Represents the defined term used in the AI Terms and Acceptable Use Policy.

For additional information, refer to [Snowflake AI and ML](../../guides-overview-ai-features).
