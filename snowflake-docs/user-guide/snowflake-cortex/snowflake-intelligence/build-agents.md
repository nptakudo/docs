---
title: "Build agents"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/snowflake-intelligence/build-agents"
---

# Build agents

You can build agents for Snowflake Intelligence using the following methods:

* In Snowsight
* Using the [Agent object REST API](../cortex-agents-rest-api)
* With the [Cortex Agents SQL](../../../sql-reference/commands-cortex-agent) commands

The following sections provide information about how to build agents for Snowflake Intelligence using SQL commands. Each section provides information about a different part of the agent configuration. The final section shows an example of an agent configuration that includes all of the components described in this topic.

For more information about the other methods to create an agent and the options available, see [Configure and interact with Agents](../cortex-agents-manage).

## Agent structure

An agent consists of the following parts:

* The base model that provides the foundation for the agent’s behavior
* A model (the orchestrator) that interprets intent, selects the right tools, and plans the sequence of actions
* Instructions for the agent’s behavior
* Tools for the agent to use
* Resources for the tools

The following sections provide information about model selection and tool configuration. This example uses a semantic view, a Cortex Search service, and a custom tool to provide answers. Although you can create a basic agent that doesn’t use any of these tools, that basic agent can only use the base model to provide answers. As a result, the agent lacks access to data within your Snowflake account and has limited context for answers.

For information about the other components of the agent, see [Cortex Agents](../cortex-agents).

## Prerequisites

To create a Cortex Agent, you must use a role with the following privileges:

| Privilege | Object | Notes |
| --- | --- | --- |
| CREATE AGENT | Schema | Required to create the Cortex Agent. |
| USAGE | Database, schema | Required to create the Cortex Agent in the specified database and schema. |

See moreShow less

Expand

The following code grants the necessary privileges to create a Cortex Agent:

> CopyExpand
>
> ```
> GRANT USAGE ON DATABASE <database_name> to ROLE <role_name>;
> GRANT USAGE ON SCHEMA <database_name>.<schema_name> to ROLE <role_name>;
> GRANT CREATE AGENT ON SCHEMA <database_name>.<schema_name> to ROLE <role_name>;
> ```
>
> Show lessSee more
>
> Scroll to top

In addition to the privileges required to create a Cortex Agent, the following prerequisites are necessary to connect the agent to specific tools:

* A semantic view to connect to the agent
  :   For information about creating a semantic view, see [Overview of semantic views](../../views-semantic/overview).
* A Cortex Analyst tool to connect to the agent
  :   For information about creating a Cortex Analyst tool, see [Cortex Analyst](../cortex-analyst).
* Unstructured data in a database to connect to the agent
* A Cortex Search tool to connect to the agent
  :   For information about creating a Cortex Search tool, see [Cortex Search](../cortex-search/cortex-search-overview).
* A custom tool to connect to the agent
  :   For information about creating user-defined functions (UDFs) and stored procedures to use as custom tools, see [Extending Snowflake with Functions and Procedures](../../../developer-guide/extensibility).

To attach tools to an agent, the role that is used to create the agent must have the following privileges:

| Privilege | Object | Notes |
| --- | --- | --- |
| USAGE | Cortex Search service | Required to add the Cortex Search service to the Cortex Agent. |
| SELECT | Table/View | Required to access the objects referenced in the agent’s semantic view/model. |
| USAGE | Tools | Required to access all of the custom tools to attach to the agent. For example, if the custom tool is a stored procedure, then the you must have USAGE on the procedure. |
| USAGE | Semantic view/model | Required to access the semantic view/model to attach to the agent. |

See moreShow less

Expand

## Agent configuration basics

When you create an agent, you must specify information about the agent, such as the name, description, and model. You can also specify the tools that the agent can use and the resources that the agent can access. These resources are passed as a YAML specification in the `FROM SPECIFICATION` clause of the `CREATE AGENT` command.

The following recommendations provide best practices for this configuration:

**Scope agents narrowly:** Before adding tools or writing instructions, define why the agent exists, who it serves, and what specific questions it should answer. This step shapes everything that follows, from tool selection to performance and trust. Snowflake recommends that you narrow the agent’s scope to a specific, high-value use case.

After an agent proves reliable in one area, you can replicate the pattern for others. For example, you could have one agent to analyze your store’s recent sales and marketing data, and another that recommends the best SKUs to pitch to the retailer.

**Select the number of tools carefully:** Every agent should have access to only the tools it needs. To determine that, consider the documents or data that the agent needs to fulfill its purpose. If the agent needs to access unstructured data, use [Cortex Search](../cortex-search/cortex-search-overview). If the agent needs to access structured data, use [Cortex Analyst](../cortex-analyst). If the agent needs other tools, you can use custom tools.

**Write a useful tool description:** These descriptions are used to help the agent understand what the tool does and how to use it. Unclear tool descriptions can create cascading failures and lead to “hallucinations.”

> To create a useful tool description, follow these guidelines:
>
> > * Add a clear and specific tool name that clarifies the tool’s domain (“Customer”, “Sales”) and function (“Analytics”, “Search”).
> > * Write a purpose-driven tool description that tells the agent:
> >
> >   + What the tool does
> >   + Which data it accesses
> >   + When to use it
> >   + When NOT to use it
> > * Be explicit about the tool’s expected inputs. Ambiguous inputs to your tools lead to incorrect tool calls and errors.
> >
> >   + Be specific.
> >   + Specify the data format.
> >   + Provide clear data instructions.
> >   + Provide default guidance.
> >   + Use consistent terminology.

For more agent configuration recommendations, see [Best Practices for Building Cortex Agents](https://www.snowflake.com/en/developers/guides/best-practices-to-building-cortex-agents/).

## Model selection

When you create an agent, we recommend that you select auto for the model. With this option, Cortex automatically selects the highest quality model for your account, and the quality automatically improves as new models become available. For more information about the available models, see [Supported models and regions](reference.html#label-snowflake-intelligence-models).

The following example shows how to specify the model for the agent:

CopyExpand

```
models:
  orchestration: auto
```

Show lessSee more

Scroll to top

### Cross-region inference

Important

Cross-region inference is disabled by default. We recommend using cross-region inference to access the full set of LLMs and avoid limitations within a single region.

When using a model that is not available in the local region, you must use Cortex cross-region inference. This setting enables inference requests to be processed in a different region from the default region. The parameter for cross-region inference can only be set at the account level by the ACCOUNTADMIN role, not at the user or session levels.

To set the parameter, use the following command:

CopyExpand

```
ALTER ACCOUNT SET CORTEX_ENABLED_CROSS_REGION = 'ANY_REGION';
```

Show lessSee more

Scroll to top

For more information about configuring Cortex cross-region inference, see [Cross-region inference](../cross-region-inference).

## Connect semantic views using Cortex Analyst (structured data)

Snowflake Intelligence supports semantic views, which are a type of structured data with instructions that tell the agent how to query or interpret the data. Cortex Agents use Cortex Analyst to retrieve structured data from semantic views by converting natural language requests into SQL queries. Agents can route across multiple semantic views to provide the response.

Each semantic view should cover a similar set of tables. You can set data-specific defaults, such as always adding a date filter for the past three months if not specified or always excluding internal accounts.

You can connect a semantic view to an agent by specifying the semantic view as part of the tool resources. The following example shows how to connect a semantic view to an agent and how to specify the Cortex Analyst tool to retrieve structured data from the semantic view:

CopyExpand

```
tools:
  - tool_spec:
      type: "cortex_analyst_text_to_sql"
      name: "<your cortex analyst tool name>"
      description: "<clear and specific tool description>"

tool_resources:
  <your cortex analyst tool name>:
    semantic_view: "<db>.<schema>.<semantic_view>"
```

Show lessSee more

Scroll to top

### Best practices for semantic views

Semantic views power how Snowflake Intelligence understands and queries your data. A well-designed semantic view improves accuracy, reduces latency, and builds user trust. The following best practices are designed to help you create a semantic view that is accurate and efficient:

**Start small and focused:** Begin with 5-10 tables in a single business domain. Organize by use case (Sales Performance, Customer Support Metrics) rather than by data structure. Scale after you validate accuracy.

**Write clear descriptions:** Descriptions are the most important element. Every table and column should have a business-friendly description that explains what the data represents, not just its name. Include context like calculation logic, business definitions, and any legacy terminology.

**Add verified queries:** These are examples of questions paired with validated SQL. They improve accuracy on similar questions, reduce latency, and help the system learn your business patterns. Start with 10 to 20 queries that cover your most common questions, and add more based on actual usage.

**Define metrics and filters:** Pre-define reusable calculations (like total revenue or average order value) and common conditions (like active customers or current fiscal year). These can significantly improve consistency.

**Use custom instructions for business logic:** Add SQL generation instructions for data quirks, fiscal year definitions, default filters, or domain-specific rules. Be specific: “If no date filter is provided, default to last 12 months” is better than “filter by date.”

**Enable Cortex Search for text matching:** For high-cardinality text columns like product names, customer names, or company names, Cortex Search enables fuzzy matching when user input does not exactly match your data.

**Test and iterate:** Create an evaluation set of representative questions, measure accuracy, and refine based on real usage patterns. Review suggestions regularly to add verified queries and improve descriptions over time.

For more information about best practices for creating semantic views, see [Best Practices for Semantic Views in Cortex Analyst](https://www.snowflake.com/en/developers/guides/best-practices-semantic-views-cortex-analyst/).

## Connect Cortex Search (unstructured data)

To process unstructured data, you can connect a Cortex Search tool to an agent by specifying the Cortex Search tool in the YAML specification as part of the tool resources. Cortex Search services retrieve documents and records from unstructured data sources using semantic search. The two primary use cases for Cortex Search are retrieval augmented generation (RAG) and enterprise search. For information about creating a Cortex Search service, see [Cortex Search](../cortex-search/cortex-search-overview). You can also use a Cortex Knowledge Extension (CKE) that is shared with you.

When you connect a Cortex Search tool to an agent, it is especially important to include the following information about the parameters and their expected values:

* Type and format (include examples)
* Whether required or optional (with default values)
* Valid values or constraints (enums, ranges, formats)
* Relationship to other parameters (dependencies, conflicts)
* How to obtain the value (especially for IDs)

The following example shows how to connect a Cortex Search tool to an agent and how to specify the Cortex Search tool in the YAML specification:

CopyExpand

```
tools:
  - tool_spec:
      type: "cortex_search"
      name: "<your cortex search tool name>"
      description: "<clear and specific tool description>"

tool_resources:
  <your cortex search tool name>:
    name: "<db>.<schema>.<search_service_name>"
    max_results: "5"
    filter:
      "@eq":
        region: "North America"
    title_column: "<title_name>"
    id_column: "<column_name>"
```

Show lessSee more

Scroll to top

## Add custom tools

Snowflake Intelligence supports custom tools, which are user-defined functions or stored procedures that can be used to implement custom business logic. You can connect a custom tool to an agent by specifying the custom tool in the YAML specification as part of the tool resources.

The following example shows how to connect a custom tool to an agent and how to specify the custom tool in the YAML specification:

CopyExpand

```
tools:
  - tool_spec:
      type: "custom_tool"
      name: "<your custom tool name>"
      description: "<clear and specific tool description>"

tool_resources:
  <your custom tool name>:
    user-defined-function-argument: "argument1"
```

Show lessSee more

Scroll to top

## Create an agent

* Combine all of the tools and components to create an agent using SQL:

  > CopyExpand
  >
  > ```
  > CREATE OR REPLACE AGENT <agent_name>
  >     COMMENT = 'agent level comment'
  >     PROFILE = '{"display_name": "My Business Assistant", "avatar":  "business-icon.png", "color": "blue"}'
  >     FROM SPECIFICATION
  >     $$
  >     models:
  >     orchestration: claude-4-sonnet
  >
  >     orchestration:
  >     budget:
  >         seconds: 30
  >         tokens: 16000
  >
  >     instructions:
  >     response: "You will respond in a friendly but concise manner"
  >     orchestration: "For any revenue question, use Analyst; for policy questions, use Search"
  >     system: "You are a friendly agent that helps with business questions"
  >     sample_questions:
  >         - question: "What was our revenue last quarter?"
  >         answer: "I'll analyze the revenue data using our financial database."
  >
  >     tools:
  >     - tool_spec:
  >         type: "cortex_analyst_text_to_sql"
  >         name: "<your cortex analyst tool name>"
  >         description: "<clear and specific tool description>"
  >     - tool_spec:
  >         type: "cortex_search"
  >         name: "<your cortex search tool name>"
  >         description: "<clear and specific tool description>"
  >     - tool_spec:
  >         type: "data_to_chart"
  >         name: "data_to_chart"
  >         description: "Generates visualizations from data"
  >
  >     tool_resources:
  >     <your cortex analyst tool name>:
  >         semantic_view: "<db>.<schema>.<semantic_view>"
  >     <your cortex search tool name>:
  >         name: "<db>.<schema>.<search_service_name>"
  >         max_results: "5"
  >         filter:
  >         "@eq":
  >             region: "North America"
  >         title_column: "<title_name>"
  >         id_column: "<column_name>"
  >     $$;
  > ```
  >
  > Show lessSee more
  >
  > Scroll to top

## Modifying an existing agent

For instructions on modifying the configuration for an existing agent, including adding tools and updating other details, see [Add tools](../cortex-agents-manage.html#label-snowflake-agents-modify-agents).
