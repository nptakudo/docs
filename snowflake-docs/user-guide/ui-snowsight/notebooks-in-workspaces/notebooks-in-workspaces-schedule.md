---
title: "Run and schedule Notebooks in Workspaces"
url: "https://docs.snowflake.com/en/user-guide/ui-snowsight/notebooks-in-workspaces/notebooks-in-workspaces-schedule"
---

# Run and schedule Notebooks in Workspaces

Feature — Generally Available

Available to all AWS and Azure commercial regions. PrivateLink is supported.

## Scheduling notebooks in Workspaces

When deploying notebooks to production, Snowflake provides native functionality to manage deployment, orchestration, and monitoring. You develop
and iterate on notebooks interactively in Workspaces within Snowsight. Scheduling a notebook deploys its contents into a production
object called a Notebook Project Object (NPO), which encapsulates the workspace contents (for example, `.ipynb` files, Python scripts,
and SQL files). NPOs support versioned deployments and are schema-level objects (for example, `db_name.schema_name.npo_name`).

After deployment, you can orchestrate notebook execution using Snowflake Tasks (which run notebook code top-down using a consistent runtime
and dependency set) or with any third-party orchestration tool. Snowflake captures execution telemetry that you can monitor in Snowsight
or query programmatically through an event table. For more information, see [Observability and logging for Notebooks in Workspaces](notebooks-in-workspaces-observability-logging).

### Notebook Project Objects (NPOs)

An NPO is a schema-level object that acts as a production-ready “unit” in your pipeline. A Notebook project is linked to a workspace or a stage, and all
files from the workspace are copied over. NPOs are executed in a non-interactive way and can be embedded in a task for scheduling.

* **Placement:** NPOs exist within a specific schema inside a database (`database_name.schema_name.npo_name`).
* **Encapsulation:** When you schedule a notebook, the NPO captures the entire Workspace directory to ensure all dependencies are available during execution.
* **Execution:** You execute an NPO by specifying a main `.ipynb` file (for example, using the `MAIN_FILE` parameter). The main notebook can call additional notebooks using [%run](notebooks-in-workspaces-edit-run.html#label-nb-in-ws-edit-run-jupyter-magics).
* **Scheduling:** You can create multiple task objects that execute the same NPO, allowing multiple schedules for the same notebook project object.

### Discovering NPOs

NPOs are standard database objects, so you can use metadata commands to audit or clean up scheduled tasks

| Scope | Command |
| --- | --- |
| Current context | SHOW NOTEBOOK PROJECTS; |
| Database level | SHOW NOTEBOOK PROJECTS IN DATABASE <database\_name>; |
| Schema level | SHOW NOTEBOOK PROJECTS IN SCHEMA <database\_name>.<schema\_name>; |
| Account level | SHOW NOTEBOOK PROJECTS IN ACCOUNT; |

See moreShow less

Expand

## Permissions and sharing for NPOs

To execute or manage an NPO, a role must have the following privileges:

* **Location:** USAGE or OWNERSHIP on the database and schema containing the NPO.
* **NPO access:** USAGE or OWNERSHIP on the specific NPO.
* **Compute:** USAGE and MONITOR on the warehouse, and USAGE on the compute pool (for Container Runtime).
* **Scheduling:** The account-level global EXECUTE TASK privilege is required if the NPO is triggered by a task.
* **External access integrations:** USAGE on any EAIs used by the notebook.
* **Tasks:** When the NPO is scheduled via a task, the task owner role must be granted the USAGE privilege on all required objects (such as NPOs,
  warehouses, or databases). The task owner role must also have privileges to execute the USE DATABASE and USE SCHEMA commands if
  the notebook sets its execution context programmatically.

Note

NPOs use caller’s rights, where the caller is the user (not the role). When you run [EXECUTE NOTEBOOK PROJECT](../../../sql-reference/sql/execute-notebook-project) directly in
Snowsight, the execution uses the calling user’s identity rather than the active role in the Snowsight session.
The notebook runs in its own dedicated session (separate from the Snowsight session), with the user’s default role as the primary
role and all secondary roles activated. This means the notebook can execute with all privileges granted to the user’s roles.

## Using an NPO to schedule a notebook

Currently there are two supported scenarios for deploying and scheduling notebooks. In both scenarios, notebooks must be packaged in the NPO.
[Scenario A](notebooks-in-workspaces-workflow-scenarios.html#label-nb-in-ws-schedule-scenario-a) is scheduling notebooks from a private workspace. [Scenario B](notebooks-in-workspaces-workflow-scenarios.html#label-nb-in-ws-schedule-scenario-b)
is integrating GitHub Actions (or another CI/CD system) to automate the creation of NPOs from an [internal or temporary stage](../../../sql-reference/sql/create-stage),
manage their lifecycle through versioned updates, and orchestrate their execution using Snowflake Tasks.

| Scenario | Workspace Type | Scheduling Method |
| --- | --- | --- |
| A: Individual Development | Private | Supported. Develop in your private workspace. Create [Notebook Project Objects](#label-nb-in-ws-schedule-npo) (NPO) and schedule tasks. |
| B: Production (CI/CD) | Git-integrated | Notebook files are deployed to an internal or temporary stage from GitHub using GitHub Actions (or other CI/CD tools) and an NPO is created/updated from that stage. The Task is executed on the NPO. |

See moreShow less

Expand

For detailed workflows for each scenario, see [Scheduling workflows by scenario](notebooks-in-workspaces-workflow-scenarios).

## View scheduled notebook runs

You can view scheduled tasks in three places:

**From the notebook**

To view or interact with scheduled runs, you must use a role with access to the database and schema where the schedule and project object were created.

1. In the navigation menu, select Projects » Workspaces.
2. Open a scheduled notebook.
3. At the top of the notebook editor, select Scheduled runs [![Scheduled runs icon](../../../_images/nb-scheduled-runs.png)](../../../_images/nb-scheduled-runs.png). A popover displays the following information:

> * All scheduled runs for this notebook.
> * The next scheduled run time.
> * Status of past runs. Hover over a status indicator to see details such as Query ID, last run time, duration, and status.

**From the Actions menu**

* **Open Run History:** Opens the notebook’s project object showing all past runs, including status, duration, results, source file, logs, and metrics.
  Selecting a run’s result opens the executed notebook with its output. For more information, see [Observability and logging for Notebooks in Workspaces](notebooks-in-workspaces-observability-logging).

**From Database Explorer**

To view run history for any scheduled notebook (including those deployed via CI/CD):

1. In the navigation menu, select Catalog » Database Explorer.
2. Select the database and schema that contain the Notebook Project Object (NPO).
3. Select the NPO.
4. Select Run history.
5. Select a run to view the notebook output from that execution, along with logs and metrics (when available). For more information,
   see [Observability and logging for Notebooks in Workspaces](notebooks-in-workspaces-observability-logging).

Note

To view run history for notebook runs triggered by Airflow, sign in to Snowsight using the same user that runs Airflow.

## Manage scheduled tasks

From the Scheduled runs popover, you can manage your scheduled tasks by selecting the ellipsis (more actions) [![sf-options-button](../../../_images/horizontal-options.png)](../../../_images/horizontal-options.png) next to a scheduled task:

* **Run now:** Triggers an immediate execution of the scheduled task.
* **Pause schedule:** Temporarily stops the schedule from running automatically. The task remains configured but won’t execute until resumed.
* **Delete:** Removes the scheduled task permanently. You can create a new schedule with different settings (such as a different role or database
  location) after deleting the existing schedule.

## Deploy updates to scheduled notebook tasks

After editing a notebook, you must deploy your changes before scheduled runs use the updated version. Deployment ensures reproducibility and prevents
scheduled tasks from running code that differs from what was last deployed. If this is the notebook’s first task and a notebook has changes that
require deployment, the Schedule (calendar) icon displays a clock indicator. If a schedule already exists, the icon is a calendar with a clock.

After modifying code or cells, the icon indicates that there are undeployed changes.

* Select Deploy Changes.

  Snowflake then updates the associated notebook project object, and all scheduled tasks for that notebook will use the newly deployed version for the next run.

## Find a notebook project object (NPO) in the Object Explorer

Each scheduled notebook automatically creates an NPO that stores its deployed code, execution history, and artifacts. You
can locate these objects in the Object Explorer in Snowsight.

To locate an NPO in Snowsight, follow these steps:

1. In the navigation menu, select Catalog » Database Explorer.
2. Navigate to Database » Schema » Notebook Project Objects to view all NPOs in that schema.

Alternatively, you can:

1. Open the relevant notebook.
2. At the top of the notebook editor, select Scheduled runs [![Scheduled runs icon](../../../_images/nb-scheduled-runs.png)](../../../_images/nb-scheduled-runs.png).
3. Select Open run history to open the associated NPO.

## View the notebook’s run history

This section describes how to view execution details and troubleshoot notebook runs after a schedule has been created. If any step fails
during execution, Snowflake stops the run to prevent partial or inconsistent downstream results.

To view run history, follow these steps:

1. In the navigation menu, select Projects » Workspaces.
2. Open the notebook whose run history you want to review.
3. At the top of the notebook editor, select Scheduled runs [![Scheduled runs icon](../../../_images/nb-scheduled-runs.png)](../../../_images/nb-scheduled-runs.png).
4. Select View run history from the drop-down menu.

> Run History shows the following information for the notebook’s project object:
>
> * **Results:** View the notebook and output from past runs.
> * **Tasks:** See which tasks executed the NPO.
> * **Source file:** View the notebook file that was executed.
> * **Logs and metrics:** View execution logs and performance metrics (ensure you have enabled logging and event tables). For more information, see [Observability and logging for Notebooks in Workspaces](notebooks-in-workspaces-observability-logging).
> * **Run details:** Start and end times, run status, and error details.

## Schedule a notebook using Tasks

1. In the navigation menu, select Projects » Workspaces.
2. Run the following command in a SQL file/worksheet:

> CopyExpand
>
> ```
> -- Execute a notebook project using a task
> CREATE OR REPLACE TASK <database_name>.<schema_name>.<name>
>   WAREHOUSE = <string>
>   SCHEDULE = 'USING CRON 10 13 * * * America/Los_Angeles'
>   -- CRON format: <minute> <hour> <day_of_month> <month> <day_of_week> <timezone>
> AS
>   -- Execute a notebook stored within a notebook project.
>   EXECUTE NOTEBOOK PROJECT "<database_name>"."<schema_name>"."<project_name>"
>     MAIN_FILE = 'notebook.ipynb'  -- Path to the notebook file
>     COMPUTE_POOL = '<compute_pool_name>'
>     RUNTIME = '<runtime_version>'  -- e.g. V2.2-CPU-PY3.11
>     QUERY_WAREHOUSE = '<wh_name>'
>     ARGUMENTS = '<string>'  -- Can pass a single string parsed in the notebook code
>     REQUIREMENTS_FILE = '<path/to/requirements.txt>'  -- Pre-installs dependencies before the notebook runs
>     EXTERNAL_ACCESS_INTEGRATIONS = ('integration_name');  -- e.g. ('http_eai', 's3_eai')
> ```
>
> Show lessSee more
>
> Scroll to top

After creating this task, run the following command to activate the schedule:

> CopyExpand
>
> ```
> ALTER TASK <database_name>.<schema_name>.<task_name> RESUME;
> ```
>
> Show lessSee more
>
> Scroll to top

If a task fails because your active role lacks the required privileges, Snowsight displays the relevant error messages so you can
address missing permissions.

For syntax, parameters, and examples, see [EXECUTE NOTEBOOK PROJECT](../../../sql-reference/sql/execute-notebook-project). For information about passing parameters to scheduled notebooks, see [Running notebooks with parameters](notebooks-in-workspaces-parameters).

Note

To learn more about credit usage, idle timeout behavior, and notebook service management, see [Setting up compute](notebooks-in-workspaces-compute-setup.html#label-nb-in-ws-compute-setup)
and [Idle timeout](notebooks-in-workspaces-compute-setup.html#label-nb-in-ws-idle-timeout).
