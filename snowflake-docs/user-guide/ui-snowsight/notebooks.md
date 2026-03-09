---
title: "About Snowflake Notebooks"
url: "https://docs.snowflake.com/en/user-guide/ui-snowsight/notebooks"
---

# About Snowflake Notebooks

Snowflake Notebooks is a unified development interface in Snowsight that offers an interactive, cell-based programming environment for Python, SQL, and Markdown.
In Notebooks, you can leverage your Snowflake data to perform exploratory data analysis, develop machine learning models, and perform other data science and
data engineering workflows, all within the same interface.

* Explore and experiment with data already in Snowflake, or upload new data to Snowflake from local files, external cloud storage, or
  datasets from the Snowflake Marketplace.
* Write SQL or Python code and quickly compare results with cell-by-cell development and execution.
* Interactively visualize your data using embedded Streamlit visualizations and other libraries like Altair, Matplotlib, or seaborn.
* Integrate with Git to collaborate with effective version control. See [Sync notebooks with a Git repository](notebooks-snowgit).
* Contextualize results and make notes about different results with Markdown cells and charts.
* Run your notebook on a schedule to automate pipelines. See [Schedule notebook runs](notebooks-schedule).
* Make use of the role-based access control and other data governance functionality available in Snowflake to allow other users
  with the same role to view and collaborate on the notebook.

Note

Private Notebooks are deprecated and no longer supported. The new Snowflake Notebooks experience in [Workspaces](workspaces) offers a similar private development
environment with improved capabilities. If you’re interested in enrolling in the preview, contact your Snowflake account team for more information.

![An example notebook in the Snowsight UI](../../_images/sf-notebooks-intro.png)

## Notebook runtimes

Snowflake Notebooks offer two distinct runtimes, each designed for specific workloads: Warehouse Runtime and Container Runtime. Notebooks utilize
compute resources from either virtual warehouses (for Warehouse Runtime) or Snowpark Container Services compute pools (for Container Runtime)
to execute your code. For both runtimes, SQL and Snowpark queries are always executed on the warehouse for optimized performance.

The Warehouse Runtime offers the fastest way to start, with a familiar and generally available warehouse environment. The Container
Runtime provides a more flexible environment that can support many different types of workloads, including SQL analytics and data
engineering. You can install additional Python packages if the Container Runtime doesn’t include what you need by default. Container
runtime also comes in CPU and GPU versions that have many popular ML packages pre-installed, making them ideal for ML and deep learning
workloads.

The following table shows supported features for each type of runtime. You can use this table to help decide which runtime is the right
choice for your use case.

| Supported Features | Warehouse Runtime | Container Runtime |
| --- | --- | --- |
| Compute | Kernel runs on the notebook warehouse. | Kernel runs on a [compute pool](../../developer-guide/snowpark-container-services/working-with-compute-pool) node. |
| Environment | Python 3.9 | Python 3.10 (Preview) |
| Base image | Streamlit + Snowpark | Snowflake Container Runtime (CPU and GPU images pre-installed with Python libraries). |
| Additional Python libraries | Install using Snowflake Anaconda or from a Snowflake stage. | Install using `pip`, `conda`, or from a Snowflake stage. | If needed, specify a particular package version. |
| Editing support | Python, SQL, and Markdown cells. | Reference outputs from SQL cells in Python cells and vice versa. | Use visualization libraries like Streamlit. | Same as warehouse |
| Access | Ownership required to access and edit notebooks. | Same as warehouse |
| Supported Notebook features (still in Preview) | Git integration (Preview) | Scheduling (Preview) | Same as warehouse |

See moreShow less

Expand

For details on creating, running, and managing notebooks on Container Runtime, see [Notebooks on Container Runtime](../../developer-guide/snowflake-ml/notebooks-on-spcs).

## Explore Notebooks

The Snowflake Notebooks toolbar provides the controls used to manage the notebook and adjust cell display settings.

| Control | Description |
| --- | --- |
| <../../_images/ui-notebooks-packages.png> | Package selector: Select and install packages for use in the notebook. See [Import Python packages to use in notebooks](notebooks-import-packages.html#label-notebooks-import-libraries). |
| <../../_images/ui-notebooks-start.png> | Start: Start the Notebooks session. When the session starts, the image changes to Active. |
| <../../_images/ui-notebooks-active.png> | Active: Hover over the button to view real-time session details and aggregated resource consumption metrics (memory usage and CPU/GPU utilization metrics are displayed for Container Runtime notebooks). Select the down arrow to access options to restart or end the session. Select Active to end the current session. |
| <../../_images/ui-notebooks-run-all.png> | Run All/Stop: Run all cells or stop cell execution. See [Run cells in Snowflake Notebooks](notebooks-develop-run.html#label-notebooks-run). |
| <../../_images/ui-notebooks-scheduler.png> | Scheduler: Set a schedule to run your notebook as a task in the future. See [Schedule notebook runs](notebooks-schedule). |
| <../../_images/ui-notebooks-ellipsis.png> | Vertical ellipsis menu: Customize notebook settings, clear cell outputs, duplicate, export, or delete the notebook. |

See moreShow less

Expand

### Collapse cells in a notebook

You can collapse the code in a cell to see only the output. For example, collapse a Python cell to show only the visualizations produced
by your code, or collapse a SQL cell to show only the results table.

* To change what is visible, select Collapse results.
  :   The drop-down offers options to collapse specific parts of the cell.

      ![Collapse or expand cell.](../../_images/snowsight-ui-cell-collapse.png)
