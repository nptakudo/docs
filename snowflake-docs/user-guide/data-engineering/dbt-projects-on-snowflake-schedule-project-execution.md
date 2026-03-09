---
title: "Schedule runs of dbt Projects on Snowflake"
url: "https://docs.snowflake.com/en/user-guide/data-engineering/dbt-projects-on-snowflake-schedule-project-execution"
---

# Schedule runs of dbt Projects on Snowflake

You can use Snowflake [tasks](../tasks-intro) to schedule runs of dbt Projects on Snowflake with the [EXECUTE DBT PROJECT](../../sql-reference/sql/execute-dbt-project) command. You can use a workspace for dbt Projects on Snowflake to quickly create and schedule a user-managed task. You can also use SQL commands to create a task. If a workspace is connected to a dbt project object, from within the workspace, you can view all tasks that run the EXECUTE DBT PROJECT command for that project.

You must create a task that runs the EXECUTE DBT PROJECT command in the same database and schema as the dbt project object.

Note

Serverless tasks can’t be used to run dbt Projects. You must specify a user-managed warehouse when creating a task that executes the EXECUTE DBT PROJECT command.

## Create a task from within a workspace

When you create a schedule from within a workspace for dbt Projects on Snowflake, Snowflake creates the schedule by creating a user-managed task that is saved in the same database and schema as the dbt project object. The task runs with the privileges of the task owner, but task runs are not associated with a user.

**To create a task that schedules execution of a dbt project object from within a workspace:**

1. From the dbt project menu in the upper right of the workspace editor, under Scheduled runs, choose Create schedule.
2. In the Schedule a dbt run dialog box, do the following:

   * For Schedule name, enter a name for the task.
   * For Frequency, choose a frequency that ranges from Hourly to Monthly with an at qualifier, or choose Custom and enter a Cron expression. For more information about scheduling tasks, see [SCHEDULE = ...](../../sql-reference/sql/create-task.html#label-create-task-schedule) in the CREATE TASK command reference.
   * Under dbt properties:

     + For Operation, select the dbt command that you want to execute on a schedule. For a list of supported commands, see [Supported dbt commands and flags](dbt-projects-on-snowflake-supported-commands).
     + For Profile, select one of the profiles defined in the `profiles.yml` file of your dbt project.
     + For Additional flags, enter any additional [command-line options](https://docs.getdbt.com/reference/global-configs/about-global-configs#available-flags) for the dbt command.
3. Choose Create.

   Snowflake creates a task that runs an EXECUTE DBT PROJECT command using the parameters you specify.

## Viewing a task from within a workspace

From within workspace for dbt Projects on Snowflake, you can view all tasks in the database and schema that EXECUTE DBT PROJECT on the dbt project object that is connected to a workspace. You can choose a task to view its details in the object explorer, including the task definition, the run history of the task, and the task graph.

**To view tasks associated with a dbt project object from within a workspace:**

* From the dbt project menu, select View schedules and then choose your schedule (task) from the list.

  > The Task Details for the task opens in the object explorer. Task details, the SQL statement that comprises the task definition, and the privileges granted on the task object are shown.
  >
  > Choose the Run History tab to view the task run history, or choose the Task Graph tab to view the relationship of this task to other tasks in a [task graph](../tasks-graphs), if applicable.
  >
  > For more information, see [View tasks and task graphs in Snowsight](../ui-snowsight-tasks).

## Create a task using SQL

You can use the [CREATE TASK](../../sql-reference/sql/create-task) command to create tasks that run the EXECUTE DBT PROJECT command. Using SQL to create tasks that execute different dbt commands with different dbt CLI options provides a powerful way to orchestrate dbt deployments in Snowflake.

The following SQL example creates a task for a production dbt target that executes a dbt `run` command on a six-hour interval.

CopyExpand

```
CREATE OR ALTER TASK my_database.my_schema.run_dbt_project
  WAREHOUSE = my_warehouse
  SCHEDULE = '6 hours'
AS
  EXECUTE DBT PROJECT my_database.my_schema.my_dbt_project args='run --target prod';
```

Show lessSee more

Scroll to top

Then, the following SQL creates a task that executes the dbt `test` command after each completion of the previous `run_dbt_project` task.

CopyExpand

```
CREATE OR ALTER TASK change_this.public.test_dbt_project
        WAREHOUSE = my_warehouse
        AFTER run_dbt_project
AS
  EXECUTE DBT PROJECT my_database.my_schema.my_dbt_project args='test --target prod';
```

Show lessSee more

Scroll to top
