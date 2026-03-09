---
title: "Staging data files from a local file system"
url: "https://docs.snowflake.com/en/user-guide/data-load-local-file-system-stage"
---

# Staging data files from a local file system

Execute [PUT](../sql-reference/sql/put) using the [Snowflake CLI](../developer-guide/snowflake-cli/index) client, the [SnowSQL client](snowsql), or [Drivers](../developer-guide/drivers) to upload (stage) local data files into an internal stage.

If you want to load a few small local data files into a named internal stage, you can also use Snowsight.
Refer to [Staging files using Snowsight](data-load-local-file-system-stage-ui).

## Staging the data files

User Stage
:   The following example uploads a file named `data.csv` in the `/data` directory on your local machine to
    your user stage and prefixes the file with a folder named `staged`.

    Note that the `@~` character combination identifies a user stage.

    * Linux or macOS

      > CopyExpand
      >
      > ```
      > PUT file:///data/data.csv @~/staged;
      > ```
      >
      > Show lessSee more
      >
      > Scroll to top
    * Windows

      > CopyExpand
      >
      > ```
      > PUT file://C:\data\data.csv @~/staged;
      > ```
      >
      > Show lessSee more
      >
      > Scroll to top

Table Stage
:   The following example uploads a file named `data.csv` in the `/data` directory on your local machine to
    the stage for a table named `mytable`.

    Note that the `@%` character combination identifies a table stage.

    * Linux or macOS

      > CopyExpand
      >
      > ```
      > PUT file:///data/data.csv @%mytable;
      > ```
      >
      > Show lessSee more
      >
      > Scroll to top
    * Windows

      > CopyExpand
      >
      > ```
      > PUT file://C:\data\data.csv @%mytable;
      > ```
      >
      > Show lessSee more
      >
      > Scroll to top

Named Stage
:   The following example uploads a file named `data.csv` in the `/data` directory on your local machine to a
    named internal stage called `my_stage`. See [Choosing an internal stage for local files](data-load-local-file-system-create-stage) for information on named stages.

    In SQL, note that the `@` character by itself identifies a named stage.

    * Linux or macOS

      SQLPython

      CopyExpand

      ```
      PUT file:///data/data.csv @my_stage;
      ```

      Show lessSee more

      Scroll to top

      CopyExpand

      ```
      my_stage_res = root.databases["<database>"].schemas["<schema>"].stages["my_stage"]
      my_stage_res.put("/data/data.csv", "/")
      ```

      Show lessSee more

      Scroll to top
    * Windows

      SQLPython

      CopyExpand

      ```
      PUT file://C:\data\data.csv @my_stage;
      ```

      Show lessSee more

      Scroll to top

      CopyExpand

      ```
      my_stage_res = root.databases["<database>"].schemas["<schema>"].stages["my_stage"]
      my_stage_res.put("C:/data/data.csv", "/")
      ```

      Show lessSee more

      Scroll to top

## Listing staged data files

To see files that have been uploaded to a Snowflake stage, use the [LIST](../sql-reference/sql/list) command:

User stage:

CopyExpand

```
LIST @~;
```

Show lessSee more

Scroll to top

Table stage:

CopyExpand

```
LIST @%mytable;
```

Show lessSee more

Scroll to top

Named stage:

SQLPython

CopyExpand

```
LIST @my_stage;
```

Show lessSee more

Scroll to top

CopyExpand

```
stage_files = root.databases["<database>"].schemas["<schema>"].stages["my_stage"].list_files()
for stage_file in stage_files:
  print(stage_file)
```

Show lessSee more

Scroll to top

**Next:** [Copy data from an internal stage](data-load-local-file-system-copy)
