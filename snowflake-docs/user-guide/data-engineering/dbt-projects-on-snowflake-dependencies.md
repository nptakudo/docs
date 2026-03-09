---
title: "Understand dependencies for dbt Projects on Snowflake"
url: "https://docs.snowflake.com/en/user-guide/data-engineering/dbt-projects-on-snowflake-dependencies"
---

# Understand dependencies for dbt Projects on Snowflake

In dbt Projects on Snowflake, dbt dependencies are the packages that you declare in your `packages.yml` file (for example, `dbt-labs/dbt_utils` from the
[Getting started tutorial](../tutorials/dbt-projects-on-snowflake-getting-started-tutorial)). They get installed into a
`dbt_packages` folder when you run `dbt deps`, just like in dbt Core.

You must execute the `dbt deps` command within a Snowflake workspace to populate the `dbt_packages` folder for your dbt Project.
Alternatively, you can run `dbt deps` on your local machine or git orchestrator (for example, GitHub Actions) and deploy with
`snow dbt deploy`.

Once a dbt project version is created, think of it as read-only code. You don’t modify its files with `dbt deps`; you create a new
version if you need updated dependencies.

## About executing the dbt deps command

You can execute the `dbt deps` command in one of the following ways:

* **In a Snowflake Workspace:** (Recommended for dev environments.) You can execute the `dbt deps` command inside your workspace in
  Snowsight to populate `dbt_packages` before you deploy your dbt Project as a DBT PROJECT object.

  This requires external network access so Snowflake can access the repositories for the dependencies. For more information, see
  [Create an external access integration in Snowflake for dbt dependencies](#label-dbt-key-concepts-external-access-config).
* **Outside Snowflake:** (For example, in the build step of your deployment pipeline.) You can execute the `dbt deps` command on your
  local machine or in your continuous integration (CI), which downloads packages into `dbt_packages`, then deploy the whole project
  (including that folder) into Snowflake.

  This doesn’t require an external network access integration because all dependencies are already included in the dbt project.

  Because the files in a dbt project version are immutable, if you try to execute `dbt deps` against a deployed object, this would have
  no effect on the `dbt_packages` folder within the object.

## Cross dbt project dependencies

In order to reference another dbt project within your dbt project, the dbt project being referenced must be copied into the root of your dbt
project. Snowflake only supports references in the same folder. For example, `:local: ../some_other_project` isn’t supported.

Although local dependencies don’t require an external access integration, if you need a mix of local packages and remote packages (for example, from dbt Packages hub or Git), you must configure a real external access integration.

Take, for example, the following two dbt projects. You want `core_project` to include `metrics_project` locally so that everything
is self-contained when you deploy to Snowflake (no external access needed).

CopyExpand

```
/Projects
├─ core_project/
│   ├─ dbt_project.yml
│   ├─ packages.yml
│   ├─ models/
│   └─ ...
└─ metrics_project/
    ├─ dbt_project.yml
    ├─ models/
    └─ ...
```

Show lessSee more

Scroll to top

* `core_project`: This is your main project (the one that you’ll deploy).
* `metrics_project`: This is the project you want to use as a local dependency.

To reference `metrics_project` inside `core_project`, complete the following steps:

1. Inside of `core_project`, create a folder named `local_packages`. Copy `metrics_project` into this folder.

   Make sure that `metrics_project` has a different name in its `dbt_project.yml` than `core_project`. They must be unique.

   CopyExpand

   ```
   cd /Projects/core_project
   mkdir local_packages
   cp -R ../metrics_project ./local_packages/metrics_project
   ```

   Show lessSee more

   Scroll to top

   Now, your layout looks like this:

   CopyExpand

   ```
   core_project/
     ├─ dbt_project.yml
     ├─ packages.yml
     ├─ models/
     ├─ local_packages/
     │   └─ metrics_project/
     │       ├─ dbt_project.yml
     │       ├─ models/
     │       └─ ...
   ```

   Show lessSee more

   Scroll to top
2. In `core_project/packages.yml`, declare the local dependency using the relative path.

   CopyExpand

   ```
   packages:
     - local: local_packages/metrics_project
   ```

   Show lessSee more

   Scroll to top
3. From inside `core_project`, run `dbt deps`.

   dbt will now treat `metrics_project` as a package and macros from `metrics_project` are available to `core_project`.

## Run dbt deps automatically at compilation

When you deploy or update a dbt project object and give it an external access integration, Snowflake can automatically run `dbt deps`
during compilation so that dependencies are installed as part of that step. This means you no longer need to include `/dbt_packages`
when deploying projects with external dependencies.

SnowsightSQLSnowflake CLI

When you deploy your dbt project object from the workspace to a Snowflake database and schema, you can create or update an object that
you previously created.

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Projects » Workspaces.
3. In the Workspaces menu, select the workspace that contains your dbt project.
4. On the right side of the workspace editor, select Connect » Deploy dbt project.
5. In the Deploy dbt project popup window, select the following:

   * Under Select location, select your database and schema.
   * Under Select or Create dbt project, select Create dbt project.
   * Enter a name and description.
   * Optionally, enter a default target to choose which profile will be used for compilation and subsequent runs (for example, prod). The
     target of a dbt project run can still be overridden with `--target` in `ARGS`.
   * Optionally, select Run dbt deps, then select your external access integration to execute `dbt deps` automatically during
     deployment.
6. Select Deploy.

The Output tab displays the command that runs on Snowflake, which is similar to the following example:

CopyExpand

```
CREATE DBT PROJECT mydb.my_dbt_projects_schema.my_dbt_project
  FROM 'snow://workspace/mydb.my_dbt_projects_schema.sales_model/versions/version$2'
  DEFAULT_TARGET = 'prod'
  EXTERNAL_ACCESS_INTEGRATIONS = (my_dbt_ext_access);
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

To automatically run `dbt deps` during compile, run the CREATE DBT PROJECT or ALTER DBT PROJECT command with the
EXTERNAL\_ACCESS\_INTEGRATIONS parameter, as shown in the following example.

You can pass an empty array into the EXTERNAL\_ACCESS\_INTEGRATIONS parameter or you can specify one or more external access integrations,
depending on your use case. Local dependencies don’t require an external access integration, but if you need a mix of local packages and
remote packages (for example, from dbt Packages hub or Git), you must configure a real external access integration.

CopyExpand

```
-- Create a dbt project object that runs dbt deps on compile for remote packages
CREATE DBT PROJECT mydb.my_dbt_projects_schema.my_dbt_project
  FROM 'snow://workspace/mydb.my_dbt_projects_schema.sales_model/versions/version$2'
  EXTERNAL_ACCESS_INTEGRATIONS = (my_dbt_ext_access);

-- Create a dbt project object that runs dbt deps on compile for only local dependencies
CREATE DBT PROJECT mydb.my_dbt_projects_schema.my_dbt_project
  FROM 'snow://workspace/mydb.my_dbt_projects_schema.sales_model/versions/version$2'
  EXTERNAL_ACCESS_INTEGRATIONS = ();
```

Show lessSee more

Scroll to top

CopyExpand

```
-- Update the Git repository object to fetch the latest code
ALTER GIT REPOSITORY mydb.dev_schema.my_dbt_git_stage FETCH;

-- Set external access integrations
ALTER DBT PROJECT mydb.my_dbt_projects_schema.my_dbt_project
  SET EXTERNAL_ACCESS_INTEGRATIONS = ();

-- Add a new version to the dbt project object based on the updated Git repository object
-- After an external access integration is set, the next ALTER DBT PROJECT ... ADD VERSION will call dbt deps during compile
ALTER DBT PROJECT mydb.my_dbt_projects_schema.my_dbt_project
  ADD VERSION
  FROM '@mydb.dev_schema.my_dbt_git_stage/branches/main/sales_dbt_project';
```

Show lessSee more

Scroll to top

To automatically run `dbt deps` during compile, run the [snow dbt deploy](../../developer-guide/snowflake-cli/command-reference/dbt-commands/deploy)
command with either the `--external-access-integration` or `--install-local-deps` flag, as shown in the following example.

The `--install-local-deps` flag creates an object that has an empty external access integration. On a regular compile, it runs
`dbt deps` and replaces the previous state of the `dbt_packages` folder.

The `--external-access-integration` flag adds an external access integration, which takes precedence over the
`--install-local-deps` flag.

CopyExpand

```
snow dbt deploy my_dbt_project --install-local-deps;
```

Show lessSee more

Scroll to top

## Create an external access integration in Snowflake for dbt dependencies

When you run dbt commands in a workspace, dbt might need to access remote URLs to download dependencies. For example, dbt might need to
download packages from the dbt Package hub or from GitHub.

Most dbt projects specify dependencies in their `packages.yml` file. You must install these dependencies in the dbt project workspace.

You can’t update a deployed dbt project object with dependencies. To update a dbt project object with new dependencies, you must add a new
version to the object. For more information, see [How dbt project objects get updated](dbt-projects-on-snowflake-understanding-dbt-project-objects.html#label-dbt-key-concepts-dbt-project-updating).

To get dbt package from remote URLs, Snowflake needs an external access integration that relies on a network rule, as shown in the
following example:

CopyExpand

```
-- Create NETWORK RULE for external access integration

CREATE OR REPLACE NETWORK RULE my_dbt_network_rule
  MODE = EGRESS
  TYPE = HOST_PORT
  -- Minimal URL allowlist that is required for dbt deps
  VALUE_LIST = (
    'hub.getdbt.com',
    'codeload.github.com'
    );

-- Create EXTERNAL ACCESS INTEGRATION for dbt access to external dbt package locations

CREATE OR REPLACE EXTERNAL ACCESS INTEGRATION my_dbt_ext_access
  ALLOWED_NETWORK_RULES = (my_dbt_network_rule)
  ENABLED = TRUE;
```

Show lessSee more

Scroll to top

For more information about external access integrations in Snowflake, see [Creating and using an external access integration](../../developer-guide/external-network-access/creating-using-external-network-access).

## Limitations, requirements, and considerations for dbt dependencies

The following requirements, considerations, and limitations apply to dbt dependencies for dbt projects in dbt Projects on Snowflake:

* You must execute the `dbt deps` command within a Snowflake workspace to populate the `dbt_packages` folder for your dbt Project.
  Alternatively, you can run `dbt deps` on your local machine or Git orchestrator and deploy with `snow dbt deploy`.

  A dbt Project object is a versioned snapshot, so running `dbt deps` with EXECUTE DBT PROJECT or `snow dbt execute` doesn’t
  modify any files; it mainly checks that your external access is configured correctly.
* You can specify public [Git packages](https://docs.getdbt.com/docs/build/packages#git-packages) in the `packages.yml` file. As a best practice, Snowflake recommends using private Git packages
  only if they are stored securely. We don’t recommend embedding unencrypted Git tokens.
* A network rule and external access integration are required to allow Snowflake to access the repositories for the dependencies. For more
  information, see [Create an external access integration in Snowflake for dbt dependencies](#label-dbt-key-concepts-external-access-config).
* A dbt project object is a versioned snapshot of your project. Running the `deps` command on it doesn’t modify any files; it’s
  primarily used to verify that your external access configuration is correct. When a dbt project object is created with an external access
  integration, `dbt deps` is run before `dbt compile` to package all dependencies and project files.
* Snowflake only supports referencing another dbt project in the same folder. For example, `:local: ../some_other_project` isn’t
  supported. For a workaround, see [Cross dbt project dependencies](#label-dbt-key-concepts-cross-project-deps).
