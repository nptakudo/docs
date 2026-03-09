---
title: "Using Snowsight to create and manage semantic views"
url: "https://docs.snowflake.com/en/user-guide/views-semantic/ui"
---

# Using Snowsight to create and manage semantic views

In Snowsight, you can create and manage [semantic views](overview):

## Creating a semantic view

In Snowsight, you can create a semantic view by using a wizard or by uploading a
[YAML specification](semantic-view-yaml-spec).

* [Uploading a YAML specification to create a semantic view](#label-views-semantic-ui-create-upload)

Note

To create a semantic view, you must use a role with the following privileges:

* CREATE SEMANTIC VIEW on the schema where you are creating the semantic view.
* USAGE on the database and schema where you are creating the semantic view.
* SELECT on the tables and views used in the semantic view.

### Using the AI-assisted generator to create a semantic view

Use the AI-assisted generator to create a semantic view that combines semantic information from multiple sources. Instead of creating a semantic view manually with your own YAML specification, you can use the model generator within Snowsight to save time. The process of creating a semantic view requires the following information:

* A description with basic information about the view
* Context, such as example SQL queries
* The data source (at least one table or view) that you’re using
* The columns that you’re using

The AI-assisted generator handles inputs in the following ways:

* **Example SQL queries**

  + Validate the list of queries and throw out invalid queries.
  + Extract all tables and columns from the queries and present them for review before adding to the semantic view.
  + Extract relationships from the queries.
  + Add valid queries to the semantic view as verified queries.
* **Table metadata**

  + Extract all table and column descriptions.
  + Add primary and unique keys to the semantic view by analyzing metadata or counting distinct values to determine cardinality and relationship types.
* **Query history**

  + Surface historical SQL queries as suggestions to the semantic view. The AI-assisted generator identifies the most common types of queries that fit within the bounds of the selected tables and columns.
  + Find valid relationships and column types for the semantic view.
  + Cortex Analyst uses the query history accessible by the role used to create the semantic model to generate both relationships and verified query suggestions.

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » Cortex Analyst.
3. At the top of the navigation menu, select Create new » Create new Semantic View.
4. Select a location to store the semantic view after creation.
5. Enter a name for the semantic view.
6. For Description, specify information about the semantic view.
7. Select Next.
8. To provide context, add the following information:

   * For SQL Queries, provide example questions and their respective SQL queries that you want to use as part of the view.
9. For Select tables, provide the data source that you’re using to create the semantic view.

   > You must provide at least one table or view. There’s no limit on the tables or views that you can specify, but Snowflake recommends not using more than 10.
10. Select Next.
11. For Select columns, select the columns that you’re using to create the semantic view.

    You can select all the columns or specific columns. For performance reasons, Snowflake recommends not using more than 50 columns.
12. Select whether you want to add sample values from each column to the semantic view. Sample values help improve the accuracy of Cortex Analyst’s results.
13. Select whether you want to add AI-generated descriptions for tables and columns to the semantic view. The AI-generated descriptions are based on the column names and sample values.
14. Select Create and save.
    You can view the progress of the view generation, including details about the steps that the view generator is taking, on the semantic view page. The process can take a few minutes.
15. Optional: To make additional modifications, edit the view either by using Snowsight or by editing the YAML file directly.

Cortex Analyst automatically generates suggestions to improve the semantic view after creation. After the suggestions appear, which might take several minutes, you can review them and apply them to the view as needed.

### Uploading a YAML specification to create a semantic view

1. If you are planning to create the semantic view from Cortex Analyst,
   [create a stage](../data-load-local-file-system-stage-ui) for the YAML file.
2. Upload the YAML file in one of the following ways:

   * [Database object explorer](../ui-snowsight-data):

     1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
     2. In the navigation menu, select Catalog » Database Explorer.
     3. Select the database and schema where you want to create the semantic view.
     4. Select Create » Semantic View » Upload YAML file.
     5. Select the YAML file to upload.
     6. Under Select database, schema and stage, select the database, schema, and stage where you want to upload the YAML
        file.
     7. If you want the YAML file uploaded to a specific path in the stage, specify that path.
     8. Select Upload.
   * Cortex Analyst:

     1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
     2. In the navigation menu, select AI & ML » Cortex Analyst.
     3. Select Create new » Upload YAML file.
     4. Select the YAML file to upload.
     5. Select Convert and save.

## Editing a semantic view

Note

Editing a semantic view in Snowsight effectively replaces the existing view. To replace an existing semantic view, you
must use a role that has been granted the following privileges:

* CREATE SEMANTIC VIEW on the schema where you are creating the semantic view.
* USAGE on the database and schema where you are creating the semantic view.
* SELECT on the tables and views used in the semantic view.

To edit a semantic view:

1. Access the semantic view in one of the following ways:

   * [Database object explorer](../ui-snowsight-data):

     1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
     2. In the navigation menu, select Catalog » Database Explorer.
     3. Select the database and schema containing the semantic view.
     4. Select Semantic views.
     5. Select the semantic view.
     6. Select the Semantic information tab.
   * Cortex Analyst:

     1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
     2. In the navigation menu, select AI & ML » Cortex Analyst.
     3. Select the Semantic views tab.
     4. Under Select database to see semantic views, select the database and schema containing the semantic view that you
        want to edit.
     5. Select the semantic view that you want to edit.
2. Make changes to the semantic view. You can make the following types of changes:

   * **To modify the name or description of the semantic view:**

     1. Select Edit next to the name of the semantic view.
     2. Make changes to the name or description.
     3. Select Apply.
   * **To add a new logical table to the semantic view:**

     1. Select + Logical Table in the database object explorer or + in Cortex Analyst.
     2. In the Select a table step in the wizard:

        1. Select the table or view that contains the data that you want to use in your semantic view.
        2. Select Next.
     3. In the Select columns step in the wizard:

        1. Select the columns to include in the view.

           To select all columns in a table or view, select the table or view.
        2. Select Generate logical table.
   * **To make changes to the name, description, synonyms, or primary key of a logical table in the semantic view:**

     1. Select [![More options](../../_images/snowsight-worksheet-explorer-ellipsis.png)](../../_images/snowsight-worksheet-explorer-ellipsis.png) » Edit Logical Table next to the logical table name in the database object explorer
        or Edit next to the logical table name in Cortex Analyst.
     2. Make your changes to the name, description, synonyms, and primary key.

        If you have not specified the description or synonyms, you can select Generate fields to fill in these fields
        automatically.
     3. Select Save.
   * **To add a fact, dimension, or metric:**

     1. Open the form for adding the new item:

        + In the database object explorer, select [![More options](../../_images/snowsight-worksheet-explorer-ellipsis.png)](../../_images/snowsight-worksheet-explorer-ellipsis.png), and select Fact, Dimension, or Metric.
        + In Cortex Analyst, select + next to Facts, Dimensions, or Metrics.
     2. Enter information about the new fact, dimension, or metric, and select Add.
   * **To modify or remove a fact, dimension, or metric:**

     1. Select Facts, Dimensions, or Metrics to display the list of facts, dimensions or metrics.
     2. For the fact, dimension, or metric that you want to change:

        + Select Edit to modify the item.
        + Select [![More options](../../_images/snowsight-worksheet-explorer-ellipsis.png)](../../_images/snowsight-worksheet-explorer-ellipsis.png) » Remove fact, Remove dimension, or Remove metric to remove the item.
   * **To add a relationship:**

     1. Open the form for adding the new item:

        + In the database object explorer, select + Relationship.
        + In Cortex Analyst, select + next to Relationships.
     2. Enter a name for the relationship, select the tables in the relationship, and select the columns to use to join the
        tables.
     3. Select Add.
3. If you plan to use Cortex Analyst with this view, consider the following:

   * Add sample queries to the Verified Queries section. Note that this section is available only in Cortex Analyst.

     + These are example queries that help Cortex Analyst understand how to use the semantic view.
     + Add queries that represent common use cases for your data.
   * Add synonyms for your tables, facts, dimensions, or metrics.

     + These are alternative terms that users might use in queries.
     + Synonyms help Cortex Analyst correctly interpret user questions.
   * Add custom instructions.

     + These provide additional context about how the data should be interpreted.
     + Include business rules or constraints that should be considered.
4. Select Save.

## Granting the privilege to use a semantic view to another role

To grant another role the privileges to view and query a semantic view:

1. Access the semantic view in one of the following ways:

   * [Database object explorer](../ui-snowsight-data):

     1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
     2. In the navigation menu, select Catalog » Database Explorer.
     3. Select the database and schema containing the semantic view.
     4. Select Semantic views.
     5. Select the semantic view.
     6. Select [![More options](../../_images/snowsight-worksheet-explorer-ellipsis.png)](../../_images/snowsight-worksheet-explorer-ellipsis.png) » Share.
   * Cortex Analyst:

     1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
     2. In the navigation menu, select AI & ML » Cortex Analyst.
     3. Select the Semantic views tab.
     4. Select the semantic view.
     5. Select Share.
2. Select the role that should be granted the privileges to view and query the semantic view.
3. Select Done.

This grants the SELECT and REFERENCES privileges on the semantic view to the selected role.

## Querying a semantic view

If you are viewing a semantic view in the database object explorer, you can open a worksheet to construct a query for that view
by selecting [![More options](../../_images/snowsight-worksheet-explorer-ellipsis.png)](../../_images/snowsight-worksheet-explorer-ellipsis.png) » Query with SQL.

For information on how to construct the query, see [Querying semantic views](querying).

## Best practices for creating a semantic view

* **Provide clear descriptions:**

  + Use business terminology in all names and descriptions.
  + Make descriptions detailed enough for non-technical users to understand.
* **Include representative user questions:**

  + Include questions that can help the model generator better understand your intent.
  + Include variations of how questions might be asked.
* **Review generated suggestions carefully:**

  + Make sure the questions are relevant for the use case.
  + Make sure the suggested relationships match your business understanding.
* **Test with real questions:**

  + After creating your semantic view, test it with actual business questions.
  + Refine your semantic view, based on how well the model supports these questions.
* **Iterate on developing the semantic view:**

  + Start with a simple star schema.
  + Start with core tables and metrics, then expand. We suggest three tables to keep things simple.
  + Get feedback from business users, and refine your semantic view.

## Troubleshooting

* If your semantic view is not listed in the list of views, refresh the list of models (not the page itself).
* If errors occur with the relationships in the semantic view, ensure that these relationships match the actual data structure.
* If queries are slow, reduce the number of tables or columns.
* If Cortex Analyst produces unexpected results when using your semantic view, review the facts, dimensions, and metrics in the
  semantic view.
