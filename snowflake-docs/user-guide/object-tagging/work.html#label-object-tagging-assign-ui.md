---
title: "Work with object tags"
url: "https://docs.snowflake.com/en/user-guide/object-tagging/work.html#label-object-tagging-assign-ui"
---

# Work with object tags

This topic describes how to create a tag and assign it to a Snowflake object. It also contains instructions on how to delete a tag.

## Create a tag

Use the [CREATE TAG](../../sql-reference/sql/create-tag) command to create a new tag. For example, to create a basic tag named `cost_center` without
any optional parameters, execute the following:

CopyExpand

```
CREATE TAG cost_center;
```

Show lessSee more

Scroll to top

### Set a list of allowed tag values

The `ALLOWED_VALUES` tag parameter lets you specify a list of the string values that can be assigned to the tag when the tag is set
on an [object](introduction.html#label-object-tag-supported-objects). Users cannot assign a value to a tag unless the value is in the defined list.

The maximum number of possible string values for a single tag is 5,000. The string value for each tag can be up to 256 characters.

You can specify the list of allowed values when creating or replacing a tag with a [CREATE TAG](../../sql-reference/sql/create-tag) statement, or while
modifying an existing tag with an [ALTER TAG](../../sql-reference/sql/alter-tag) statement. Note that the ALTER TAG statement supports adding allowed
values for a tag and dropping existing values for a tag.

Note

If a tag is configured to automatically propagate to target objects, the order of values in the allowed list can affect how conflicts are
resolved. For more information, see [Tag propagation conflicts](propagation.html#label-tag-propagation-conflicts).

To determine the list of allowed values for a tag, call the [SYSTEM$GET\_TAG\_ALLOWED\_VALUES](../../sql-reference/functions/system_get_tag_allowed_values) function.

#### Examples

Create a tag named `cost_center` with `finance` and `engineering` as the only two allowed string values:

> CopyExpand
>
> ```
> CREATE TAG cost_center
>   ALLOWED_VALUES 'finance', 'engineering';
> ```
>
> Show lessSee more
>
> Scroll to top

Verify the allowed values:

> CopyExpand
>
> ```
> SELECT SYSTEM$GET_TAG_ALLOWED_VALUES('governance.tags.cost_center');
> ```
>
> Show lessSee more
>
> Scroll to top

Modify the tag named `cost_center` to add `marketing` as an allowed string value:

> CopyExpand
>
> ```
> ALTER TAG cost_center
>   ADD ALLOWED_VALUES 'marketing';
> ```
>
> Show lessSee more
>
> Scroll to top

Modify the tag named `cost_center` to drop `engineering` as an allowed string value:

> CopyExpand
>
> ```
> ALTER TAG cost_center
>   DROP ALLOWED_VALUES 'engineering';
> ```
>
> Show lessSee more
>
> Scroll to top

### Define a tag that will automatically propagate

[![Snowflake logo in black (no text)](../../_images/logo-snowflake-black.png)](../../_images/logo-snowflake-black.png) [Enterprise Edition Feature](../intro-editions)

This feature requires Enterprise Edition or higher. To inquire about upgrading,
please contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

The PROPAGATE tag parameter lets you configure a tag so it is automatically propagated from a source object to target objects under
certain circumstances. This PROPAGATE parameter can be set to the following values:

* `PROPAGATE = ON_DEPENDENCY`: The tag is propagated to a target object when there is an
  [object dependency](propagation.html#label-tag-propagation-dependency).
* `PROPAGATE = ON_DATA_MOVEMENT`: The tag is propagated to a target object when [data moves](propagation.html#label-tag-propagation-lineage) from the
  source object to the target object.
* `PROPAGATE = ON_DEPENDENCY_AND_DATA_MOVEMENT`: The tag is propagated for both object dependencies and data movement.

For more information about propagation, see [Automatic tag propagation with user-defined tags](propagation).

#### Examples

Create a new tag that propagates automatically when there is an object dependency.

CopyExpand

```
CREATE TAG data_sensitivity PROPAGATE = ON_DEPENDENCY;
```

Show lessSee more

Scroll to top

Update an existing tag to enable automatic propagation for both object dependency and data movement.

CopyExpand

```
ALTER TAG data_sensitivity SET PROPAGATE = ON_DEPENDENCY_AND_DATA_MOVEMENT;
```

Show lessSee more

Scroll to top

Update an existing tag to disable propagation.

CopyExpand

```
ALTER TAG data_sensitivity UNSET PROPAGATE;
```

Show lessSee more

Scroll to top

## Set a tag

You can set a tag on an object using the [user interface](#label-object-tagging-assign-ui) or [SQL](#label-object-tagging-assign-sql).

When you set a tag on an object, you must set the value of the tag. This string value can be up to 256 characters.

The user who created a tag might have specified a list of allowed values, in which case you can only set a tag value that is in the list.
To obtain the list of allowed string values for a given tag, call the [SYSTEM$GET\_TAG\_ALLOWED\_VALUES](../../sql-reference/functions/system_get_tag_allowed_values)
function. For example, assuming that the tag `cost_center` is stored in a database named `governance` and a schema named `tags`, you
can execute the following to determine that you can set the tag value to `finance` or `marketing`:

CopyExpand

```
SELECT SYSTEM$GET_TAG_ALLOWED_VALUES('governance.tags.cost_center');
```

Show lessSee more

Scroll to top

Expand

```
+--------------------------------------------------------------+
| SYSTEM$GET_TAG_ALLOWED_VALUES('GOVERNANCE.TAGS.COST_CENTER') |
|--------------------------------------------------------------|
| ["finance","marketing"]                                      |
+--------------------------------------------------------------+
```

Show lessSee more

Scroll to top

### Use Snowsight to set tags

You can set a tag on existing tables, views, and columns using Snowsight.

There are several options to set a tag:

* In the navigation menu, select Catalog » Database Explorer, and then navigate to the desired table, view, or column using the object explorer.

  Select the More menu (that is, `...`) » Edit, and then select + Tag. Follow the prompts to manage the tag
  assignment.
* In the navigation menu, select Governance & security » Tags & policies, and do the following:

  + Select a tile, distribution percentage, and one of the most used tags or tables. When you select an item in the Dashboard,
    Snowsight redirects you to the Tagged Objects tab.
  + Modify the filters as needed. When you select an object or column, Snowsight redirects you to its location in the
    object explorer. Update the tag assignment as needed.
* Navigate to the Tagged Objects tab directly. Modify the filters, select an object or column, and manage the tag assignment.

Note

To access the Tags & policies area, your Snowflake account must be [Enterprise Edition or higher](../intro-editions).
In addition, you must have one of the following roles:

* Use the ACCOUNTADMIN role.
* Use a role that is granted the GOVERNANCE\_VIEWER and OBJECT\_VIEWER database roles.

  For information about these database roles, see [SNOWFLAKE database roles](../../sql-reference/snowflake-db-roles).

### Use SQL to set tags

You can use SQL commands to set a tag when creating a new object or to set a tag on an existing object.

To set a tag on a new object you’re creating, use a CREATE … WITH TAG command. For example, to assign a tag `cost_center` to a
warehouse that you’re creating, execute the following:

CopyExpand

```
CREATE WAREHOUSE mywarehouse WITH TAG (cost_center = 'sales');
```

Show lessSee more

Scroll to top

To set a tag on an existing object, use an ALTER … SET TAG command. For example, to assign a tag `cost_center` to an existing warehouse,
execute the following:

CopyExpand

```
ALTER WAREHOUSE wh1 SET TAG cost_center = 'sales';
```

Show lessSee more

Scroll to top

#### Extended example: Create and assign tags with SQL

The following is an extended example that provides a high-level overview on how to use SQL to implement object tagging. It shows you how to
do the following:

* Manage the access control privileges needed to work with tags.

  For simplicity, the workflow assumes a centralized management approach to tags, where the `tag_admin` custom role has both the CREATE
  TAG and the global APPLY TAG privileges. For alternative approaches, see [Approaches assigning tagging privileges](#label-object-tagging-approaches).
* Create a tag using a [CREATE TAG](../../sql-reference/sql/create-tag) statement.
* Assign a tag to a new Snowflake object using a [CREATE <object>](../../sql-reference/sql/create) command.
* Assign a tag to existing Snowflake objects using [ALTER <object>](../../sql-reference/sql/alter) commands.

1. Create a custom role and assign privileges.

   In a centralized management approach, the `tag_admin` custom role is responsible for creating and assigning tags to Snowflake objects.

   Note that this example uses the ACCOUNTADMIN system role. If using this higher-privileged role in a production environment is not
   desirable, verify that the role assigning privileges to the `tag_admin` custom role has the necessary privileges to qualify the
   `tag_admin` custom role. For more information, see [Access control privileges](#label-object-tags-privileges) (in this topic).

   CopyExpand

   ```
   USE ROLE USERADMIN;
   CREATE ROLE tag_admin;
   USE ROLE ACCOUNTADMIN;
   GRANT CREATE TAG ON SCHEMA mydb.mysch TO ROLE tag_admin;
   GRANT APPLY TAG ON ACCOUNT TO ROLE tag_admin;
   ```

   Show lessSee more

   Scroll to top
2. Grant the `tag_admin` custom role to a user serving as the tag administrator.

   CopyExpand

   ```
   USE ROLE USERADMIN;
   GRANT ROLE tag_admin TO USER jsmith;
   ```

   Show lessSee more

   Scroll to top
3. Execute a [CREATE TAG](../../sql-reference/sql/create-tag) statement to create a tag.

   CopyExpand

   ```
   USE ROLE tag_admin;
   USE SCHEMA mydb.mysch;
   CREATE TAG cost_center;
   ```

   Show lessSee more

   Scroll to top
4. Assign the tag to a new warehouse.

   CopyExpand

   ```
   USE ROLE tag_admin;
   CREATE WAREHOUSE mywarehouse WITH TAG (cost_center = 'sales');
   ```

   Show lessSee more

   Scroll to top
5. Assign the tag to an existing warehouse.

   CopyExpand

   ```
   USE ROLE tag_admin;
   ALTER WAREHOUSE wh1 SET TAG cost_center = 'sales';
   ```

   Show lessSee more

   Scroll to top
6. Assign the tag to a column of an existing table.

   CopyExpand

   ```
   ALTER TABLE hr.tables.empl_info
     MODIFY COLUMN job_title
     SET TAG cost_center = 'marketing';
   ```

   Show lessSee more

   Scroll to top

## Delete a tag

Use the [DROP TAG](../../sql-reference/sql/drop-tag) command to delete a tag. When you execute the command, there is a 24-hour grace period before
the tag is permanently deleted. During the grace period, you can execute the UNDROP TAG command to restore the tag, which also restores all
of the tag assignments (that is, references) between the tag and objects.

If you want to determine which objects have a tag before you delete it, query the
[TAG\_REFERENCES](../../sql-reference/account-usage/tag_references) view (in Account Usage) to determine the tag assignments.

## Access control privileges

### Tag privileges

Snowflake supports the following privileges to determine whether users can create, set, and own tags.

Operating on an object in a schema requires at least one privilege on the parent database and at least one privilege on the parent schema.

| Privilege | Usage |
| --- | --- |
| CREATE | Enables creating a new tag in a schema. |
| APPLY | Enables the set and unset operations for the tag on a Snowflake object. For syntax examples, see: [Summary of DDL commands, operations, and privileges](#label-object-tags-ddl-privilege-summary). |
| OWNERSHIP | Transfers ownership of the tag, which grants full control over the tag. Required to alter most properties of a tag. |

See moreShow less

Expand

### Summary of DDL commands, operations, and privileges

The following table summarizes the relationship between tag privileges and DDL operations.

| Operation | Privilege required |
| --- | --- |
| Create tag. | A role with the CREATE TAG privilege in the same schema. |
| Create tag that propagates | A role with the APPLY TAG privilege on the account and the OWNERSHIP privilege on the tag. |
| Alter tag. | The role with the OWNERSHIP privilege on the tag. |
| Drop & Undrop tag. | A role with the OWNERSHIP privilege on the tag and the USAGE privilege on the database and schema in which the tag exists. |
| Show tags. | One of the following: . A role with the USAGE privilege on the schema in which the tags exist, or . A role with the APPLY TAG privilege on the account. |
| Set or unset a tag on an object. | For individual objects, a role with the APPLY TAG privilege on the account, or the APPLY TAG privilege on the tag and the OWNERSHIP privilege on the object on which the tag is set. See [Supported objects](introduction.html#label-object-tag-supported-objects). |
| Set or unset a tag on a column. | A role with the APPLY TAG privilege on the account, or a role with the APPLY privilege on the tag and the OWNERSHIP privilege on the table or view. |
| Get tags on an object. | See [SYSTEM$GET\_TAG](../../sql-reference/functions/system_get_tag), [TAG\_REFERENCES](../../sql-reference/functions/tag_references), and [TAG\_REFERENCES\_WITH\_LINEAGE](../../sql-reference/functions/tag_references_with_lineage). |

See moreShow less

Expand

### Approaches assigning tagging privileges

This section describes different approaches to assigning the privileges needed to create and set tags.

1. For a centralized tag management approach in which the `tag_admin` custom role creates and sets tags on all objects/columns,
   the following privileges are necessary:

   CopyExpand

   ```
   USE ROLE securityadmin;
   GRANT CREATE TAG ON SCHEMA <db_name.schema_name> TO ROLE tag_admin;
   GRANT APPLY TAG ON ACCOUNT TO ROLE tag_admin;
   ```

   Show lessSee more

   Scroll to top
2. In a hybrid management approach, a single role has the CREATE TAG privilege to ensure tags are named consistently and individual
   teams or roles have the APPLY privilege for a specific tag.

   For example, the custom role `finance_role` can be granted the privilege to set the tag `cost_center` on tables and views
   the role owns (that is, the role has the OWNERSHIP privilege on the table or view):

   CopyExpand

   ```
   USE ROLE securityadmin;
   GRANT CREATE TAG ON SCHEMA <db_name.schema_name> TO ROLE tag_admin;
   GRANT APPLY ON TAG cost_center TO ROLE finance_role;
   ```

   Show lessSee more

   Scroll to top
