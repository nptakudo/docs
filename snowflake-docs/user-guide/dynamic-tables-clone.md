---
title: "Clone dynamic tables"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-clone"
---

# Clone dynamic tables

Cloning creates a new dynamic table with the same column definitions and contains all the existing data from the source dynamic table, without
actually copying the data.

You can clone a dynamic table to a new dynamic table or regular table.

## Clone a dynamic table to a new dynamic table

Cloned dynamic tables, whether cloned directly or as part of a cloned database or schema, are suspended by default.

In [DYNAMIC\_TABLE\_GRAPH\_HISTORY](../sql-reference/functions/dynamic_table_graph_history), this appears as CLONED\_AUTO\_SUSPENDED in the SCHEDULING\_STATE column. Any
downstream dynamic tables are also suspended, shown as UPSTREAM\_CLONED\_AUTO\_SUSPENDED. For more information, see
[Automatic dynamic table suspension](dynamic-tables-suspend-resume.html#label-dynamic-tables-manage-understanidng-auto-suspend).

CopyExpand

```
-- Clone a dynamic table to a new dynamic table
CREATE [ OR REPLACE ] [ TRANSIENT ] DYNAMIC TABLE <name>
  CLONE <source_dynamic_table>
        [ { AT | BEFORE } ( { TIMESTAMP => <timestamp> | OFFSET => <time_difference> | STATEMENT => <id> } ) ]
  [
    COPY GRANTS
    TARGET_LAG = { '<num> { seconds | minutes | hours | days }' | DOWNSTREAM }
    WAREHOUSE = <warehouse_name>
  ]
```

Show lessSee more

Scroll to top

You can also clone a dynamic table as it existed at a specific point in the past:

CopyExpand

```
CREATE DYNAMIC TABLE my_cloned_dynamic_table CLONE my_dynamic_table AT (TIMESTAMP => TO_TIMESTAMP_TZ('04/05/2013 01:02:03', 'mm/dd/yyyy hh24:mi:ss'));
```

Show lessSee more

Scroll to top

For more information, see [Cloning using Time Travel (databases, schemas, tables, dynamic tables, event tables, and streams only)](object-clone.html#label-cloning-and-time-travel).

## Clone a dynamic table to a new table

Cloned tables inherit the same column definitions and data of the source dynamic table but lack dynamic table-specific properties. They retain
row access and masking policies, tags, clustering keys, and comments.

CopyExpand

```
-- Clone a dynamic table to a new table
CREATE [ OR REPLACE ] TABLE [ IF NOT EXISTS ] <name>
CLONE <source_dynamic_table_name>
  [ { AT | BEFORE } ( { TIMESTAMP => <timestamp> | OFFSET => <time_difference> | STATEMENT => <id> } ) ]
```

Show lessSee more

Scroll to top

Cloning a dynamic table to a regular table follows the same considerations as [cloning a regular table](../sql-reference/sql/create-clone),
with the following exceptions:

* The source dynamic table has to be [initialized](dynamic-tables-refresh.html#label-dynamic-tables-initialization) in order to be cloned as a regular table.
* You can’t clone dynamic Apache Iceberg™ tables.

## Best practice for cloning pipelines of dynamic tables

Clone all elements of the dynamic table pipeline in the same clone command to avoid reinitializations of your pipeline. You can do this by
consolidating all elements of the pipeline (e.g. base tables, view, and dynamic tables) in the same schema or database.
