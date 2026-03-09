---
title: "Limitations, requirements, and considerations for dbt Projects on Snowflake"
url: "https://docs.snowflake.com/en/user-guide/data-engineering/dbt-projects-on-snowflake-limitations"
---

# Limitations, requirements, and considerations for dbt Projects on Snowflake

Before you use dbt Projects on Snowflake, review the requirements, considerations, and limitations in this topic.

* [Limitations, requirements, and considerations for dbt project configurations](#label-dbt-requirements-projects)
* [Limitations, requirements, and considerations for stored procedures](#label-dbt-requirements-stored-procedures)
* [Limitations, requirements, and considerations for using workspaces with dbt projects](dbt-projects-on-snowflake-using-workspaces.html#label-dbt-requirements-workspaces)

  + [Personal database requirement](dbt-projects-on-snowflake-using-workspaces.html#label-dbt-requirements-workspaces-personal-database)
  + [Git repositories](dbt-projects-on-snowflake-using-workspaces.html#label-dbt-requirements-workspaces-git)
* [Limitations, requirements, and considerations for dbt dependencies](dbt-projects-on-snowflake-dependencies.html#label-dbt-requirements-deps)
* [Limitations, requirements, and considerations for telemetry, logging, and tracing](#label-dbt-requirements-telemetry)

## Limitations, requirements, and considerations for dbt project configurations

The following requirements, considerations, and limitations apply to dbt project configurations that are supported by dbt Projects on Snowflake:

> * Only dbt Core projects are supported. dbt Cloud projects aren’t supported. When you migrate an existing dbt Core project to Snowflake, it
>   must be compatible with [supported Snowflake versions](dbt-projects-on-snowflake-versions).
> * Each dbt project folder in your Snowflake workspace must contain a `profiles.yml` file that specifies a target `warehouse`,
>   `database`, `schema`, and `role` in Snowflake for the project. The `type` must be set to `snowflake`. dbt
>   requires an `account` and `user`, but these can be left with an empty or arbitrary string because the dbt project runs in
>   Snowflake under the current account and user context.
> * A dbt project in a workspace can’t have more than 20,000 files in its folder structure. This limit includes all files in the dbt project
>   directory and subdirectories, including the `target/dbt_packages/logs` directories, which is where log files are saved when a dbt
>   project runs from within the workspace.
> * Environment variables (for example, `{{ env_var ('MY_ENV_VAR') }}`) aren’t supported when running a dbt project object. As an
>   alternative, use project variables (for example, `--vars`). For more information, see [Project variables](https://docs.getdbt.com/docs/build/project-variables).
> * Serverless tasks can’t be used to run dbt projects. When you create a task that executes the EXECUTE DBT PROJECT command, you must
>   specify a user-managed warehouse.

## Limitations, requirements, and considerations for stored procedures

When you use a stored procedure to call EXECUTE DBT PROJECT, use a caller’s rights stored procedure. For more information, see
[CREATE PROCEDURE](../../sql-reference/sql/create-procedure) and [Creating a stored procedure](../../developer-guide/stored-procedure/stored-procedures-creating).

## Limitations, requirements, and considerations for telemetry, logging, and tracing

The following requirements, considerations, and limitations apply to telemetry, logging, and tracing for dbt on Snowflake:

> * Workspaces for dbt Projects on Snowflake don’t stream stdout dynamically, and stdout is only viewable upon command completion.
> * Viewing logs and tracing requires that you set the LOG\_LEVEL and TRACE\_LEVEL on the dbt project object. For more information, see [Access control for dbt projects on Snowflake](dbt-projects-on-snowflake-access-control) and [Monitor dbt Projects on Snowflake](dbt-projects-on-snowflake-monitoring-observability).
> * By default, Snowflake collects telemetry in the default SNOWFLAKE.TELEMETRY.EVENTS table. If you have a custom event table that is set as the event table for your account, telemetry data is collected there. If you use an Enterprise Edition account, you can create an event table to collect telemetry data and associate it with the database where the dbt project object is deployed. For more information, see [Event table overview](../../developer-guide/logging-tracing/event-table-setting-up).
