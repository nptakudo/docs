---
title: "Improve literal search to enhance Cortex Analyst responses"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-analyst/cortex-analyst-search-integration"
---

# Improve literal search to enhance Cortex Analyst responses

This topic describes ways to improve literal string searches to help Cortex Analyst generate more accurate
SQL queries. Writing the correct SQL query to answer a question sometimes requires knowing exact literal values to filter on.
Since those values can’t always be extracted directly from the question, a search of some kind may be needed.

For example, if a user asks a question such as:

CopyExpand

```
What was my overall sales of iced tea in Q1?
```

Show lessSee more

Scroll to top

You might try the following query:

CopyExpand

```
SELECT DISTINCT name FROM product WHERE name LIKE '%iced%tea%'
```

Show lessSee more

Scroll to top

If you’ve ever gone through this process yourself, you’ll know that this
isn’t a perfect solution. For example, this query won’t show you any products named “Ice Tea”, but it will show you some “spiced tea”.

Cortex Analyst offers two solutions to help improve literal usage:

* Semantic search over the provided sample values in your [semantic model](../../views-semantic/sql).
* Semantic search using [Cortex Search Services](../cortex-search/cortex-search-overview).

This is where integrating with Cortex Search can help. [Cortex Search](../cortex-search/cortex-search-overview)
is a feature that enables low-latency, high-quality “fuzzy” search over text data. You can create a Cortex Search service to do a semantic
search over the underlying database column to find any literal values needed for Cortex Analyst to use in the SQL query that answers the
user’s question.

## Semantic search over sample values

For dimensions with relatively low-cardinality (about 1 - 10 distinct values), using a sample value search by specifying enough sample
values to show the structure of the response for the dimension is recommended. This solution requires no
additional storage besides the minimal increase to the semantic model size.

Before Cortex Analyst generates a SQL query for your question, it does a semantic similarity search between your question and the provided
sample values to identify any appropriate literal values that may be needed to write your query. Note that the semantic similarity search
may retrieve more relevant literals than the fuzzy string matching query approach mentioned above.

Only a fixed-sized set of retrieved sample values will be presented to the LLM as literals that may be needed to write the SQL query.
That means adding more sample values does not put you at risk of exceeding the LLM’s context window.

## Semantic search using Cortex Search Service

For dimensions with higher cardinality (more than 10 distinct values) or dimensions whose values change frequently, you can use a
Cortex Search Service to search through the literals. This solution reduces data duplication and keeps your semantic model concise.

Cortex Search Services do come with additional storage and compute costs. For details, see [Cost considerations](../cortex-search/cortex-search-overview.html#label-cortex-search-cost-considerations).

Note

In this preview, only a single Cortex Search Service per logical dimension is supported.

There are two options for creating a Cortex Search Service for a logical dimension in your Cortex Analyst semantic model:

* Use the Cortex Analyst UI to create a Cortex Search Service. This is the recommended approach, because it is simpler
  and less error-prone than manual setup.
* Create a Cortex Search Service manually with SQL code. This approach is more flexible but requires you to write code.

### Option 1: Use the Cortex Analyst UI

You can create a Cortex Search Service in Snowsight using the Cortex Analyst semantic model creation UI. This approach
requires no writing or editing of SQL or YAML, and is suitable for most uses.

Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in). in the navigation menu, select AI & ML » Cortex Analyst » Create new model. Follow the model creation
flow to create the Cortex Analyst semantic model. The screen for setting up Cortex Search Services is at the end of this flow.

When defining dimensions in the UI, select columns that contain text values you want to improve literal matching for.
The wizard automatically selects high cardinality columns for you, but you can choose other columns. Next, the UI lets
you choose settings for your new service, then creates the service automatically when you complete the flow.

The service is provisioned in database and schema that you selected. Once created, the service is automatically linked to your
semantic model. (The wizard also generates the YAML that links the service.)

### Option 2: Create a Cortex Search Service manually

The following steps show how to manually set up a Cortex Search Service for a logical dimension in your Cortex Analyst semantic model:

1. Create Cortex Search Service

   > CopyExpand
   >
   > ```
   > CREATE OR REPLACE CORTEX SEARCH SERVICE my_logical_dimension_search_service
   >   ON my_dimension
   >   WAREHOUSE = xsmall
   >   TARGET_LAG = '1 hour'
   >   AS (
   >       SELECT DISTINCT my_dimension FROM my_logical_dimension_landing_table
   >   );`
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
2. Include the Cortex Search service in your semantic model using the following yaml snippet:

   > CopyExpand
   >
   > ```
   > tables:
   >
   >   - name: my_table
   >
   >     base_table:
   >       database: my_database
   >       schema: my_schema
   >       table: my_table
   >
   >     dimensions:
   >       - name: my_dimension
   >         expr: my_column
   >         cortex_search_service:
   >           service: my_logical_dimension_search_service
   >           literal_column: my_column     # optional
   >           database: my_search_database  # optional
   >           schema: my_search_schema      # optional
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
   >
   > The following fields are optional under `cortex_search_service`:
   >
   > * `literal_column`: Defaults to the search index.
   > * `database`: Defaults to the database of the specified base table.
   > * `schema`: Defaults to the schema of the specified base table.
