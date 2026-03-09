---
title: "Getting started with worksheets"
url: "https://docs.snowflake.com/en/user-guide/ui-snowsight-worksheets-gs"
---

# Getting started with worksheets

Important

Starting in September 2025, Snowflake is gradually upgrading accounts from Worksheets to Workspaces. Workspaces will become the default
SQL editor. For more information, see [Defaulting accounts from Worksheets to Workspaces](../release-notes/bcr-bundles/un-bundled/bcr-2117).

View and create worksheets in Snowsight.

SQL worksheets let you write and run SQL statements, explore and filter query results, and visualize the results.
See [Querying data using worksheets](ui-snowsight-query) and [Visualizing worksheet data](ui-snowsight-visualizations).
You can also write Snowpark Python in worksheets. See [Writing Snowpark Code in Python Worksheets](../developer-guide/snowpark/python/python-worksheets).

Manage your worksheets by organizing them into folders, share worksheets with colleagues that also use Snowflake, and
manage the version history for worksheets. For more details, see [Work with worksheets in Snowsight](ui-snowsight-worksheets).

## Viewing worksheets in Snowsight

After signing in to Snowsight, you see the worksheets in your account.

Using the options, you can view recent worksheets opened by you, worksheets that your colleagues have shared with you,
worksheets that you created and own, or folders you created or that your colleagues have shared with you.

For any worksheet or worksheet folder, you can review the title, roughly when the worksheet or folder was last viewed or updated,
and the role associated with the worksheet or folder. In each row, you can see the initials of the user that owns the worksheet or folder.
You can sort by any column in the table.

Use the Search option to search the titles and contents of worksheets and dashboards that you can access.

## Import worksheets

You can import your Classic Console SQL worksheets into Snowsight even if you can no longer access the Classic Console.

Important

Snowsight has a maximum worksheet size of 1 MB, and larger worksheets cannot be imported. See [Troubleshoot issues with upgrading to Snowsight](ui-snowsight-upgrade-migrate.html#label-troubleshoot-snowsight-upgrade).

### Import worksheets from Classic Console into Snowsight Worksheets

To import your Classic Console SQL worksheets into Snowsight Worksheets, follow these steps:

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Projects » Worksheets.
3. Select the … more menu at the top right of your worksheet, then select Import Worksheets.

   [![Select Options for worksheets, then select Import Worksheets.](../_images/snowsight-worksheets-import.png)](../_images/snowsight-worksheets-import.png)
4. In the confirmation dialog, select Import.

Snowflake creates a folder named Import YYYY-MM-DD and places all worksheets from the Classic Console in that folder.

### Import worksheets from Classic Console into Snowsight Workspaces

To import your Classic Console SQL worksheets into Snowsight Workspaces, follow these steps:

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Projects » Workspaces.
3. Select the … more menu in the Worksheets pane.
4. Select Import Worksheets from Classic.

   [![Select Options for workspaces, then select Import Worksheets from Classic.](../_images/worksheets-import-to-workspaces.png)](../_images/worksheets-import-to-workspaces.png)
5. In the confirmation dialog, select Import.

Snowflake creates a folder named Import YYYY-MM-DD and places all worksheets from the Classic Console in that folder.

## Create worksheets in Snowsight

To create a worksheet in Snowsight, do the following:

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Projects » Worksheets.
3. Select + and select SQL Worksheet or Python Worksheet to create a worksheet.

   The worksheet opens in the same window with the date and time of creation as the default title.

You can then start writing in your worksheet. For a SQL worksheet, [start writing queries](ui-snowsight-query.html#label-worksheets-write-sql).
For a Python worksheet, [start writing code](../developer-guide/snowpark/python/python-worksheets).

### Create worksheets from a SQL file

To create a SQL worksheet from an existing SQL file, do the following:

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Projects » Worksheets.
3. Select the … more menu » Create Worksheet from SQL File.
4. Browse to the SQL file to upload.
5. A new worksheet opens with a title that matches the file name.

You can also add a SQL file to an existing SQL worksheet. Refer to [Append a SQL script to an existing worksheet](ui-snowsight-query.html#label-worksheets-append-sql).

## Opening worksheets in tabs

You can use tabs to refer to multiple active worksheets and explore the databases and schemas in Snowflake while writing SQL
statements or Python code in Snowsight. Your scroll position is preserved in each tab, making comparisons across worksheets easier
to perform. Worksheet tabs are preserved across sessions, so you can pick up your work where you left off.

To open your Snowsight worksheets in tabs, do the following:

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Projects » Worksheets.
3. Select an existing worksheet, or select + Worksheet to open a new worksheet.
4. Select a role to run the worksheet as, and select a warehouse to allocate the compute resources for your query.
5. In the Worksheets menu, select an existing worksheet or select + to open a new worksheet tab. By default, the new worksheet
   uses your default role and warehouse.
6. (Optional) Make changes to the role or warehouse used to run the new worksheet.

After you open a worksheet, you can [update the contents](ui-snowsight-worksheets),
[run SQL statements](ui-snowsight-query) or
[write Python code](../developer-guide/snowpark/python/python-worksheets), and manage the worksheet.
