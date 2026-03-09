---
title: "View and manage information for existing dbt Projects"
url: "https://docs.snowflake.com/en/user-guide/data-engineering/dbt-projects-on-snowflake-manage"
---

# View and manage information for existing dbt Projects

This topic covers how to explore the structure and metadata of an existing dbt project object. This includes viewing the project’s DAG,
inspecting model and source details, and running dbt projects.

## Browse the project DAG to see model lineage and dependencies

The Directed Acyclic Graph (DAG) shows how dbt models depend on each other, visualizing data lineage so you can:

* Verify where a model is built (database.schema), how it materializes, and which upsteam and downstream dependencies it has.
* Spot and improve inefficient model designs to support better performance and scalability.

To browse the project DAG and see model lineage and dependencies in Snowsight, do the following:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Projects » Workspaces.
3. [Ensure your dbt project is deployed](dbt-projects-on-snowflake-deploy).
4. From the right side of the workspace editor, select Connect » View project.
5. The Project details page displays the following:

> * A Description of your dbt project.
> * Your dbt Project definition.
> * Your Privileges
> * The Graph of your models and their relationships.
>
>   Click a model node to inspect model, source, or test details (such as compiled SQL and configuration) directly from the DAG.

## View dbt project object properties

View the metadata Snowflake stores about a dbt project object to see what it’s called, who owns it, which version is the default, and where
its files live in Snowflake’s internal `snow://dbt/...` stage.

To view the properties (such as name, owner, comment) of a specific dbt project, use the DESCRIBE DBT PROJECT command, as shown in the
following example:

CopyExpand

```
DESCRIBE DBT PROJECT my_dbt_project;
```

Show lessSee more

Scroll to top

The output shows the object’s name, owner, comment, versioning details, and external access integration. For more information, see
[DESCRIBE DBT PROJECT](../../sql-reference/sql/desc-dbt-project).

### View all dbt projects

Use SHOW DBT PROJECTS when you want to see all dbt project objects you can access, plus key metadata.

CopyExpand

```
SHOW DBT PROJECTS IN DATABASE mydb;
```

Show lessSee more

Scroll to top

The output shows each object’s database, schema, owner, comment, when it was created and last updated, versioning details, and external access
integration. For more information, see [SHOW DBT PROJECTS](../../sql-reference/sql/show-dbt-projects).

Alternatively, use the [snow dbt list](../../developer-guide/snowflake-cli/command-reference/dbt-commands/list) command. For more information, see
[Listing all available dbt project objects](../../developer-guide/snowflake-cli/data-pipelines/dbt-projects.html#label-snowcli-snow-dbt-list).
