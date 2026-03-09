---
title: "Scheduling workflows by scenario"
url: "https://docs.snowflake.com/en/user-guide/ui-snowsight/notebooks-in-workspaces/notebooks-in-workspaces-workflow-scenarios"
---

# Scheduling workflows by scenario

Feature — Generally Available

Available to all AWS and Azure commercial regions. PrivateLink is supported.

This topic provides detailed workflows for scheduling notebooks in two common scenarios:

* **Scenario A:** Development in a private workspace - Schedule notebooks directly from Snowsight
* **Scenario B:** Production (CI/CD) - Deploy notebooks from a Git repository using CI/CD pipelines

Note

Scheduling notebooks is not currently supported in shared workspaces.

## Scenario A: Development in a private workspace

1. In the navigation menu, select Projects » Workspaces.
2. Select + Add new » Notebook to create a new notebook, or open an existing notebook to be scheduled.

   Note

   Ensure that you have specified the execution context (database and schema) in the notebook you are scheduling. For more information,
   see [Set the execution context](notebooks-in-workspaces-edit-run.html#label-nb-in-ws-edit-run-execution-context).
3. At the top of the notebook editor, select Scheduled runs [![Scheduled runs icon](../../../_images/nb-scheduled-runs.png)](../../../_images/nb-scheduled-runs.png).

   * If this is the notebook’s first task, the [![Scheduled runs icon](../../../_images/nb-scheduled-runs.png)](../../../_images/nb-scheduled-runs.png) icon is a calendar.
   * If a schedule already exists, the [![Scheduled runs icon](../../../_images/nb-scheduled-runs.png)](../../../_images/nb-scheduled-runs.png) icon is a calendar with a clock.
4. Select Create Schedule.
5. In the Schedule a Notebook Task dialog, provide the following information:

   **Basic settings**

   * **Task name:** The unique name for the scheduled task. The default name is `{notebook-name}_task_#` but can be updated if necessary.
   * **Owner role:** The Snowflake role under which the task executes. Select a role with the required permissions to execute all operations performed by
     the scheduled notebook. This role must have permissions to:

     + Read/write the database objects the notebook uses.
     + Access warehouses, compute pools, and integrations.
     + Create/update the task and project objects.
   * **Location:** The database and schema where the task object and associated notebook project object is created. Choose a schema where your role
     has CREATE TASK and USAGE privileges. If your role has only USAGE privileges on the schema, ensure it also has the CREATE NOTEBOOK PROJECT privilege.
   * **Frequency:** How often the notebook should run. Choose from: Hourly, Daily, Weekly, Monthly, or Custom (Cron scheduling). All execution times use
     your local time zone.

   **Advanced settings (all fields are required unless otherwise specified)**

   * **Notebook project name:** A unique name for the notebook’s project container that Snowflake creates for task execution. If not edited, Snowflake provides a
     default name.
   * **Parameters (optional):** Key-value parameters are passed to the notebook at runtime and appear as command-line arguments (in `sys.argv`). Parameters
     are useful for passing dates, environment flags, thresholds, or model versions. Parameters can be passed in Snowsight as whitespace-separated values
     or in the [EXECUTE NOTEBOOK PROJECT](../../../sql-reference/sql/execute-notebook-project) command as `ARGUMENTS = 'env prod'`. For more information, see
     [Running notebooks with parameters](notebooks-in-workspaces-parameters).
   * **Runtime variant:** The runtime environment used for notebook execution. Choose from:

     + **CPU:** Uses a CPU Container Runtime environment and runs on a CPU compute pool (for example, the automatically provisioned `SYSTEM_COMPUTE_POOL_CPU`).
     + **GPU:** Uses a GPU Container Runtime environment that includes GPU-accelerated libraries and runs on a GPU compute pool (such as `SYSTEM_COMPUTE_POOL_GPU`).
     + **Python version:** The Python version used during task execution.
     + **Runtime version:** The base Container Runtime image. Choosing the correct runtime version ensures that your notebook runs consistently between
       development and scheduled execution.
   * **Compute pool:** The compute pool that executes the notebook task. Ensure that the compute pool has capacity (free nodes) at the time of
     the scheduled execution. To prevent scheduled runs from failing, we recommend that you use a dedicated compute pool to ensure no other SPCS services
     take up full capacity.
   * **Query warehouse:** The Snowflake warehouse used for all SQL queries inside the notebook.
   * **External access integrations (optional):** Defines which external access integrations (EAIs) the notebook may use. EAIs are required if
     your notebook requires external APIs, third-party services, or cloud storage outside of Snowflake’s internal stages. If no EAIs are listed, your
     selected role does not own or have privileges on any integrations.
   * **Requirements file (optional):** Pre-install Python dependencies for repeatable runs using the `REQUIREMENTS_FILE` parameter. For more
     information, see [Managing packages and runtime](notebooks-in-workspaces-packages-runtime).
6. Review the schedule preview, and select Create.

## Scenario B: Production (CI/CD)

For production environments, we recommend managing notebook code in a Git-based workspace (for details, see [Integrate workspaces with a Git repository](../workspaces-git))
or developing locally in your preferred IDE. You can use a CI/CD pipeline (such as GitHub Actions) to deploy files to a Snowflake internal or temporary stage.

For a hands-on walkthrough of this pattern, see the [Getting Started with Data Engineering using Snowflake Notebooks](https://www.snowflake.com/en/developers/guides/data-engineering-with-notebooks/)
quickstart and the accompanying [code repository](https://github.com/Snowflake-Labs/sfguide-data-engineering-with-notebooks) on GitHub.

After the files are on the stage, you can:

* Create a Notebook Project Object (NPO) sourced from that stage location.
* Schedule the NPO using a Snowflake Task for automated execution.

1. **Create a stage**

   Use [CREATE STAGE](../../../sql-reference/sql/create-stage) to create an internal or temporary stage:

   CopyExpand

   ```
   -- Ensure the landing zone exists
   CREATE STAGE IF NOT EXISTS <database_name>.<schema_name>.<stage_name>;
   ```

   Show lessSee more

   Scroll to top
2. **Load/deploy notebook file(s) to the internal or temporary stage**

   Your CI/CD pipeline should upload the `.ipynb` file(s) to a Snowflake stage. Use the [PUT](../../../sql-reference/sql/put) command to ensure that the notebook
   files are loaded into a stage readable by the Notebook Project.

   CopyExpand

   ```
   PUT file://<absolute_path_to_file>/ @<database_name>.<schema_name>.<stage_name> AUTO_COMPRESS=FALSE OVERWRITE=TRUE;
   ```

   Show lessSee more

   Scroll to top

   Example:

   CopyExpand

   ```
   PUT file://notebooks/ml_model/train.ipynb @<database_name>.<schema_name>.<stage_name> AUTO_COMPRESS=FALSE OVERWRITE=TRUE;
   ```

   Show lessSee more

   Scroll to top
3. **Create or update the Notebook Project Object (NPO)**

   Create (or update) the NPO to reference the internal or temporary stage that contains your deployed notebook files:

   CopyExpand

   ```
   CREATE NOTEBOOK PROJECT IF NOT EXISTS <database_name>.<schema_name>.<project_name>
     FROM '@<database_name>.<schema_name>.<stage_name>';
   ```

   Show lessSee more

   Scroll to top
4. **Alter the notebook project details**

   For subsequent code changes, your pipeline executes an ALTER command. This updates the project to the latest version of the code without
   having to drop and recreate the object:

   CopyExpand

   ```
   -- Update the project with the latest code from the stage
   ALTER NOTEBOOK PROJECT <database_name>.<schema_name>.<project_name>
     ADD VERSION FROM '@<database_name>.<schema_name>.<stage_name>';
   ```

   Show lessSee more

   Scroll to top
5. **Execute the notebook project (orchestrate with a task)**

   Create a task to schedule and execute the NPO. Use a Snowflake task to define the schedule and execution parameters for the NPO.

   Note

   Ensure that you specify your notebook execution context (use the database and schema of the notebook you want to schedule). For more
   information, see [Set the execution context](notebooks-in-workspaces-edit-run.html#label-nb-in-ws-edit-run-execution-context).

   CopyExpand

   ```
   -- Create or replace the task to orchestrate the notebook
   CREATE OR REPLACE TASK <database_name>.<schema_name>.<task_name>
     WAREHOUSE = '<warehouse_name>'
     SCHEDULE = 'USING CRON 0 9 * * * America/Los_Angeles'
   AS
     EXECUTE NOTEBOOK PROJECT <database_name>.<schema_name>.<project_name>
       MAIN_FILE = 'snow://workspace/<workspace_hash>/path/to/notebook.ipynb'
       COMPUTE_POOL = 'SYSTEM_COMPUTE_POOL_CPU'
       RUNTIME = 'V2.2-CPU-PY3.12'
       QUERY_WAREHOUSE = '<warehouse_name>'
       ARGUMENTS = '<db_name> <schema_name> <warehouse_name>';
   ```

   Show lessSee more

   Scroll to top

   For information about passing parameters to scheduled notebooks, see [Running notebooks with parameters](notebooks-in-workspaces-parameters).
6. **View your notebook run or execution history**

   After the task runs, you can monitor its success or failure in Snowsight to ensure the CI/CD deployment is performing as expected.
   For detailed instructions on viewing run history, see [View scheduled notebook runs](notebooks-in-workspaces-schedule.html#label-nb-in-ws-schedule-view-runs).

Snowsight supports non-interactive (headless) execution of notebooks. This allows you to trigger a programmatic run of a notebook without
opening Snowsight and without requiring a recurring schedule.

Headless execution is intended for tasks, scheduled tasks, or workflows orchestrated by tools such as Airflow, Prefect, Dagster, CI/CD pipelines, or
external systems that need to execute a notebook programmatically. For more information, see [CREATE NOTEBOOK PROJECT](../../../sql-reference/sql/create-notebook-project).

Note

To run the SQL commands in this workflow (such as `CREATE NOTEBOOK PROJECT` and `CREATE TASK`), you must execute them from a SQL
file or SQL worksheet in Workspaces, not from within a notebook cell.
