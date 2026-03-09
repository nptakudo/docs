---
title: "Custom data metric functions"
url: "https://docs.snowflake.com/en/user-guide/data-quality-custom-dmfs"
---

# Custom data metric functions

[![Snowflake logo in black (no text)](../_images/logo-snowflake-black.png)](../_images/logo-snowflake-black.png) [Enterprise Edition Feature](intro-editions)

Data Quality Monitoring requires Enterprise Edition. To inquire about upgrading, please contact
[Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

If there isn’t a [system data quality metric function (DMF)](data-quality-system-dmfs) that can perform your data quality
checks, then you can use the [CREATE DATA METRIC FUNCTION](../sql-reference/sql/create-data-metric-function) command to create your own DMF.

## Create a custom DMF

The following examples demonstrate how to use the [CREATE DATA METRIC FUNCTION](../sql-reference/sql/create-data-metric-function) command to create a custom DMF.

Example: User-defined DMF with single table argument
:   Create a DMF that calls the [COUNT](../sql-reference/functions/count) function to return the total number of rows that
    have positive numbers in three columns of the table:

    CopyExpand

    ```
    CREATE OR REPLACE DATA METRIC FUNCTION governance.dmfs.count_positive_numbers(
      arg_t TABLE(
        arg_c1 NUMBER,
        arg_c2 NUMBER,
        arg_c3 NUMBER
      )
    )
    RETURNS NUMBER
    AS
    $$
      SELECT
        COUNT(*)
      FROM arg_t
      WHERE
        arg_c1>0
        AND arg_c2>0
        AND arg_c3>0
    $$;
    ```

    Show lessSee more

    Scroll to top

Example: Using multiple table arguments to perform referential checks
:   A user-defined DMF can have more than one argument that accepts a table. When you add the DMF to a table, that table is used as the first
    argument. If there is an additional argument that accepts a table, you must also specify the fully qualified name of the second table. This
    capability simplifies referential integrity, matching and comparison, or conditional checking across different datasets.

    Suppose you want to validate referential integrity as defined by a primary key/foreign key relationship. In this case, you can create a
    DMF to validate that all records in a source table have corresponding records in the referenced table. The following user-defined DMF
    returns the number of records where the value of a column in one table does not have a corresponding value in the column of another table:

    CopyExpand

    ```
    CREATE OR REPLACE DATA METRIC FUNCTION governance.dmfs.referential_check(
      arg_t1 TABLE (arg_c1 INT), arg_t2 TABLE (arg_c2 INT))
    RETURNS NUMBER AS
     'SELECT COUNT(*) FROM arg_t1
      WHERE arg_c1 NOT IN (SELECT arg_c2 FROM arg_t2)';
    ```

    Show lessSee more

    Scroll to top

    Now suppose you want to check whether every order, as identified by its `sp_id`, in the `salesorders` table maps back to an `sp_id`
    in the `salespeople` table. You can add the DMF to the `salesorders` table while specifying the `salespeople` table as the other
    table argument.

    CopyExpand

    ```
    ALTER TABLE salesorders
      ADD DATA METRIC FUNCTION governance.dmfs.referential_check
        ON (sp_id, TABLE (my_db.sch1.salespeople(sp_id)));
    ```

    Show lessSee more

    Scroll to top

    The output returns the number of rows in the `salesorders` table that have a value in the `sp_id` column that doesn’t appear in the
    `sp_id` column of the `salespeople` table. A value greater than 0 indicates that there are `sp_id` values in `salesorders` that
    don’t map to records in `salespeople`.

## Test a custom DMF

You can execute a custom DMF manually in order to test it before associating it with one or more tables. For more information, see
[Call a DMF manually](data-quality-working.html#label-data-quality-call).

## Secure the custom DMF

You can use the ALTER FUNCTION command to make a DMF secure. For more information about what it means for a function to be secure, see
[Protecting Sensitive Information with Secure UDFs and Stored Procedures](../developer-guide/secure-udf-procedure).

CopyExpand

```
ALTER FUNCTION governance.dmfs.count_positive_numbers(
 TABLE(
   NUMBER,
   NUMBER,
   NUMBER
))
SET SECURE;
```

Show lessSee more

Scroll to top

## View the properties of a DMF

Describe the DMF to view its properties:

CopyExpand

```
DESC FUNCTION governance.dmfs.count_positive_numbers(
  TABLE(
    NUMBER, NUMBER, NUMBER
  )
);
```

Show lessSee more

Scroll to top

Expand

```
+-----------+---------------------------------------------------------------------+
| property  | value                                                               |
+-----------+---------------------------------------------------------------------+
| signature | (ARG_T TABLE(ARG_C1 NUMBER, ARG_C2 NUMBER, ARG_C3 NUMBER))          |
| returns   | NUMBER(38,0)                                                        |
| language  | SQL                                                                 |
| body      | SELECT COUNT(*) FROM arg_t WHERE arg_c1>0 AND arg_c2>0 AND arg_c3>0 |
+-----------+---------------------------------------------------------------------+
```

Show lessSee more

Scroll to top

## Set a tag on a custom DMF

Use the [ALTER FUNCTION](../sql-reference/sql/alter-function) command to set a tag on a DMF:

CopyExpand

```
ALTER FUNCTION governance.dmfs.count_positive_numbers(
  TABLE(NUMBER, NUMBER, NUMBER))
  SET TAG governance.tags.quality = 'counts';
```

Show lessSee more

Scroll to top

## Drop a custom DMF

You can use the [DROP FUNCTION](../sql-reference/sql/drop-function) command to remove a custom data metric function from the system.

Note

You cannot drop a custom DMF from the system while it is still associated with a table or view. Use the [DATA\_METRIC\_FUNCTION\_REFERENCES](../sql-reference/functions/data_metric_function_references) function to identify the tables and views that have a data metric function set on them.

For information about removing DMF associations from a table or view, see [Drop a DMF from an object](data-quality-working.html#label-dmf-drop-dmf-object).

Drop a custom DMF from the system:

CopyExpand

```
DROP FUNCTION governance.dmfs.count_positive_numbers(
  TABLE(
    NUMBER, NUMBER, NUMBER
  )
);
```

Show lessSee more

Scroll to top
