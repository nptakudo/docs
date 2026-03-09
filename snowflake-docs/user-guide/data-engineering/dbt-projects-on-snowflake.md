---
title: "dbt Projects on Snowflake"
url: "https://docs.snowflake.com/en/user-guide/data-engineering/dbt-projects-on-snowflake"
---

# dbt Projects on Snowflake

[dbt Core](https://github.com/dbt-labs/dbt-core) is an open-source data transformation tool and framework that you can use to define, test, and deploy SQL transformations.

With dbt Projects on Snowflake, you can use familiar Snowflake features to create, edit, test, run, and manage your dbt Core projects, typically as follows:

1. **Start with a valid dbt project:** (With `dbt_project.yml`, `profile.yml`, `/models/...`.) This is stored either in a
   workspace in Snowsight or a Git repository that you’ve connected to Snowflake. Prepare a database, schema, and warehouse with a
   role that has the [necessary privileges](dbt-projects-on-snowflake-access-control).
2. **Install dependencies:** Execute the `dbt deps` command within a Snowflake workspace, local machine, or git orchestrator to
   populate the `dbt_packages` folder for your dbt Project.

   For more information, see [Understand dependencies for dbt Projects on Snowflake](dbt-projects-on-snowflake-dependencies).
3. **Deploy the DBT PROJECT object:** Create a schema-level DBT PROJECT object by copying your project files into a new version of that
   object. You can do this by using the CREATE OR REPLACE DBT PROJECT … FROM <source> command or the `snow dbt deploy` Snowflake CLI
   command.

   For more information, see [Deploy dbt project objects](dbt-projects-on-snowflake-deploy).
4. **Execute the dbt project in Snowflake:** Execute a dbt Core project within a dbt project object by using the EXECUTE DBT PROJECT command
   or the `snow dbt execute` Snowflake CLI command. Executing a dbt project involves invoking dbt Core commands to build or test models;
   this is what you schedule and orchestrate.

   For more information, see [EXECUTE DBT PROJECT](../../sql-reference/sql/execute-dbt-project).
5. **Schedule with Snowflake tasks:** Use Snowflake tasks to schedule and orchestrate dbt project runs.

   For more information, see [Schedule runs of dbt Projects on Snowflake](dbt-projects-on-snowflake-schedule-project-execution).
6. **Set up CI/CD integrations:** Use Snowflake CLI commands to integrate deployment and execution into your CI/CD workflows.

   dbt project objects support Snowflake CLI commands that you can use to create and manage dbt projects from the command line. This is
   useful for integrating dbt projects into your data engineering workflows and CI/CD pipelines. For more information, see
   [Snowflake CLI](../../developer-guide/snowflake-cli/index), [Integrating CI/CD with Snowflake CLI](../../developer-guide/snowflake-cli/cicd/integrate-ci-cd), and
   [snow dbt commands](../../developer-guide/snowflake-cli/command-reference/dbt-commands/overview).
7. **Monitor the dbt project:** Use Snowflake monitoring features to inspect, manage, and tune dbt project execution whether you execute a
   dbt project object manually or use tasks to execute dbt project objects on a schedule.

   For more information, see [Monitor dbt Projects on Snowflake](dbt-projects-on-snowflake-monitoring-observability).

## Key concepts

* **dbt project objects:** A *dbt project* is a directory that contains a `dbt_project.yml` file and a set of files that define dbt
  assets, such as models and sources. A DBT PROJECT is a schema-level object that contains versioned source files for your dbt project in
  Snowflake. You can connect a dbt project object to a workspace, or you can create and manage the object independent of a workspace. You
  can CREATE, ALTER, and DROP dbt project objects like other schema-level objects in Snowflake.

  A dbt project object is typically based on a dbt project directory that contains a `dbt-project.yml` file. This is the pattern that
  Snowflake uses when you deploy (create) a dbt project object from within a workspace.

  For more information, see [Understand dbt project objects](dbt-projects-on-snowflake-understanding-dbt-project-objects).
* **Schema customization:** dbt uses the default macro `generate_schema_name` to decide where a model is built. You can customize how
  dbt builds your models, seeds, snapshots, and test tables.

  For more information, see [Understand schema generation and customization](dbt-projects-on-snowflake-schema-customization).
* **Workspaces:** Workspaces in the Snowflake web interface are a Git-connected web IDE where you can visualize, test, run, and scaffold one
  or many dbt projects, link them to a Snowflake dbt project object to create/update it, and edit other Snowflake code in one place.

  For more information, see [Workspaces for dbt Projects on Snowflake](dbt-projects-on-snowflake-using-workspaces).
* **Versioning:** Every dbt project object is versioned; versions live under `snow://dbt/<db>.<schema>.<project>/versions/...`.

  For more information, see [Versions for dbt project objects and files](dbt-projects-on-snowflake-versions).
