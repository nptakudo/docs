---
title: "Workspaces for dbt Projects on Snowflake"
url: "https://docs.snowflake.com/en/user-guide/data-engineering/dbt-projects-on-snowflake-using-workspaces"
---

# Workspaces for dbt Projects on Snowflake

Workspaces in Snowsight offer a web-based integrated development environment (IDE) for dbt projects that can connect and sync to a Git repository. Each workspace for dbt Projects on Snowflake can represent a single dbt project or multiple dbt projects, depending on how you organize your files and folders.

You can use a workspace for dbt Projects on Snowflake to visualize, test, and run dbt projects directly in Snowflake. Workspaces provide a quick way to initialize (or scaffold) a new dbt project, creating the necessary files and directories (such as `dbt_project.yml`) or create a new dbt project from an existing git repo. You can also connect the workspace to a *dbt project object* in Snowflake, so you can create and update objects from within the workspace.

In addition to supporting dbt projects, Workspaces provide a unified editor for you to create, organize, and manage code across multiple file types and projects within Snowflake. For more information, see [Workspaces](../ui-snowsight/workspaces).

## Limitations, requirements, and considerations for using workspaces with dbt projects

The following requirements, considerations, and limitations apply to workspaces for dbt Projects on Snowflake:

* Each dbt project folder in your Snowflake workspace must contain a `profiles.yml` file that specifies a target `warehouse`, `database`, `schema`, and `role` in Snowflake for the project. The `type` must be set to `snowflake`. dbt requires an `account` and `user`, but unlike dbt Core, these can be removed or left with an empty or arbitrary string because the dbt project runs in Snowflake under the current account and user context.
* A dbt project in a workspace can’t have more than 20,000 files in its folder structure. This limit includes all files in the dbt project directory and subdirectories, including the `target/dbt_packages/logs` directories, which is where log files are saved when a dbt project runs from within the workspace.

### Personal database requirement

Workspaces are created within a personal database and cannot be shared with other users. Personal databases must be enabled at the account level, which requires ACCOUNTADMIN privileges. For more information, see [Manage access and behavior](../ui-snowsight/workspaces.html#label-manage-access-and-behavior).

Shared workspaces are created within a specific database and schema, which grants access to multiple authenticated users. Users assigned specific roles can then contribute, edit, and modify code and files simultaneously within the environment. For more information, see [Shared workspaces](../ui-snowsight/workspaces-shared).

### Git repositories

For requirements, considerations, and limitations that apply when you connect a workspace for dbt Projects on Snowflake to a Git repository, see
[Git in Snowflake limitations](../../developer-guide/git/git-limitations).

Git repositories accessed through PrivateLink must be configured beforehand. For more information, see [Configure Snowflake for access over a public network](../../developer-guide/git/git-setting-up.html#label-git-setup-public-network).
