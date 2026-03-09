---
title: "Notebooks in Workspaces limitations"
url: "https://docs.snowflake.com/en/user-guide/ui-snowsight/notebooks-in-workspaces/notebooks-in-workspaces-limitations"
---

# Notebooks in Workspaces limitations

Feature — Generally Available

Available to all AWS and Azure commercial regions. PrivateLink is supported.

## Notebook services and runtime

* Notebook services are subject to an account limit of 200 active services.
* Notebooks in different workspaces cannot share a service.
* Notebooks in the same workspace connect to a shared service by default.
* Users may create multiple services within a workspace and assign notebooks to different services as needed.
* Notebook services may be restarted over the weekend for container service maintenance. After a restart, you must rerun notebooks and reinstall any
  packages to restore variables and packages. For more information, see [Service maintenance](notebooks-in-workspaces-compute-setup.html#label-nb-in-ws-service-maintenance).
* Package installation and listing behavior differs between `uv` and standard `pip`. Snowflake supports installing packages using
  `uv pip install`, and `uv pip freeze` lists only packages installed using `uv pip install`. `pip freeze` lists all packages available in the environment, including packages in the base image, packages installed with standard pip install, and packages installed with `uv pip install`.

## Using notebooks in Workspaces

* Queries in SQL cells do not appear in the Query History pane until you shut down the kernel:

  1. Select Connected.
  2. Select Shut down kernel.
  3. Suspend the notebook service.
* Renaming notebook files, folders, or the workspace can cause unexpected behavior, including service disconnection, clearing the notebook’s output
  cache, or delays in updating referenced files.
* If you are disconnected, try reconnecting the notebook. If you renamed the workspace, create and use a new service.
* Cell-by-cell rendering is not currently supported when viewing differences in Git-integrated workspaces or when viewing publish history in
  shared workspaces. The entire notebook file is displayed as a unified diff.

## Editing and running notebooks

* Updates to Python files (`.py`) imported by a notebook are not automatically detected by the active notebook service. To apply changes,
  restart the notebook kernel or use the `%autoreload` magic command before your initial import so that file updates are detected automatically.
* Each cell has an output limit of 1 MB.
* Output of previous notebook executions is cached in an internal storage system, which is not yet
  [Tri-Secret Secure](../../security-encryption-tss). Access to this cache is encrypted at rest and results in the cache are guarded
  by governance rules.
* iPywidgets are not yet supported.
* Embedding images in Markdown cells or using remote images via URLs is not yet supported.

  To embed an image in your notebook, upload it to your workspace and display it using a Python cell, for example:

  CopyExpand

  ```
  from IPython.display import Image, display
  display(Image(filename="path/to/example_image.png"))
  ```

  Show lessSee more

  Scroll to top

  For a cleaner presentation, you can collapse the code cell to show only the image result.
* SQL cells cannot run [EXECUTE NOTEBOOK PROJECT](../../../sql-reference/sql/execute-notebook-project) (non-interactive execution). To chain notebooks,
  use Jupyter magic commands, such as `%run`, which executes another notebook in the same Python process. For more information, see
  [Jupyter magics](notebooks-in-workspaces-edit-run.html#label-nb-in-ws-edit-run-jupyter-magics).
* If the execution context (database and schema) or the query warehouse is not set when you run notebooks in Workspaces, the interactive datagrid for
  displaying table results in code cells and cell referencing may not function properly. For information about setting the execution context, see
  [Set the execution context](notebooks-in-workspaces-edit-run.html#label-nb-in-ws-edit-run-execution-context).

## Migrating from legacy notebooks

For information about migrating legacy notebooks to Workspaces, see [Migrating legacy notebooks to Workspaces](notebooks-in-workspaces-migrate).
