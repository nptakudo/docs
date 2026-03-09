---
title: "Working with the file system"
url: "https://docs.snowflake.com/en/user-guide/ui-snowsight/notebooks-in-workspaces/notebooks-in-workspaces-file-system"
---

# Working with the file system

Feature — Generally Available

Available to all AWS and Azure commercial regions. PrivateLink is supported.

## The Workspaces file system

The files shown in the left-hand pane of the Workspaces environment represent the contents of your Workspace directory, which is the notebook’s
working directory.

[![Workspaces file system](../../../_images/workspaces-file-system.png)](../../../_images/workspaces-file-system.png)

Running `ls` in the Workspace directory lists all files and folders in the directory, including notebooks and any other project assets.

## Referencing files

You can reference files in the current Workspace directory by relative path. For example, you want to read in a notebook and a sample dataset (in CSV) that are in your workspace:

CopyExpand

```
ml-intent-prediction/
├── data/
│   └── sample_data.csv
├── notebooks/
│   └── analysis.ipynb
└── utilities.py
```

Show lessSee more

Scroll to top

In a Python cell, run the following code:

CopyExpand

```
import pandas as pd

df = pd.read_csv("../data/sample_data.csv")
df.head()
```

Show lessSee more

Scroll to top

## Limitations

Writing files to the Workspace directory from code or the terminal is not supported. While file writes may appear to work during a session,
they are not guaranteed to succeed and may fail in future releases.

File persistence in the Workspace directory has the following limitations:

* **Files are read-only:** Files under `/workspace/<workspace_hash>` are read-only and cannot be updated in code while executing the notebook.
* **File writes from code or terminal are not supported:** Do not write files to the Workspace directory programmatically. Use Snowflake stages
  instead for persisting files (see [Persisting files](#label-nb-in-ws-file-system-persisting-files)).
* **Only files uploaded or created in Snowsight persist:** Only files that are uploaded or created through Snowsight persist across sessions.
* **Session-only visibility:** Any files created from code or the terminal during a session are removed when the notebook service is suspended.
  These files do not appear in the left-hand pane.

## The `/tmp` directory of the container

The `/tmp` directory is also read/write and is suitable for scratch work or temporary data that does not need to persist.

An example of writing a file to `/tmp`:

CopyExpand

```
file_path = "/tmp/sample.txt"

with open(file_path, "w") as f:
    f.write("Hello from Python!\\nThis is a sample file saved in /tmp.")

print(f"File written to {file_path}")
```

Show lessSee more

Scroll to top

To list files in the `/tmp` directory, run the following:

CopyExpand

```
%%bash
cd /tmp
ls
```

Show lessSee more

Scroll to top

## Persisting files

To store files for later use, write them to a Snowflake stage with write access using Snowpark file operation APIs.

To learn more about required stage privileges, see [write access](../../security-access-control-privileges.html#label-access-control-privileges-stage). For Snowpark file operations, see
[Snowpark file operation APIs](https://docs.snowflake.com/en/developer-guide/snowpark/reference/python/1.6.1/api/snowflake.snowpark.FileOperation#snowflake.snowpark.FileOperation).
