---
title: "Semantic View Autopilot"
url: "https://docs.snowflake.com/en/user-guide/views-semantic/autopilot"
---

# Semantic View Autopilot

In Snowsight, you can create and manage semantic views to define logical tables over your data in Cortex Analyst. Semantic views abstract the physical tables and provide a business-friendly layer over your data. You can use the semantic views with Cortex Analyst to answer business questions and perform data analysis. You can create a semantic view manually or use the Semantic View Autopilot, an AI-assisted generator, to create a semantic view.

Note

You can use the instructions in this section to also create a semantic model, but we recommend using semantic views instead. Semantic views provide the following features:

* Semantic views support advanced features such as Derived Metrics.
* Semantic views support access modification. They’re public by default, but you can make them private.
* Semantic views are schema objects that integrate with Snowflake’s privilege system, sharing mechanisms, and metadata catalog. Semantic models are YAML files stored in a stage and lack these native database integrations.

The generator uses the following inputs to build your view:

* Query History: Surfaces historical SQL queries to identify common usage patterns, relationships, and verified query suggestions.
* Table Metadata: Extracts descriptions, primary/unique keys, and cardinality to determine relationships.
* Context (Highly Recommended): Uses example SQL queries or Tableau files you provide to validate relationships and extract relevant business logic.

## Prerequisites

To create a semantic view, you must use a role with the following privileges:

* CREATE SEMANTIC VIEW on the schema where you are creating the view
* USAGE on the database and schema
* SELECT on the tables and views used in the semantic view

You can export a model from Tableau and use it to automatically generate a semantic view. In addition to the preceding prerequisites, the Tableau ingestion feature requires:

* A stage where you have write permissions.
* If your Tableau file contains Custom SQL, you must also have the CREATE VIEW privilege on the schema because the SQL is parsed into a regular Snowflake view.

## Options for providing context

While providing context is optional, it’s extremely useful in creating a high-quality semantic view. Without it, the model only uses the database schema information, which might lack business nuance. We support the following options for providing context:

### Option 1: Upload Tableau file

Semantic View Autopilot supports using a file from Tableau to automatically generate a semantic model. This lets you migrate your existing business logic and metadata directly into Snowflake.

You can either use Tableau Desktop or Tableau Online to provide the file to Semantic View Autopilot. Semantic View Autopilot supports the following file formats:

* `TWB`
* `TWBX`
* `TDS`

The file must meet the following constraints:

> * File Size: Must be under 50 MB.
> * No Published Datasources: Files containing published datasources are not currently supported.
> * No Large Extracts: If using a .twbx file, ensure it does not contain a large extract. If using a .twb file, ensure it does not contain large filters or parameters.
> * LOD Calculations: Level of Detail (LOD) calculations are not supported.

You can get the `TWB` or `TWBX` file from Tableau Desktop. If you can’t find it, you can go to `File | Save As` and choose to save as a `TWB`.

For information about getting a view or workbook from Tableau Online, see [Download Views and Workbooks](https://help.tableau.com/current/pro/desktop/en-us/export.htm).

After you provide the Tableau file to Semantic View Autopilot, autopilot parses it to extract the following metadata:

> * Tables and Columns
> * Relationships between tables
> * Tableau calculated fields
> * Parameters and Filters
> * Custom SQL (parsed and turned into a regular Snowflake view)

### Option 2: Provide SQL queries

You can add example natural language questions and their corresponding SQL queries. This helps the model learn your specific business logic and create relationships.

Snowflake uses these queries to pre-select tables and columns in subsequent steps, and will also auto-add these queries as “verified queries” in the semantic model. Additionally, if valid relationships can be inferred, these will get added to the semantic view.

## Create a semantic view

To create a semantic view, first navigate to the generator:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » Cortex Analyst.
3. At the top, select Create new.
4. Select Create new Semantic View.

After you’ve navigated to the generator, you can define the basic information for the semantic view:

1. Select the Location (Database and Schema) to store the view.
2. Enter a Name for the semantic view.
3. Enter a Description. Use clear business terminology to help the AI understand the view’s purpose.
4. Select Next.

To provide context and data as a Tableau file, do the following:

1. Select Tableau Files to upload a Tableau .twb, .tds, or .twbx file.
2. Select the Tableau file to upload.
3. Select Next.

You’ve now successfully provided the context and data for the semantic view as a Tableau file.

To provide context and data as SQL queries, do the following:

1. Select SQL Queries to manually add gold standard example SQL queries.
2. Enter the SQL query.
3. Select Next.
4. Review the tables and columns selected from your tables.
5. Choose the specific columns to include.
6. Configure the AI Options:

> * Sample Values: Select whether to add sample values. This significantly improves Cortex Analyst’s accuracy by helping it recognize specific data values, such as specific region names.
> * AI-Generated Descriptions: Select whether to auto-generate descriptions for tables and columns based on their names and content. This is also a feature that significantly improves accuracy.

To create the semantic view, do the following:

1. Select Create and save.
2. Select Save and run.

It might take a few minutes to generate the semantic view. You can view the progress on the screen.

## Best practices for creating semantic views

When you’re creating a semantic view, follow these tips to ensure high precision.

* Think from the end-user’s perspective. Use names and synonyms that match the vocabulary your business users actually use (for example, “Revenue” instead of AMT\_TOT).
* Start simple. Start with a small, focused scope. For example, Sales Analytics with 3-5 tables and expand gradually. This ensures higher accuracy than a massive, “do-it-all” model.
* Review generated content. Always review AI-generated descriptions and relationships. Ensure they align with your actual business logic.
* Capture complex logic. Use Metrics and Verified Queries to handle complex calculations so users don’t have to rely on the LLM deducing them from raw columns.
* Test and iterate. After creation, test the view with real business questions in Cortex Analyst. If an answer is wrong, add a Verified Query or update a Description to fix it.
