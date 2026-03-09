---
title: "Running notebooks with parameters"
url: "https://docs.snowflake.com/en/user-guide/ui-snowsight/notebooks-in-workspaces/notebooks-in-workspaces-parameters"
---

# Running notebooks with parameters

Feature — Generally Available

Available to all AWS and Azure commercial regions. PrivateLink is supported.

Currently, parameters passed in the `ARGUMENTS` string are parsed into the `sys.argv` list using whitespace as the delimiter.

## Example: Execute a notebook project with parameters

The following example passes two arguments (env and prod) using ARGUMENTS = ‘env prod’.

The first element (`sys.argv[0]`) is the notebook filename, followed by the space-separated arguments.

CopyExpand

```
EXECUTE NOTEBOOK PROJECT "<database_name>"."<schema_name>"."<project_name>"
  MAIN_FILE = 'snow://workspace/<workspace_hash>/path/to/notebook.ipynb' -- Notebook name with full file path
  COMPUTE_POOL = '<compute_pool_name>'
  RUNTIME = '<runtime_version>'    -- For example, V2.2-CPU-PY3.11
  QUERY_WAREHOUSE = '<warehouse_name>'
  ARGUMENTS = 'env prod' -- Can pass in a single string, which can be parsed in the notebook code. Point to the environment configuration.
  REQUIREMENTS_FILE = 'path/to/requirements.txt';
```

Show lessSee more

Scroll to top

## View all arguments

To inspect the full list of parameters passed to the session, use the `sys` module.

CopyExpand

```
import sys
print(sys.argv)
```

Show lessSee more

Scroll to top

Output example:

CopyExpand

```
['exampletestSCOS.ipynb', 'env', 'prod']
```

Show lessSee more

Scroll to top

## Print each argument

To process or log each parameter individually, loop through the `sys.argv` list.

CopyExpand

```
import sys
for arg in sys.argv:
    print(arg)
```

Show lessSee more

Scroll to top

Output example:

CopyExpand

```
exampletestSCOS.ipynb
env
prod
```

Show lessSee more

Scroll to top

## Access a specific argument

Parameters are accessed by their index in the list. Because `sys.argv[0]` is the notebook name, the first user parameter starts at `index[1]`.

CopyExpand

```
import sys

# Access the first user parameter
first_param = sys.argv[1]
print(first_param)
```

Show lessSee more

Scroll to top

Output example:

CopyExpand

```
env
```

Show lessSee more

Scroll to top

For full syntax and parameter details, see [EXECUTE NOTEBOOK PROJECT](../../../sql-reference/sql/execute-notebook-project).
