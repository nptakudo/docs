---
title: "Drop or undrop dynamic tables"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-drop-undrop"
---

# Drop or undrop dynamic tables

This topic describes dropping existing dynamic tables and restoring them.

You might no longer need a dynamic table if it’s no longer relevant to your data pipeline. Dropping it helps clean up your environment and
reduces unnecessary storage and compute usage. Because dynamic tables consume resources, especially with frequent refreshes, dropping unused
tables can help manage costs by preventing further resource consumption.

You can undrop or, in other words, restore a dropped dynamic table using the UNDROP DYNAMIC TABLE command. This allows you to recover the
dynamic table and its data without needing to recreate it, whether it’s due to accidental deletion or if a previously dropped table becomes relevant again, such as with changing project priorities or data needs.

To drop or undrop a dynamic table, you must use a role that has the [OWNERSHIP](../sql-reference/sql/grant-ownership) privilege on that
dynamic table.

## Drop existing dynamic tables

To drop a dynamic table, you can use either the [DROP DYNAMIC TABLE](../sql-reference/sql/drop-dynamic-table) command or [Snowsight](ui-snowsight),
as long as you have the [OWNERSHIP](../sql-reference/sql/grant-ownership) privilege on that dynamic table.

SQLSnowsight

The following example uses the DROP DYNAMIC TABLE command to drop `my_dynamic_table`.

CopyExpand

```
DROP DYNAMIC TABLE my_dynamic_table;
```

Show lessSee more

Scroll to top

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Transformation » Dynamic tables.
3. Find your dynamic table in the list and then select [![More options](../_images/snowsight-worksheet-explorer-ellipsis.png)](../_images/snowsight-worksheet-explorer-ellipsis.png) » Drop.
4. In the popup, confirm that you want to drop the dynamic table.

## Restore dropped dynamic tables

To undrop a dynamic table, you can use the [UNDROP DYNAMIC TABLE](../sql-reference/sql/undrop-dynamic-table) command, as long as you have the
[OWNERSHIP](../sql-reference/sql/grant-ownership) privilege on that dynamic table. Note that you can only undrop dynamic tables within
the retention period (default is 24 hours). If a dynamic table with the same name already exists, an error will be returned.

The following example uses the UNDROP DYNAMIC TABLE command to drop `my_dynamic_table`.

CopyExpand

```
UNDROP DYNAMIC TABLE my_dynamic_table;
```

Show lessSee more

Scroll to top
