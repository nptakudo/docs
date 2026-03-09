---
title: "Supported dbt project source file locations"
url: "https://docs.snowflake.com/en/user-guide/data-engineering/dbt-projects-on-snowflake-sources"
---

# Supported dbt project source file locations

dbt project source files can be in any one of the following locations:

> * **A Git repository stage**, for example:
>
>   `'@my_db.my_schema.my_git_repository_stage/branches/my_branch/path/to/dbt_project_or_projects_parent'`
>
>   For more information about creating a Git repository object in Snowflake that connects a Git repository to a workspace for dbt Projects on Snowflake, see [Create a workspace connected to your Git repository](../tutorials/dbt-projects-on-snowflake-getting-started-tutorial.html#label-dbt-create-git-integrated-workspace). For more information about creating and managing a Git repository object and stage without using a workspace, see [Using a Git repository in Snowflake](../../developer-guide/git/git-overview) and [CREATE GIT REPOSITORY](../../sql-reference/sql/create-git-repository).
> * **An existing dbt project stage**, for example:
>
>   `'snow://dbt/my_db.my_schema.my_existing_dbt_project_object/versions/last'`
>
>   The version specifier is required and can be `last` (as shown in the previous example), `first`, or the specifier for any existing version in the form `version$<num>`. For more information, see [Versions for dbt project objects and files](dbt-projects-on-snowflake-versions).
> * **An internal named stage**, for example:
>
>   `'@my_db.my_schema.my_internal_named_stage/path/to/dbt_projects_or_projects_parent'`
>
>   Internal user stages and table stages aren’t supported.
> * **A workspace for dbt on Snowflake**, for example:
>
>   `'snow://workspace/user$.public."my_workspace_name"/versions/live/path/to/dbt_projects_or_projects_parent'`
>
>   We recommend enclosing the workspace name in double quotes because workspace names are case-sensitive and can contain special characters.
>
>   The version specifier is required and can be `last`, `first`, `live`, or the specifier for any existing version in the form `version$<num>`. For more information, see [Versions for dbt project objects and files](dbt-projects-on-snowflake-versions).
