---
title: "Migrating legacy notebooks to Workspaces"
url: "https://docs.snowflake.com/en/user-guide/ui-snowsight/notebooks-in-workspaces/notebooks-in-workspaces-migrate"
---

# Migrating legacy notebooks to Workspaces

Feature — Generally Available

Available to all AWS and Azure commercial regions. PrivateLink is supported.

This topic describes how to move your legacy Snowflake Notebooks and dependent files to the Workspaces environment.

## Migration steps

1. In the navigation menu, select Projects » Notebooks to open your legacy notebook.
2. Navigate to the Files section to view your `.ipynb` notebooks and any dependent files.
3. Download all necessary files to your local machine.
4. In the navigation menu, select Projects » Workspaces.
5. Select a workspace.
6. Open an existing workspace or create a new one.

   Choose a private workspace for individual use or a shared workspace if the notebooks need to be accessed by multiple users. For more information,
   see [Workspaces](../workspaces).
7. Select + Add new.
8. Upload your downloaded files into the workspace.

## Key differences between legacy and new notebooks

Note

Not all legacy notebook files will run successfully and may require updates to align with the new environment. The table below outlines the
updates available in Notebooks in Workspaces.

| Area | Legacy notebooks | New notebooks |
| --- | --- | --- |
| Compute | Users must choose between Warehouse and Container Runtime. | Simplified user experience with Container Runtime only.   * Fully managed CPU/GPU infrastructure. * More efficient compute utilization (multiple notebooks can connect to the same service/node). * SQL and Snowpark code is still pushed down to a warehouse for flexibility and cost-performance. |
| File system / IDE environment | Partially supported. | Full IDE environment with:   * File explorer with subfolder support. * Split panes. * Terminal, etc. * Git-synced Workspaces allow users to push/pull, view diffs, and switch branches. * Shared Workspaces support team collaboration with version history and simple publish flows. |
| Package management | * Packages installed through the Anaconda channel. * EAIs need to be configured manually for each notebook. * Package installation from stages supported. | More flexible package management options:   * Direct upload to Workspaces or import from files in stage/Git repositories. * Easier setup for EAIs for installing from external sources. * Anaconda channel is no longer supported. |
| Support for Streamlit | Supported. | Not supported.  Use libraries such as `matplotlib`, `seaborn`, `plotly`, and `altair` for visualization. |
| Jupyter compatibility | Some Jupyter magics are supported. | Full support.  Use Jupyter magics such as `%run`, `%time`, and `%autoreload`. |

See moreShow less

Expand

If you have questions about availability timelines for specific features, ask your account representative to contact the Notebooks product team.

## Technical requirements and compatibility

Review the following constraints before running your notebooks in the new environment:

* **Python and Runtime:** Workspaces support Python 3.10 to 3.12 and Container Runtime 2.2.

  Note

  Python 3.9 and Container Runtime 2.0 are not supported in Workspaces.
* **Compute types:** Notebooks in Workspaces run on CPU or GPU compute types.
* **Visualizations:** Streamlit is not supported. For data visualization, use Matplotlib, Seaborn, Plotly, or Altair.

## Managing dependencies

Workspaces do not have integration support with the Snowflake Anaconda package repository. If your project requires packages not included in
the [pre-installed packages](../../../developer-guide/snowflake-ml/container-runtime-ml.html#label-container-runtime-for-ml-packages), you can install them using the following methods:

* **Interactive workflow:** Use `pip install` within the notebook. For more information,
  see [Managing packages and runtime](notebooks-in-workspaces-packages-runtime).
* **Automated setup:** Define your dependencies in a `requirements.txt` file. For detailed instructions, see
  [Managing packages and runtime](notebooks-in-workspaces-packages-runtime). For scheduled notebooks, specify the file using
  the `REQUIREMENTS_FILE` parameter in [EXECUTE NOTEBOOK PROJECT](../../../sql-reference/sql/execute-notebook-project).

## Scheduled tasks

If you have tasks scheduled on your legacy notebooks, they will continue to run with legacy notebooks and are not impacted.

If you want existing tasks to use new notebooks, update your tasks to reference the new Notebook Project Object (NPO). For more information,
see [Run and schedule Notebooks in Workspaces](notebooks-in-workspaces-schedule).
