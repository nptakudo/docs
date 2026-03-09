---
title: "Deploy dbt project objects"
url: "https://docs.snowflake.com/en/user-guide/data-engineering/dbt-projects-on-snowflake-deploy"
---

# Deploy dbt project objects

In dbt Projects on Snowflake, deploying a dbt project object means copying your dbt Project code into Snowflake to create the object or update it with a new
version. You do this with Snowsight, CREATE DBT PROJECT or ALTER DBT PROJECT SQL commands, or the `snow dbt deploy` command in
the Snowflake CLI.

## Deploy a dbt project object using Snowsight

Deploying a dbt project object in Snowsight takes the dbt code in your workspace and creates a new or updates an existing dbt project.

To deploy a dbt project object in Snowsight, [run the dbt deps command](dbt-projects-on-snowflake-dependencies),
then complete the following steps:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Projects » Workspaces.
3. In the Workspaces menu, select the workspace that contains your dbt project.
4. Confirm that your dbt files are in place.

   To verify that things work, run the `dbt compile`, `dbt run`, or dbt build command, as follows:

   1. Below the workspace editor, open the Output tab so that you can see stdout after you run dbt commands from the workspace.
   2. From the menu bar above the workspace editor, confirm that the correct Project and Profile are selected.
   3. From the command list, select dbt compile, `dbt run`, or dbt build, then select the execute button. This step parses
      your project.
5. From the top right of your workspace, select Connect then select one of the following:

   * Deploy dbt project to connect a new dbt project. On first deploy, this creates a schema-level dbt project object.
   * Existing dbt deployment to connect to an existing dbt project. Deploying adds a new version to the existing dbt project object
     (equivalent to `ALTER DBT PROJECT … ADD VERSION FROM 'snow://workspace/…/versions/last'`).
6. In the Deploy dbt project popup window, select the following:

   > * Under Select location, select your database and schema.
   > * Under Select or Create dbt project, select Create dbt project.
   > * Enter a name and description.
   > * Optionally, enter a default target to choose which profile will be used for compilation and subsequent runs (for example, prod). The
   >   target of a dbt project run can still be overridden with `--target` in `ARGS`.
   > * Optionally, select Run dbt deps, then select your external access integration to execute `dbt deps` automatically during
   >   deployment.
7. Select Deploy.

   The Output tab displays the command that runs on Snowflake, which is similar to the following example:

   CopyExpand

   ```
   CREATE DBT PROJECT mydb.my_dbt_projects_schema.my_dbt_project
     FROM 'snow://workspace/mydb.my_dbt_projects_schema.sales_model/versions/version$2'
     EXTERNAL_ACCESS_INTEGRATIONS = ();
   ```

   Show lessSee more

   Scroll to top

   Expand

   ```
   my_dbt_project successfully created.
   ```

   Show lessSee more

   Scroll to top

   The Connect menu now displays the name of the dbt project object that you created, with the following options:

   * Redeploy dbt project: Updates the dbt project object with the current workspace version of the project by using ALTER. This
     increments the version of the dbt project object by one. For more information, see [Versions for dbt project objects and files](dbt-projects-on-snowflake-versions).
   * Disconnect: Disconnects the workspace from the dbt project object, but doesn’t delete the dbt project object.
   * Edit project: Update the comment, default target, and external access integration for the dbt project object.
   * View project: Opens the dbt project object in the object explorer, where you can view the CREATE DBT PROJECT command for the dbt
     project object and run history for the project.
   * Create schedule: Provides options for you to create a task that runs the dbt project object on a schedule. For more information,
     see [Create a task to schedule dbt project execution](../tutorials/dbt-projects-on-snowflake-getting-started-tutorial.html#label-dbt-get-started-create-task).
   * View schedules: Opens a list of schedules (tasks) that run the dbt project object, with the option to view task details in the
     object explorer.
8. Optionally, confirm your dbt project exists by running the SHOW DBT PROJECTS command in a worksheet, for example:

   CopyExpand

   ```
   SHOW DBT PROJECTS IN DATABASE mydb;
   ```

   Show lessSee more

   Scroll to top

## Deploy a dbt project object using SQL commands

The [CREATE DBT PROJECT](../../sql-reference/sql/create-dbt-project) and [ALTER DBT PROJECT](../../sql-reference/sql/alter-dbt-project) commands copy the files specified in the FROM
clause of the statement to create and add new versions to a dbt project object, respectively.

The CREATE DBT PROJECT command creates a new object with a single initial version (for example, `VERSION$1`), as shown below.

CopyExpand

```
CREATE DBT PROJECT mydb.my_dbt_projects_schema.my_dbt_project
  FROM '@sales_db.integrations_schema.sales_dbt_git_stage/branches/main'
  DEFAULT_TARGET = 'prod'
  EXTERNAL_ACCESS_INTEGRATIONS = my_dbt_ext_access
  COMMENT = 'Generates sales data models.';
```

Show lessSee more

Scroll to top

The ALTER DBT PROJECT command creates a new version within the existing object with a unique, incremented version number (for example,
`VERSION$2`, `VERSION$3`, etc.).

CopyExpand

```
-- Update the Git repository object to fetch the latest code
ALTER GIT REPOSITORY sales_db.integrations_schema.sales_dbt_git_stage FETCH;

-- Add a new version to the dbt project object based on the updated Git repository object
ALTER DBT PROJECT mydb.my_dbt_projects_schema.my_dbt_project
  ADD VERSION
  FROM '@sales_db.integrations_schema.sales_dbt_git_stage/branches/main/sales_dbt_project';
```

Show lessSee more

Scroll to top

## Deploy a dbt project object using Snowflake CLI

The [snow dbt deploy](../../developer-guide/snowflake-cli/command-reference/dbt-commands/deploy) command uploads local files to a temporary stage and creates a new dbt project object, updates it by
making a new version, or completely recreates it. A valid dbt project must contain two files:

* `dbt_project.yml`: A standard dbt configuration file that specifies the profile to use.
* `profiles.yml`: A dbt connection profile definition referenced in `dbt_project.yml`. `profiles.yaml` must define the database, role, schema, and type.

  + By default, dbt Projects on Snowflake uses your target schema (`target.schema`) specified from your dbt environment or profile. Unlike dbt Core behavior, the target schema specified in the `profiles.yml`
    file must exist before you create your dbt Project in order for it to compile or execute successfully.

  CopyExpand

  ```
  <profile_name>:
  target: dev
  outputs:
    dev:
      database: <database_name>
      role: <role_name>
      schema: <schema_name>
      type: snowflake
  ```

  Show lessSee more

  Scroll to top

The following examples illustrate how to use the `snow dbt deploy` command:

* Deploy a dbt project object named `jaffle_shop`:

  CopyExpand

  ```
  snow dbt deploy jaffle_shop
  ```

  Show lessSee more

  Scroll to top
* Deploy a project named `jaffle_shop` from a specified directory and create or add a new version depending on whether the dbt project object already exists:

  CopyExpand

  ```
  snow dbt deploy jaffle_shop --source /path/to/dbt/directory --profiles-dir ~/.dbt/ --force
  ```

  Show lessSee more

  Scroll to top
* Deploy a project named `jaffle_shop` from a specified directory using a custom profiles directory, a specific dbt version, and enabling [external access integrations](../../developer-guide/external-network-access/creating-using-external-network-access):

  CopyExpand

  ```
  snow dbt deploy jaffle_shop --source /path/to/dbt/directory
  --profiles-dir ~/.dbt/
  --default-target prod
  --dbt-version 1.10.15
  --external-access-integration dbthub-integration
  --external-access-integration github-integration
  --force
  ```

  Show lessSee more

  Scroll to top

## Source file locations

The dbt project source files can be in any one of the following locations:

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
