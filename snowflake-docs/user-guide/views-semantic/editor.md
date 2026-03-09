---
title: "Semantic View Editor"
url: "https://docs.snowflake.com/en/user-guide/views-semantic/editor"
---

# Semantic View Editor

The Semantic View Editor in Snowsight provides a visual interface for creating and editing
[Semantic Views](overview). Whether you’re refining a view created by the [Autopilot](autopilot),
building one from scratch, or editing an uploaded YAML specification, the editor helps you define business concepts,
metrics, and relationships over your data.

The Semantic View Editor allows you to:

* Define logical tables that map to your physical database tables
* Create dimensions (categorical attributes), facts (row-level data), and metrics (aggregated measures)
* Establish relationships between tables
* Add verified queries as examples for Cortex Analyst
* Provide custom instructions for query generation
* Configure synonyms and descriptions to improve discoverability

## Accessing the editor

You can access the Semantic View Editor through the data catalog or through Cortex Analyst.

### Through the data catalog

To access an existing semantic view through the data catalog:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Catalog » Database Explorer
3. Navigate to your database and schema.
4. Select Semantic Views in the object list.
5. Select the semantic view you want to edit.
6. Select the Semantic information tab to open the editor.

### Through Cortex Analyst

To access semantic views through Cortex Analyst:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » Cortex Analyst.
3. Select the Semantic Views tab.
4. Either:

   * Select an existing view to edit it
   * Select Create new to create a new semantic view

## Edit semantic view metadata

The semantic view name and description help users discover and understand the purpose of the view.

To edit the semantic view name or description:

1. In the editor, select Edit next to the semantic view name at the top of the page.
2. Update the Name or Description fields.
3. Select Save.

Tip

Write clear, detailed descriptions that explain:

* What business questions this view can answer
* What data sources it includes
* Who should use this view

Example: “Revenue analysis across products and customers, including year-over-year trends.
Use this view to analyze sales performance by region, product category, and customer segment.”

## Manage logical tables

Logical tables represent business entities (such as customers, orders, or products) and map to physical database
tables or views. Each semantic view contains one or more logical tables.

### Add a logical table

To add a logical table to your semantic view:

1. In the editor, select + Logical Table.
2. Browse and select the physical table or view from your database.
3. Select Next.
4. Choose which columns to include from the table.
5. Select Generate logical table.

The editor automatically generates dimensions and facts based on the selected columns.

### Edit a logical table

To modify an existing logical table:

1. Select Edit next to the table name (or select More options » Edit Logical Table).
2. Modify the table properties:

   * Name: The business-friendly name for this table
   * Description: Explanation of what this table represents
   * Synonyms: Alternative names (comma-separated)
   * Primary Key: Columns that uniquely identify rows
3. Select Save.

Tip

Use the Generate fields button to let AI automatically fill in descriptions and synonyms based on your
data and column names. This can significantly speed up the initial setup process.

## Managing facts, dimensions, and metrics

Within each logical table, you define the business concepts that users can query: dimensions, facts, and metrics.

### Understanding the content types

* **Dimensions**: Categorical attributes that provide context (such as, customer name, product category, or order date)
* **Facts**: Row-level quantitative data (such as, sale amount, quantity, or unit price)
* **Metrics**: Aggregated measures calculated from functions like SUM, AVG, or COUNT (such as, total revenue, average order value)

### Adding dimensions, facts, or metrics

To add a new item to a logical table:

1. Navigate to the logical table in the editor.
2. Select + next to Dimensions, Facts, or Metrics.
3. Enter the required details:

   * Name: Descriptive name for this item
   * Expression: SQL expression to calculate the value
   * Data Type: The data type of the result
4. Select Add

### Edit or remove items

To modify or delete an existing dimension, fact, or metric:

1. Select the item to open its details and edit properties.
2. Or select More options » Remove to delete the item.
3. Select Save to apply changes.

### Advanced features

**Derived Metrics**: You can create view-level metrics that combine metrics from multiple tables.
For more information, see [Defining derived metrics](sql.html#label-semantic-views-create-derived-metrics).

**Private Access Modifiers**: Mark facts or metrics as private to hide them from queries while still using them in
other calculations. For more information, see [Marking a fact or metric as private](sql.html#label-semantic-views-private).

## Managing relationships

Relationships define how logical tables join together, enabling queries that span multiple tables. Each relationship
defines which columns in one table reference columns in another table.

### Adding a relationship

To create a relationship between two logical tables:

1. In the editor, select + next to Relationships.
2. Enter a descriptive Name for the relationship (for example, “orders\_to\_customers”).
3. Select the Left Table (the table with the foreign key).
4. Select the Right Table (the table being referenced).
5. Specify the Join Columns for each table:

   * Left Column: The foreign key column(s) in the left table
   * Right Column: The primary key or unique column(s) in the right table
6. Select Add.

The relationship now appears in the Relationships list and enables Cortex Analyst to generate queries that join these tables.

Note

For semantic views, you typically don’t need to specify join types (left outer, inner) or relationship types
(one-to-one, many-to-one). These are automatically inferred from the data and primary key definitions at query time.

### Editing or removing relationships

To modify or delete a relationship:

1. Select the relationship to view its details.
2. Edit the properties as needed, or select Remove to delete it.
3. Select Save to apply changes.

## Advanced features for Cortex Analyst

To improve the accuracy and reliability of Cortex Analyst, you can add context and guidance through verified queries,
synonyms, and custom instructions.

### Verified queries

Verified queries provide example questions with their correct SQL answers. They serve two purposes:

* Help Cortex Analyst understand how to answer similar questions
* Provide suggested questions for users to get started

Adding a verified query:

1. Select + next to Verified Queries.
2. Enter a natural language Question (for example, “What are the top 10 products by revenue?”).
3. Enter the corresponding SQL Query that correctly answers the question.
4. (Optional) Check Use as onboarding question to show this as a suggestion to users.
5. Select Add.

Tip

Add verified queries for:

* Common business questions users are likely to ask
* Complex queries that require specific logic
* Edge cases or unusual calculations
* Questions that demonstrate the view’s capabilities

### Synonyms

Note

Add synonyms manually rather than auto-generating them with AI. Focus on domain-specific alternatives like internal terminology, abbreviations, or legacy names. Auto-generated synonyms often reduce semantic view quality.

Synonyms help users discover and query your data using alternative terminology. For example, users might refer to
“customers” as “clients” or “accounts.”

Adding synonyms to a table or field:

1. Navigate to the table, dimension, fact, or metric you want to add synonyms for.
2. Select Edit to open the item’s properties.
3. In the Synonyms field, enter alternative terms separated by commas.
4. Select Save.

Example synonyms:

* For a “customer\_name” dimension: “client name, account name, buyer name”
* For a “revenue” metric: “sales, income, earnings”
* For an “orders” table: “sales orders, purchases”

### Custom instructions

Custom instructions provide specific guidance to Cortex Analyst for SQL generation and question categorization.
Use custom instructions to:

* Define business rules and constraints
* Specify default behaviors
* Handle ambiguous questions
* Reject certain types of questions

Add a custom instruction by:

1. In the editor, select the Custom Instructions section.
2. Enter instructions in natural language. Examples:

   * “Always filter by active customers (status = ‘ACTIVE’) unless specified otherwise”
   * “Round all monetary values to 2 decimal places”
   * “When asked about revenue, use net\_revenue metric unless gross revenue is explicitly requested”
   * “If a question asks about users without specifying a region, ask the user to clarify which region”
3. Select Save.

For more information about custom instructions on semantic views, see [Providing custom instructions for Cortex Analyst](sql.html#label-semantic-views-custom-instructions).

## Uploading a YAML file

If you have an existing semantic view YAML specification or a legacy semantic model YAML file, you can upload it
to create a new semantic view or update an existing one.

To upload a YAML file:

1. In the navigation menu, select AI & ML » Cortex Analyst.
2. Select Create new » Upload YAML file.
3. Browse and select your YAML file.
4. Review the generated semantic view structure in the editor.
5. Select Convert and save to create the semantic view as a schema-level object.

The editor converts the YAML specification into a native Snowflake semantic view, which you can then edit using the
visual interface.

For information about the YAML specification format, see [YAML specification for semantic views](semantic-view-yaml-spec).

For information about converting a specification to a semantic view programmatically, see [Creating a semantic view from a YAML specification](sql.html#label-semantic-views-create-from-yaml).

## Sharing and granting privileges

To allow other users or roles to use your semantic view, you need to grant them appropriate privileges.

### Granting access through the editor

To quickly grant access to a semantic view:

1. In the editor, select Share (or More options » Share).
2. Select the role to grant access to.
3. Confirm the grant operation.

This grants both SELECT and REFERENCES privileges on the semantic view, which allows the role to:

* Query the semantic view
* Use the semantic view with Cortex Analyst

### Understanding privileges

Semantic views support Snowflake’s standard privilege model:

* **SELECT**: Required to query the semantic view and view its contents
* **REFERENCES**: Required to use the semantic view with Cortex Analyst and see its structure
* **OWNERSHIP**: Full control over the semantic view

For more information about granting privileges on semantic views, including future grants and more complex scenarios,
see [Granting privileges on semantic views](sql.html#label-semantic-views-privileges).

### Sharing semantic views

You can share semantic views across accounts using Snowflake’s sharing mechanisms. For more information,
see [Sharing semantic views](sharing-semantic-views).
