---
title: "Access control for dbt projects on Snowflake"
url: "https://docs.snowflake.com/en/user-guide/data-engineering/dbt-projects-on-snowflake-access-control"
---

# Access control for dbt projects on Snowflake

The following commands demonstrate commonly granted privileges for dbt project objects.

* **To grant privileges to create a dbt project object, including deploying from within a workspace:**

  CopyExpand

  ```
  GRANT CREATE DBT PROJECT ON SCHEMA my_database.my_schema TO ROLE my_role;
  ```

  Show lessSee more

  Scroll to top
* **To grant privileges to alter or drop (delete) a dbt project object, including connecting a workspace to a dbt project object:**

  CopyExpand

  ```
  GRANT OWNERSHIP ON DBT PROJECT my_dbt_project_object TO ROLE my_role;
  ```

  Show lessSee more

  Scroll to top
* **To grant privileges to execute a dbt project object and to list or get files:**

  CopyExpand

  ```
  GRANT USAGE ON DBT PROJECT my_dbt_project_object TO ROLE my_role;
  ```

  Show lessSee more

  Scroll to top
* **To view a dbt project object in Snowsight, you must use a role that has the MONITOR privilege on that dbt project. Without this
  privilege, you can’t access the project details, run history, or monitoring information:**

  CopyExpand

  ```
  GRANT MONITOR ON DBT PROJECT my_dbt_project_object TO ROLE my_role;
  ```

  Show lessSee more

  Scroll to top

For more information, see [dbt project object privileges](../security-access-control-privileges.html#label-access-control-privs-dbt).

## Roles and privileges for dbt project deployment

Deploying a dbt project from Snowsight initially uses the role selected in the deploy dialog (that is, the role you select from Connect » Deploy dbt project). During compilation, the dbt Project uses the role specified in the target profile in `profiles.yml` file, unless the object has the DEFAULT\_TARGET attribute, which takes precedence.

Similarly, deploying a dbt project from SQL or CLI initially uses the role in the worksheet or `connection.toml`, respectively, then uses the role specified in the command. The actual compilation during deployment uses the role within the target profile in `profiles.yml`, unless the object has the DEFAULT\_TARGET attribute, which takes precedence.

## Roles and privileges for dbt project execution

When you execute a dbt project, the roles that perform execution and that materialize output when you specify the dbt `run` or `build` commands depend on the method of execution.

### Execution from SQL or CLI

The dbt command specified in EXECUTE DBT PROJECT runs with the privileges of the `role` specified in the `outputs` block of the projects `profiles.yml` file. Operations are further restricted to only those privileges granted to the Snowflake user calling EXECUTE DBT PROJECT. Both the user and the role specified must have the required privileges to use the `warehouse`, perform operations on the `database` and `schema` specified in the project’s `profiles.yml` file, and perform operations on any other Snowflake objects that the dbt model specifies.

### Execution from within Workspaces

Choosing the dbt Run or Build command for a project from within a workspace materializes target output using the `role` defined in the project’s `profiles.yml` file. Both the user and the role specified must have the required privileges to use the `warehouse`, perform operations on the `database` and `schema` that are specified in the project’s `profiles.yml` file, and perform operations on any other Snowflake objects that the dbt model specifies.

### Scheduled execution from within Workspaces

Scheduling dbt project object execution from within Workspaces creates user-managed tasks. To create a task from within Workspaces, a user must have a role with privileges described under [Access control requirements](../../sql-reference/sql/create-task.html#label-create-task-sql-access-control) in the CREATE TASK reference. Snowflake runs tasks with the privileges of the task owner, but task runs are not associated with the user. For more information, see [Tasks run by a system service](../tasks-intro.html#label-system-service).
