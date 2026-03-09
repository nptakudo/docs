---
title: "Understand dbt project objects"
url: "https://docs.snowflake.com/en/user-guide/data-engineering/dbt-projects-on-snowflake-understanding-dbt-project-objects"
---

# Understand dbt project objects

A DBT PROJECT is a schema-level object that contains versioned source files for your dbt project in Snowflake. You can connect a dbt project
object to a workspace, or you can create and manage the object independently of a workspace.

A dbt project object is typically based on a dbt project directory that contains a `dbt-project.yml` file. This is the pattern that
Snowflake uses when you [deploy](dbt-projects-on-snowflake-deploy) (create) a dbt project object from
within a workspace.

dbt project objects support role-based access control (RBAC). You can CREATE, ALTER, and DROP dbt project objects like other schema-level objects in Snowflake. You can use the [EXECUTE DBT PROJECT](../../sql-reference/sql/execute-dbt-project) command from a Snowflake warehouse to run dbt
commands like `test` and `run`. You can also use [tasks](../tasks-intro) to schedule execution of these commands.

## How dbt project objects get updated

dbt project objects don’t automatically update as you edit the workspace; you must deploy (that is, add a new version) each time you want the
object to pick up code changes.

To create a production pipeline, we recommend creating a dbt project object and [scheduling its execution with a task](dbt-projects-on-snowflake-schedule-project-execution).
Because each dbt project object version is immutable, doing so ensures nothing changes between runs unless someone explicitly adds a new
version.

To update the dbt Project’s files, you must add a new version in a workspace, for example:

CopyExpand

```
ALTER DBT PROJECT testdbt.public.my_dbt_project_object
  ADD VERSION FROM 'snow://workspace/user$.public."all_my_dbt_projects"/versions/last';
```

Show lessSee more

Scroll to top

If your dbt Project is backed by Git and you want to automate your testing and deployment, run the Snow CLI `snow dbt deploy` command
with the `--force` option, as shown in the following example:

CopyExpand

```
snow dbt deploy --source 'snow://workspace/user$.public."all_my_dbt_projects"/versions/last'  --force my_dbt_project;
```

Show lessSee more

Scroll to top

`--force` enables you to add a version; without it, it would be the equivalent of running CREATE DBT PROJECT on an already created
object, which would fail.

For more information about versioning, see [Versions for dbt project objects and files](dbt-projects-on-snowflake-versions).
