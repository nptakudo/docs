---
title: "Configure and interact with Agents"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-agents-manage"
---

# Configure and interact with Agents

You can build an agent with the following methods:

* In Snowsight
* Using the [Agents REST API](cortex-agents-rest-api)
* With the [Cortex Agents SQL](../../sql-reference/commands-cortex-agent) commands

You can then integrate the agent into your application to perform tasks or respond to queries. You must first create an agent object that contains information such as the metadata, tools, and orchestration instructions that the agent can use to perform a task or answer questions. You can then reference the agent object in your application to integrate the agent’s functionality. You can configure a thread to maintain the context in memory, so that the client does not have to send the context at every turn of the conversation.

Note

Snowflake REST APIs support authentication via programmatic access tokens (PATs), key pair authentication using JSON Web Tokens (JWTs), and OAuth. For details, see [Authenticating Snowflake REST APIs with Snowflake](../../developer-guide/snowflake-rest-api/authentication).

## Create an agent

Create an agent object by specifying the database and schema where the agent should be located, along with a name and description for the agent. In addition, specify the display name, avatar, and the color. These attributes are used by the client application to display the agent. The display name is also used as the handle to reference the agent in conversations.

For best practices when creating an agent, see [Best Practices to Building Cortex Agents](https://www.snowflake.com/en/developers/guides/best-practices-to-building-cortex-agents).

The following examples show how to create an agent object from Snowsight or using the REST API:

> Method 1: Snowsight UIMethod 2: REST APIMethod 3: SQL
>
> 1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
> 2. In the navigation menu, select AI & ML » Agents.
> 3. Select Create agent.
> 4. For Agent object name, specify a name for the agent that is displayed to users in the UI.
> 5. For Display name, specify a name for the agent that is displayed to admins in the agent list.
> 6. Select Create agent.
> 7. Prompt the agent with general knowledge requests.
>
> 1. Create an agent object by specifying the database and schema where the agent will be created, as well as the parameters needed for the agent. You can also specify tool fields when creating the agent object.
>
>    > CopyExpand
>    >
>    > ```
>    > curl -X POST "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/databases/<database-name>/schemas/<schema-name>/agents" \
>    > --header 'Content-Type: application/json' \
>    > --header 'Accept: application/json' \
>    > --header "Authorization: Bearer $PAT" \
>    > --data '{
>    >     "name": "TransportationAgent",
>    >     "comment": "This agent handles queries related to transportation methods and costs.",
>    >     "models": {
>    >         "orchestration": "claude-4-sonnet"
>    >     }
>    > }'
>    > ```
>    >
>    > Show lessSee more
>    >
>    > Scroll to top
>
> Create an agent object in the database and schema where the agent will be created. You can specify the agent properties and specification using the `FROM SPECIFICATION` clause in the CREATE AGENT command. For more information, see [CREATE AGENT](../../sql-reference/sql/create-agent).
>
> CopyExpand
>
> ```
> CREATE OR REPLACE AGENT myagent
>   COMMENT = 'agent level comment'
>   PROFILE = '{"display_name": "My Business Assistant", "avatar":  "business-icon.png", "color": "blue"}'
>   FROM SPECIFICATION
>   $$
>   orchestration:
>     budget:
>       seconds: 30
>       tokens: 16000
>
>   instructions:
>     response: "You will respond in a friendly but concise manner"
>     orchestration: "For any revenue question use Analyst; for policy use Search"
>     system: "You are a friendly agent that helps with business questions"
>     sample_questions:
>       - question: "What was our revenue last quarter?"
>         answer: "I'll analyze the revenue data using our financial database."
>
>   tools:
>     - tool_spec:
>         type: "cortex_analyst_text_to_sql"
>         name: "Analyst1"
>         description: "Converts natural language to SQL queries for financial analysis"
>     - tool_spec:
>         type: "cortex_search"
>         name: "Search1"
>         description: "Searches company policy and documentation"
>     - tool_spec:
>         type: "data_to_chart"
>         name: "data_to_chart"
>         description: "Generates visualizations from data"
>
>   tool_resources:
>     Analyst1:
>       semantic_view: "db.schema.semantic_view"
>     Search1:
>       name: "db.schema.service_name"
>       max_results: "5"
>       filter:
>         "@eq":
>           region: "North America"
>       title_column: "<title_name>"
>       id_column: "<column_name>"
>       columns_and_descriptions:
>         TEXT:
>           description: "The main text content of the document"
>           type: "string"
>           searchable: true
>           filterable: false
>         CATEGORY:
>           description: "Document category. Values include: policy, guide, reference."
>           type: "string"
>           searchable: false
>           filterable: true
>   $$;
> ```
>
> Show lessSee more
>
> Scroll to top

## Add tools

After you’ve created the agent, you need to add tools and provide instructions on how to orchestrate across the tools. Agents support the following tool types:

> * **Cortex Analyst:** You specify the semantic views so that Cortex Analyst can use these to retrieve structured data. The Agents can route across multiple semantic views to provide the response.
>
>   Note
>
>   When Cortex Analyst is invoked by an agent, it does not have access to open source LLM models. For a list of the models that Cortex Analyst can use when invoked by an agent, see the [Snowflake Service Consumption Table](https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf).
> * **Cortex Search:** You provide the Cortex Search indices as tools, along with column descriptions for filterable and searchable columns. The Cortex Agent uses the Cortex Search indices to retrieve unstructured data.
> * **Data to Chart:** You can enable the agent to automatically generate visualizations from data. When included in the tools array, the agent can create charts using Vega-Lite specifications in response to queries that would benefit from visual representation.
> * **Custom tools:** You can implement code for a specific business logic as a stored procedure or user defined function (UDF). Alternatively, you can use the custom tools to retrieve data from your backend systems using APIs.
> * **Web Search:** You can enable the agent to search the web and use those search results to generate responses and plan tasks.

You also specify the resources used by each tool. For example, on Cortex Analyst you specify the warehouse along with the timeout for SQL query execution. Similarly for Cortex Search, you specify the filters and column names used in the search query, along with the max results in the search response. For custom tools, you will provide the warehouse details.

> Method 1: Snowsight UIMethod 2: REST APIMethod 3: SQL
>
> To modify the configuration for an existing agent, follow these steps:
>
> 1. In the navigation menu, select AI & ML » Agents.
> 2. From the list of agents, select the agent that you want to modify.
>    :   The configuration details for the agent are displayed.
> 3. Select Edit.
> 4. For Description, describe the agent and how users can interact with it.
> 5. To add sample questions that users can ask the agent, enter a sample question and select Add a question.
> 6. Select Tools. Add one or more of the following tools.
>
>    > * **To add a semantic view in Cortex Analyst to the agent**: This section assumes that you already have a semantic view created. For information about semantic views and how to create one, see [Overview of semantic views](../views-semantic/overview).
>    >
>    >   > 1. Find Cortex Analyst and select the respective + Add button.
>    >   > 2. For Name, enter a name for the semantic view.
>    >   > 3. Select Semantic view.
>    >   > 4. Select the semantic view that the agent uses.
>    >   > 5. For Warehouse, select the warehouse that the agent uses to run queries.
>    >   > 6. For Query timeout (seconds), specify the maximum time in seconds that the agent waits for a query to complete before timing out.
>    >   > 7. For Description, describe the semantic view.
>    >   > 8. Select Add.
>    > * **To add a Cortex Search service to the agent**: This section assumes that you’ve already created a Cortex Search service. For information about creating a Cortex Search service, see [Cortex Search](cortex-search/cortex-search-overview). You can also use a Cortex Knowledge Extension (CKE) that is shared with you. For a tutorial that uses a CKE, see [Common issues and solutions](snowflake-intelligence/troubleshooting.html#label-snowflake-intelligence-troubleshooting).
>    >
>    >   > 1. Find Cortex Search Services and select the respective + Add button.
>    >   > 2. For Name, enter a name for the Cortex Search service.
>    >   > 3. For Description, describe the Cortex Search service.
>    >   > 4. For Search service, select the Cortex Search service that the agent uses.
>    >   > 5. Under Tool details, add Columns Description to help the agent effectively use the search service. Column descriptions are not required for all columns, but providing them for filterable and searchable columns is recommended to improve the quality of results. Provide a description that explains the column’s content and sample values.
>    >   > 6. Select Add.
>    > * **To add a custom tool to the agent**: By adding custom tools, you can extend the functionality of your agents. With custom tools, the agent can call stored procedures and functions that you have defined to perform actions or do computations. This section assumes that you’ve already created a custom tool. For information about procedures and functions, see [Extending Snowflake with Functions and Procedures](../../developer-guide/extensibility).
>    >
>    >   > 1. Find Custom tools and select the respective + Add button.
>    >   > 2. For Name, enter a name for the custom tool.
>    >   > 3. For Resource type, select whether the custom tool is a function or a procedure. For information about whether to use a function or procedure, see [Choosing whether to write a stored procedure or a user-defined function](../../developer-guide/stored-procedures-vs-udfs).
>    >   > 4. For Custom tool identifier, select the existing function or procedure that you want to add as a custom tool.
>    >   > 5. The related parameters for the function or procedure automatically appear. You can manually add parameters for the custom tool by adding a name, type, description, and selecting whether the parameter is required. You can also modify parameters that automatically populate.
>    >   >
>    >   >    Note
>    >   >
>    >   >    Snowflake Cortex does not support stored procedures and custom tools with a parameter of type `object`.
>    >   > 6. For Warehouse, select the warehouse that the agent uses to run the custom tool. You must manually select a warehouse.
>    >   > 7. For Description, describe the custom tool and how to use it.
>    >   > 8. Select Add.
>    >   > 9. After creating the custom tool, make sure users are granted USAGE privileges to the function or procedure that you added as a custom tool. When using stored procedures, agents maintain whether the procedure runs with owner’s or caller’s rights. For information about owner’s and caller’s rights, see [Understanding caller’s rights and owner’s rights stored procedures](../../developer-guide/stored-procedure/stored-procedures-rights).
>    > * **To add web search tool to the agent**: This section assumes that you’ve already enabled web search at the account level. For information about enabling web search at the account level, see [Web search](cortex-agents.html#label-cortex-agents-web-search).
>    >
>    >   > 1. Find Web search and select the respective toggle to enable the feature.
> 7. Select Save.
>
> To add tools to an agent using the REST API, add the following payloads as part of a request to [Update Cortex Agent](cortex-agents-rest-api.html#label-snowflake-agents-rest-api-update). You can also specify these fields when creating the agent object.
>
> > * **Add Cortex Analyst tool and tool resources**: The following example shows how to add a Cortex Analyst tool and tool resources to an existing agent object.
> >
> >   > 1. Add a Cortex Analyst tool
> >   >
> >   >    > CopyExpand
> >   >    >
> >   >    > ```
> >   >    > curl -X PUT "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/databases/<database-name>/schemas/<schema-name>/agents/<agent-name>" \
> >   >    > --header 'Content-Type: application/json' \
> >   >    > --header 'Accept: application/json' \
> >   >    > --header "Authorization: Bearer $PAT" \
> >   >    > --data '{
> >   >    >  "tools": [
> >   >    >   {
> >   >    >    "tool_spec": {
> >   >    >     "description": "Analyst to analyze price",
> >   >    >     "type": "cortex_analyst_text_to_sql",
> >   >    >     "name": "Analyst1"
> >   >    >    }
> >   >    >   }
> >   >    >  ]
> >   >    > }'
> >   >    > ```
> >   >    >
> >   >    > Show lessSee more
> >   >    >
> >   >    > Scroll to top
> >   > 2. Add a Cortex Analyst tool resource
> >   >
> >   >    > CopyExpand
> >   >    >
> >   >    > ```
> >   >    > curl -X PUT "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/databases/<database-name>/schemas/<schema-name>/agents/<agent-name>" \
> >   >    > --header 'Content-Type: application/json' \
> >   >    > --header 'Accept: application/json' \
> >   >    > --header "Authorization: Bearer $PAT" \
> >   >    > --data '{
> >   >    >  "tool_resources": {
> >   >    >   "Analyst1": {
> >   >    >    "semantic_model_file": "stage1",
> >   >    >    "semantic_view": "The name of the Snowflake native semantic model object",
> >   >    >    "execution_environment": {"type":"warehouse", "warehouse":"my_wh"}
> >   >    >   }
> >   >    >  }
> >   >    > }'
> >   >    > ```
> >   >    >
> >   >    > Show lessSee more
> >   >    >
> >   >    > Scroll to top
> > * **Add Cortex Search tool and tool resources**: The following example shows how to add a Cortex Search tool and tool resources to an existing agent object.
> >
> >   > 1. Add a Cortex Search tool
> >   >
> >   >    > CopyExpand
> >   >    >
> >   >    > ```
> >   >    > curl -X PUT "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/databases/<database-name>/schemas/<schema-name>/agents/<agent-name>" \
> >   >    > --header 'Content-Type: application/json' \
> >   >    > --header 'Accept: application/json' \
> >   >    > --header "Authorization: Bearer $PAT" \
> >   >    > --data '{
> >   >    >  "tool_spec": {
> >   >    >   "type": "cortex_search",
> >   >    >   "name": "Search1"
> >   >    >  }
> >   >    > }'
> >   >    > ```
> >   >    >
> >   >    > Show lessSee more
> >   >    >
> >   >    > Scroll to top
> >   > 2. Add a Cortex Search tool resource:
> >   >
> >   >    > CopyExpand
> >   >    >
> >   >    > ```
> >   >    > curl -X PUT "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/databases/<database-name>/schemas/<schema-name>/agents/<agent-name>" \
> >   >    > --header 'Content-Type: application/json' \
> >   >    > --header 'Accept: application/json' \
> >   >    > --header "Authorization: Bearer $PAT" \
> >   >    > --data '{
> >   >    >  "tool_resources": {
> >   >    >   "Search1": {
> >   >    >    "search_service": "db.schema.service_name",
> >   >    >    "filter": {"@eq": {"region": "North America"} },
> >   >    >    "max_results": 10,
> >   >    >    "title_column": "TITLE",
> >   >    >    "columns_and_descriptions": {
> >   >    >      "TEXT": {
> >   >    >        "description": "The main text content of the document",
> >   >    >        "type": "string",
> >   >    >        "searchable": true,
> >   >    >        "filterable": false
> >   >    >      },
> >   >    >      "CATEGORY": {
> >   >    >        "description": "Document category. Values include: policy, guide, reference.",
> >   >    >        "type": "string",
> >   >    >        "searchable": false,
> >   >    >        "filterable": true
> >   >    >      },
> >   >    >      "AUTHOR": {
> >   >    >        "description": "Author name in format: firstname.lastname",
> >   >    >        "type": "string",
> >   >    >        "searchable": false,
> >   >    >        "filterable": true
> >   >    >      }
> >   >    >    }
> >   >    >   }
> >   >    >  }
> >   >    > }'
> >   >    > ```
> >   >    >
> >   >    > Show lessSee more
> >   >    >
> >   >    > Scroll to top
> >   >    >
> >   >    > The `columns_and_descriptions` field is a map of column names to column properties. Descriptions are not required for all columns, but providing them for filterable and searchable columns improves the quality of results. Each column entry must include:
> >   >    >
> >   >    > + `description` (string): A description of the column content and sample values. Include guidance on when and how to filter on this column.
> >   >    > + `type` (string): The column data type. Use `"string"` or `"datetime"`.
> >   >    > + `searchable` (boolean): Set to `true` for text index columns that can be searched. Vector index columns are not supported.
> >   >    > + `filterable` (boolean): Set to `true` for attribute columns that can be used in filter conditions.
> > * **Add data\_to\_chart tool**: The following example shows how to add the data to chart tool to an existing agent object.
> >
> >   > 1. Add the data\_to\_chart tool
> >   >
> >   >    > CopyExpand
> >   >    >
> >   >    > ```
> >   >    > curl -X PUT "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/databases/<database-name>/schemas/<schema-name>/agents/<agent-name>" \
> >   >    > --header 'Content-Type: application/json' \
> >   >    > --header 'Accept: application/json' \
> >   >    > --header "Authorization: Bearer $PAT" \
> >   >    > --data '{
> >   >    >  "tools": [
> >   >    >   {
> >   >    >    "tool_spec": {
> >   >    >      "type": "data_to_chart",
> >   >    >      "name": "data_to_chart",
> >   >    >      "description": "Generates visualizations from data"
> >   >    >    }
> >   >    >   }
> >   >    >  ]
> >   >    > }'
> >   >    > ```
> >   >    >
> >   >    > Show lessSee more
> >   >    >
> >   >    > Scroll to top
> > * **Add custom tool and tool resources**: The following example shows how to add a custom tool and tool resources to an existing agent object.
> >
> >   > 1. Add a custom tool
> >   >
> >   >    > CopyExpand
> >   >    >
> >   >    > ```
> >   >    > curl -X PUT "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/databases/<database-name>/schemas/<schema-name>/agents/<agent-name>" \
> >   >    > --header 'Content-Type: application/json' \
> >   >    > --header 'Accept: application/json' \
> >   >    > --header "Authorization: Bearer $PAT" \
> >   >    > --data '{
> >   >    >  "tools": [
> >   >    >   {
> >   >    >    "tool_spec": {
> >   >    >      "description": "Custom tool",
> >   >    >      "type": "generic",
> >   >    >      "name": "custom1"
> >   >    >    }
> >   >    >   }
> >   >    >  ]
> >   >    > }'
> >   >    > ```
> >   >    >
> >   >    > Show lessSee more
> >   >    >
> >   >    > Scroll to top
> >   > 2. Add a custom tool resource
> >   >
> >   >    > CopyExpand
> >   >    >
> >   >    > ```
> >   >    > curl -X PUT "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/databases/<database-name>/schemas/<schema-name>/agents/<agent-name>" \
> >   >    > --header 'Content-Type: application/json' \
> >   >    > --header 'Accept: application/json' \
> >   >    > --header "Authorization: Bearer $PAT" \
> >   >    > --data '{
> >   >    >  "tool_resources": {
> >   >    >   "Custom1": {
> >   >    >    "user-defined-function-argument": "argument1"
> >   >    >   }
> >   >    >  }
> >   >    > }'
> >   >    > ```
> >   >    >
> >   >    > Show lessSee more
> >   >    >
> >   >    > Scroll to top
> > * **Add web\_search tool**: The following example shows how to add the web\_search tool to an existing agent object. This section assumes that you’ve already enabled web search at the account level. For information about enabling web search at the account level, see [Web search](cortex-agents.html#label-cortex-agents-web-search).
> >
> >   > 1. Add the web\_search tool
> >   >
> >   >    > CopyExpand
> >   >    >
> >   >    > ```
> >   >    > curl -X PUT "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/databases/<database-name>/schemas/<schema-name>/agents/<agent-name>" \
> >   >    > --header 'Content-Type: application/json' \
> >   >    > --header 'Accept: application/json' \
> >   >    > --header "Authorization: Bearer $PAT" \
> >   >    > --data '{
> >   >    >  "tools": [
> >   >    >   {
> >   >    >    "tool_spec": {
> >   >    >      "type": "web_search",
> >   >    >      "name": "Web Search",
> >   >    >    }
> >   >    >   }
> >   >    >  ]
> >   >    > }'
> >   >    > ```
> >   >    >
> >   >    > Show lessSee more
> >   >    >
> >   >    > Scroll to top
>
> You can update an agent object to add tools and tool resources using the ALTER AGENT command. For information about the ALTER AGENT command, see [ALTER AGENT](../../sql-reference/sql/alter-agent).
>
> Note
>
> The new specification completely replaces the existing one. Fields that are not included in the new specification are removed.
>
> CopyExpand
>
> ```
> ALTER AGENT <agent_name> MODIFY LIVE VERSION SET SPECIFICATION =
> $$
> models:
>     orchestration: claude-4-sonnet
>
>   orchestration:
>     budget:
>       seconds: 30
>       tokens: 16000
>
>   instructions:
>     response: "You will respond in a friendly but concise manner"
>     orchestration: "For any revenue question use Analyst; for policy use Search"
>     system: "You are a friendly agent that helps with business questions"
>     sample_questions:
>       - question: "What was our revenue last quarter?"
>         answer: "I'll analyze the revenue data using our financial database."
>
>   tools:
>     - tool_spec:
>         type: "cortex_analyst_text_to_sql"
>         name: "Analyst1"
>         description: "Converts natural language to SQL queries for financial analysis"
>     - tool_spec:
>         type: "cortex_search"
>         name: "Search1"
>         description: "Searches company policy and documentation"
>     - tool_spec:
>         type: "data_to_chart"
>         name: "data_to_chart"
>         description: "Generates visualizations from data"
>
>   tool_resources:
>     Analyst1:
>       semantic_view: "db.schema.semantic_view"
>     Search1:
>       name: "db.schema.service_name"
>       max_results: "5"
>       filter:
>         "@eq":
>           region: "North America"
>       title_column: "<title_name>"
>       id_column: "<column_name>"
> $$;
> ```
>
> Show lessSee more
>
> Scroll to top

## Specify orchestration

Cortex Agents orchestrate the task by breaking it into a sequence of sub-tasks and identifying the right tool for each sub-task. You specify the LLM that the Agent should use to conduct this orchestration. You can also influence the orchestration by providing instructions. For example, consider an agent built to respond to retail product questions. You can use the orchestration instruction `"Use the search tool for all requests related to refunds"` to ensure the Agent only provides refund policy details (using Cortex Search) and does not actually calculate the refund amounts (using Cortex Analyst). You can also specify instructions to align the response to a brand or a tone, such as `"Always provide provide a concise response; maintain a friendly tone"`.

Method 1: Snowsight UIMethod 2: REST APIMethod 3: SQL

1. Select Orchestration.
2. For the Orchestration model, select the model that the agent uses to handle orchestration.
3. For Planning instructions, provide instructions that influence tool selection by the agent based on user-provided input. These can include specific instructions about when to use each tool, or even to always use a tool at the beginning or end of a response.
4. For Response instruction, provide instructions that the model uses for response generation. For example, specify if you want the agent to prioritize chart creation, or to keep a certain tone with users.
5. For Budget configuration, you can specify time limit and token limit for the agent. The budget is the maximum amount of time or tokens that the agent can use to generate a response. After either one of the limits is reached, the agent will stop generating a response. Token limits are used only for orchestration and don’t include tokens used by Cortex Analyst, Cortex Search, and other tools invoked.
6. Select Save.

> To update an agent using the REST API, add the following payloads as part of a request to [Update Cortex Agent](cortex-agents-rest-api.html#label-snowflake-agents-rest-api-update). You can also specify these fields when creating the agent object. The following procedure shows how to update the agent with planning and response instructions, and specify the LLM model used for orchestration.

1. Update the LLM model

   > CopyExpand
   >
   > ```
   > curl -X PUT "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/databases/<database-name>/schemas/<schema-name>/agents/<agent-name>" \
   > --header 'Content-Type: application/json' \
   > --header 'Accept: application/json' \
   > --header "Authorization: Bearer $PAT" \
   > --data '{
   >  "models": {
   >   "orchestration": "llama3.3-70B"
   > }'
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
2. Specify the planning and response instructions

   > CopyExpand
   >
   > ```
   > curl -X PUT "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/databases/<database-name>/schemas/<schema-name>/agents/<agent-name>" \
   > --header 'Content-Type: application/json' \
   > --header 'Accept: application/json' \
   > --header "Authorization: Bearer $PAT" \
   > --data '{
   >  "instructions": {
   >   "response": "Always provide a concise response and maintain a friendly tone.",
   >   "orchestration": "<orchestration instructions>",
   >   "system": "You are a helpful data agent."
   >  }
   > }'
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

You can update an agent object to add orchestration information using the ALTER AGENT command. For information about the ALTER AGENT command, see [ALTER AGENT](../../sql-reference/sql/alter-agent).

CopyExpand

```
ALTER AGENT <agent_name> MODIFY LIVE VERSION SET SPECIFICATION =
$$
models:
    orchestration: claude-4-sonnet

  orchestration:
    budget:
      seconds: 30
      tokens: 16000

  instructions:
    response: "You will respond in a friendly but concise manner"
    orchestration: "For any revenue question use Analyst; for policy use Search"
    system: "You are a friendly agent that helps with business questions"
    sample_questions:
      - question: "What was our revenue last quarter?"
        answer: "I'll analyze the revenue data using our financial database."

  tools:
    - tool_spec:
        type: "cortex_analyst_text_to_sql"
        name: "Analyst1"
        description: "Converts natural language to SQL queries for financial analysis"
    - tool_spec:
        type: "cortex_search"
        name: "Search1"
        description: "Searches company policy and documentation"
    - tool_spec:
        type: "data_to_chart"
        name: "data_to_chart"
        description: "Generates visualizations from data"

  tool_resources:
    Analyst1:
      semantic_view: "db.schema.semantic_view"
    Search1:
      name: "db.schema.service_name"
      max_results: "5"
      filter:
        "@eq":
          region: "North America"
      title_column: "<title_name>"
      id_column: "<column_name>"
$$;
```

Show lessSee more

Scroll to top

## Set up access to the agent

Important

By default, Cortex Agents uses the user’s default role and the default warehouse. If another user is using the agent, make sure that they’ve done the following:

* Set a default role
* Set a default warehouse
* Granted USAGE on the agent to the default role

For information about granting usage, see [Access control requirements](cortex-agents.html#label-cortex-agents-access-control).

You must use the user’s default role when calling or updating Cortex Agents. To allow another role to use the agent, grant USAGE on the agent to that role:

CopyExpand

```
GRANT USAGE ON AGENT <database_name>.<schema_name>.<agent_name> TO ROLE <role_name>;
```

Show lessSee more

Scroll to top

Set up access policies from Snowsight UI or using SQL so that users can access the Agent. Specify the role to provide access to the Agent.

Method 1: Snowsight UIMethod 2: SQL

1. Select Access.
2. To give a role access to the agent, select Add role, then select the role from the dropdown menu.
3. Select Save.

CopyExpand

```
GRANT USAGE ON AGENT myagent TO ROLE test_rl;
```

Show lessSee more

Scroll to top

## Review the agent

After you have built the Agent, you can review the Agent to verify all parameters.

Method 1: Snowsight UIMethod 2: REST APIMethod 3: SQL

Note

When reviewing agents from Snowsight, you can only view agents in the Agent Admin UI. You cannot view agents in the database object explorer.

1. In the navigation menu, select AI & ML » Agents.
2. From the list of agents, select the agent that you want to view the details for. This opens a new page that gives an overview of the agent details.
3. To review all agent details, select Next.

You can list and describe agents using the REST APIs.

1. List all agents.

   > CopyExpand
   >
   > ```
   > curl -X GET "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/databases/{database}/schemas/{schema}/agents:" \
   >  --header 'Content-Type: application/json' \
   >  --header 'Accept: application/json' \
   >  --header "Authorization: Bearer $PAT" \
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
2. Describe the desired agent.

   > CopyExpand
   >
   > ```
   > curl -X GET "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/databases/{database}/schemas/{schema}/agents/{name}:" \
   >  --header 'Content-Type: application/json' \
   >  --header 'Accept: application/json' \
   >  --header "Authorization: Bearer $PAT" \
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

You can list and describe agents using SQL.

1. List all agents.

   CopyExpand

   ```
   SHOW AGENTS IN ACCOUNT;
   ```

   Show lessSee more

   Scroll to top
2. Describe the desired agent.

   CopyExpand

   ```
   DESCRIBE AGENT myagent;
   ```

   Show lessSee more

   Scroll to top

## Test the agent

After you’ve created the agent, you can test it to see how it responds to user queries. You can also test the agent using [Agent run request with agent object](cortex-agents-run.html#label-snowflake-data-agents-run-rest-api).

To test the agent, follow these steps:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » Agents.
3. Select the agent from the list of agents.
4. On the agent details page, enter a query in the agent playground.
5. Verify that the agent responds to the query as expected. If the agent does not respond as expected, modify the agent’s configuration by following the steps in [Add tools](#label-snowflake-agents-modify-agents).

## Interact with the agent

After creating the agent object, you can integrate the agent directly into your application using the REST API. To maintain context during the interaction, use a thread. The agent object and thread combined simplify the client application code.

### Create a thread

Create a thread to maintain the context during a conversation. When the thread is created successfully, the system returns a `Thread ID`.

CopyExpand

```
curl -X POST "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/cortex/threads" \
--header 'Content-Type: application/json' \
--header 'Accept: application/json' \
--header "Authorization: Bearer $PAT" \
--data '{
    "origin_application": <application_name>,
}'
```

Show lessSee more

Scroll to top

### Send a request to the agent

To interact with the Agent, you must pass the agent object, thread ID, and a unique `parent_message_id` as part of your REST API request. The initial `parent_message_id` should be `0`.

CopyExpand

```
curl -X POST "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/databases/{database}/schemas/{schema}/agents/{name}:run" \
 --header 'Content-Type: application/json' \
 --header 'Accept: application/json' \
 --header "Authorization: Bearer $PAT" \
 --data '{
     "thread_id": <thread id for context>,
     "parent_message_id": <parent message id>,
     "messages": [
      {
         "role": "user",
         "content": [
           {
            "type": "text",
             "text": "What are the projected transportation costs for the next three quarters? "
             }
         ]
       }
     ],
     "tool_choice": {
       "type": "required",
       "name": [
         "Analyst1",
         "Search1"
       ]
     }
 }'
```

Show lessSee more

Scroll to top

## Collect feedback about the agent

You can collect feedback from users about the responses given by the agent. This feedback can help you refine the agent as you iterate on your use case. Users can provide an objective rating (postive/negative), as well as more subjective detail with a message. Also, users can classify the feedback across one of many categories.

CopyExpand

```
curl -X POST "$SNOWFLAKE_ACCOUNT_BASE_URL/api/v2/databases/<database-name>/schemas/<schema-name>/agents/<agent-name>:feedback:" \
--header 'Content-Type: application/json' \
--header 'Accept: application/json' \
--header "Authorization: Bearer $PAT" \
--data '{
    "request_id": "<request-id>",
    "positive": true
    "feedback_message": "This answer was great",
    "categories":[
        "category1", "category2", "category3"
    ],
    "thread_id": "<thread-id>"
}'
```

Show lessSee more

Scroll to top

## Interact without an agent object

In some cases, you may want to get started with Cortex Agents by using `agent:run` without an agent object. For example, this may be useful when you want to quickly try out a use case. For more information about the REST API, see [Agent run without an agent object](cortex-agents-run.html#label-snowflake-lite-agents-run-rest-api).

Note

When interacting with an agent without creating an agent object, you must manually maintain the context for the agent with every request.
