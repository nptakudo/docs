---
title: "Dynamic table access control"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-privileges"
---

# Dynamic table access control

This topic discusses the privileges needed to perform operations with dynamic tables, such as creating, querying, altering, viewing, and dropping.

For more information about the Snowflake privilege model, see [Overview of Access Control](security-access-control-overview) and [Access control privileges](security-access-control-privileges).

## Transfer ownership

To provide a user full access to a dynamic table, you can do either of the following:

* [Grant OWNERSHIP on the dynamic table to a role](../sql-reference/sql/grant-ownership).
* [Grant all privileges, except OWNERSHIP, on the dynamic table to a role](../sql-reference/sql/grant-privilege).
* [Grant the OWNERSHIP privilege or ALL PRIVILEGES on future dynamic tables to a role](../sql-reference/sql/grant-privilege.html#label-grant-privilege-schema-future-grants).

When assigning grants, ensure that you specify the object type as `DYNAMIC TABLE` because dynamic tables have a different set of privileges
than regular tables.

To grant the OWNERSHIP privilege on dynamic tables, ensure the receiving role has the USAGE privilege on the following. Otherwise, subsequent
scheduled refreshes fail.

* The database and schema that contains the dynamic table.
* The warehouse used to refresh the table.

To transfer ownership of a dynamic table, you can use either the [GRANT OWNERSHIP](../sql-reference/sql/grant-ownership) command or
[Snowsight](ui-snowsight).

SQLSnowsight

The following example uses the [GRANT OWNERSHIP](../sql-reference/sql/grant-ownership) command to grant ownership privileges on `my_dynamic_table`
to the `budget_admin` role.

CopyExpand

```
GRANT OWNERSHIP ON DYNAMIC TABLE my_dynamic_table TO ROLE budget_admin;
```

Show lessSee more

Scroll to top

The following example uses the [GRANT OWNERSHIP](../sql-reference/sql/grant-ownership) command to grant ownership privileges on all future dynamic
tables created in the `mydb.myschema` schema to the `budget_admin` role.

CopyExpand

```
GRANT OWNERSHIP ON FUTURE DYNAMIC TABLES IN SCHEMA mydb.myschema TO ROLE budget_admin;
```

Show lessSee more

Scroll to top

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Transformation » Dynamic tables.
3. Find your dynamic table in the list and then select [![More options](../_images/snowsight-worksheet-explorer-ellipsis.png)](../_images/snowsight-worksheet-explorer-ellipsis.png) » Transfer Ownership.
4. Select the role to transfer ownership to.

To learn more about the Snowflake privilege model, see [Overview of Access Control](security-access-control-overview) and [Access control privileges](security-access-control-privileges).

## Refresh dynamic tables with specific user privileges and secondary roles

Dynamic tables can be configured to refresh with the privileges of a specific user, in addition to privileges of the owner role. Dynamic
tables that specify EXECUTE AS USER run on behalf of the named user, instead of the system user.

For example, you can grant a user a primary role that provides access to a table and a secondary role that provides access to a virtual
warehouse. The user can then create a dynamic table that operates with the combined privileges of both roles, simplifying privilege
management and enhancing the flexibility of your data operations.

While the EXECUTE AS USER option enables dynamic tables to refresh under the user’s role, all other operations on these dynamic tables adhere
to the standard privilege model.

### Key use cases

* **Manage multi-role privileges:** In situations where users have secondary roles, they can create and refresh a dynamic table using the
  combined privileges of their primary and secondary roles. This configuration ensures that the user who is refreshing the dynamic table has
  the necessary permissions to access all required resources, while maintaining consistency with existing role-based access controls.
* **Granular security and governance controls:** Users can configure optional security measures with additional options such as REQUIRE USER,
  where a dynamic table can’t run unless a user is specified.
* **Accountability for all operations:** All refreshes on an EXECUTE AS USER dynamic table are attributed to the configured user instead
  of the SYSTEM user. This attribution helps maintain a clear audit trail for all operations.

### Access control

The owner role of the dynamic table must be granted the IMPERSONATE privilege on the user specified by EXECUTE AS USER, and the specified user
must be granted the owner role of the dynamic table. If the IMPERSONATE privilege is revoked, the dynamic table refresh will fail and the
dynamic table might be [auto suspended](dynamic-tables-suspend-resume.html#label-dynamic-tables-manage-understanidng-auto-suspend).

When the dynamic table refreshes, the primary role of the refresh session is the owner role of the dynamic table, and the user’s default
secondary roles are activated. Users can switch primary roles with the USE ROLE command and adjust the secondary roles in the refresh session
with the USE SECONDARY ROLES command.

### Cross-product considerations

* **Data masking and row access policies:** Policies—for example, those using CURRENT\_USER()—evaluate based on the specified user and
  roles rather than the SYSTEM user.
* **Replication and failover:** The user name and role name are replicated to secondary deployments.

  If a user or role is not available on the secondary deployment, the user is marked as INVALID and refreshes will fail until fixed.

  Invalid secondary roles are skipped during execution if the remaining roles provide sufficient privileges.

### Examples

#### Configure a dynamic table to run refreshes as a user

The following example creates a dynamic table that executes refreshes as the specified user, with the primary role set to the owner role of
the dynamic table. Refreshes execute with any user-lineage parameters that the user has set.

If no option for secondary roles is explicitly specified, the refresh defaults to the user’s current session setting.

CopyExpand

```
CREATE DYNAMIC TABLE my_dynamic_table
  [ EXECUTE AS USER my_user_name
    [ USE SECONDARY ROLES { ALL | NONE | (<role1>, <role2>, ... ) } ]
  ]
```

Show lessSee more

Scroll to top

#### Set a secondary role for an existing dynamic table

The following example configures a dynamic table to execute as the specified user. If no specific secondary roles are selected, the refresh
process defaults to the current session’s active secondary roles. If the dynamic table is already set to execute as a specific user, this
command will update the configuration to execute as the user executing the ALTER DYNAMIC TABLE command.

Executing this command requires the OWNERSHIP privilege on the dynamic table.

CopyExpand

```
ALTER DYNAMIC TABLE my_dynamic_table SET
  EXECUTE AS USER my_user_name
  [ USE SECONDARY ROLES { ALL | NONE | (<role1>, <role2>, ... ) } ]
```

Show lessSee more

Scroll to top

#### Switch a dynamic table to execute as the SYSTEM user

The following example reverts a dynamic table to execute under the SYSTEM user using the owner role of the dynamic table.

Executing this command requires the OWNERSHIP privilege on the dynamic table.

CopyExpand

```
ALTER DYNAMIC TABLE my_dynamic_table UNSET EXECUTE AS USER;
```

Show lessSee more

Scroll to top

## Privileges to create a dynamic table

To create a dynamic table, you must use a role that has the following privileges:

| Privilege | Object |
| --- | --- |
| CREATE DYNAMIC TABLE | Schema in which you plan to create the dynamic table. |
| SELECT | Existing tables and views that you plan to query for the new dynamic table. |
| USAGE | Database and schema that you plan to use for the new dynamic table.  Warehouse that you plan to use to refresh the table.  Note  Although you can execute `CREATE DYNAMIC TABLE ... INITIALIZE = ON_SCHEDULE` with a secondary role that has the USAGE privilege, the dynamic table won’t successfully refresh if the primary role lacks this privilege, and therefore it won’t be initialized. |

See moreShow less

Expand

To create a dynamic table that depends on another dynamic table, you must use a role that has the following privileges:

| Privilege | Object |
| --- | --- |
| SELECT | Dynamic table you plan to query from to create the new dynamic table. |
| OPERATE | All upstream dynamic tables the new dynamic table depends on. Only required if you set the dynamic table to refresh synchronously at creation. |

See moreShow less

Expand

## Privileges to query a dynamic table

To query a dynamic table, you can use a role that has the [privileges to create a dynamic table](#label-dynamic-tables-privileges).
For scenarios where a user only needs to query a dynamic table - for example, a data analyst - use a role that has the following privileges:

| Privilege | Object |
| --- | --- |
| USAGE | Database and schema that contains the dynamic table.  Warehouse used to run the query. |
| SELECT | The dynamic table being queried. |

See moreShow less

Expand

## Privileges to alter a dynamic table

To alter a dynamic table, you must use a role that has either the OWNERSHIP or OPERATE privilege on that dynamic table.

If you have the OPERATE privilege on a dynamic table, you can do the following with the ALTER DYNAMIC TABLE command:

* Suspend a dynamic table using [ALTER … SUSPEND](../sql-reference/sql/alter-dynamic-table.html#label-alter-dynamic-table-suspend-resume).
* Resume a dynamic table using [ALTER … RESUME](../sql-reference/sql/alter-dynamic-table.html#label-alter-dynamic-table-suspend-resume).
* Refresh a dynamic table using [ALTER … REFRESH](../sql-reference/sql/alter-dynamic-table.html#label-alter-dynamic-table-refresh).
* Set or change the warehouse and/or target lag using [ALTER … SET](../sql-reference/sql/alter-dynamic-table.html#label-alter-dynamic-table-set-property).

If you have the OWNERSHIP privilege on a dynamic table, you can do the following in addition to the operations listed above:

* Set or unset a comment using [ALTER … SET | UNSET COMMENT](../sql-reference/sql/alter-dynamic-table.html#label-alter-dynamic-table-tablecommentaction).
* Rename a dynamic table using [ALTER … RENAME TO](../sql-reference/sql/alter-dynamic-table.html#label-alter-dynamic-table-rename-to).
* Swap a dynamic table with another using [ALTER … SWAP WITH](../sql-reference/sql/alter-dynamic-table.html#label-alter-dynamic-table-swap)
* Set a new parameter using [ALTER … SET](../sql-reference/sql/alter-dynamic-table.html#label-alter-dynamic-table-set-property)
* Specify or drop clustering keys. See [Clustering actions (clusteringAction)](../sql-reference/sql/alter-dynamic-table.html#label-alter-dynamic-table-clusteringaction).
* Change governance policies. See [Data Governance policy and tag actions (dataGovnPolicyTagAction)](../sql-reference/sql/alter-dynamic-table.html#label-alter-dynamic-table-datagovpolicytagaction).
* Change search optimization. See [Search optimization actions (searchOptimizationAction)](../sql-reference/sql/alter-dynamic-table.html#label-alter-dynamic-table-searchoptimizationaction).

## Privileges to view a dynamic table’s metadata

To view metadata, you must use a role that has the MONITOR privilege on that dynamic table.

For scenarios where the user only needs to view the metadata and Information Schema of a dynamic table (for example, roles held by data
scientists), use a role that has the MONITOR privilege on that dynamic table. While the OPERATE privilege grants this access, it also includes
the capability to alter dynamic tables, making MONITOR the more suitable option for scenarios where a user does not need to alter a dynamic
table.

If you have the MONITOR privilege on a dynamic table, you can do the following:

* Use the [DESCRIBE DYNAMIC TABLE](../sql-reference/sql/desc-dynamic-table) command and Snowsight dynamic tables details page to view the specific details
  for a dynamic table. The following fields are hidden if you only have the SELECT privilege on a dynamic table: `text`, `warehouse`,
  `scheduling_state`, `last_suspended_on`, and `suspend_reason_code` (UI-only).
* Use the [SHOW DYNAMIC TABLES](../sql-reference/sql/show-dynamic-tables) command to view which dynamic tables you have access to.
* Call the [DYNAMIC\_TABLE\_GRAPH\_HISTORY](../sql-reference/functions/dynamic_table_graph_history) table function to view graph history.
* Call the [DYNAMIC\_TABLE\_REFRESH\_HISTORY](../sql-reference/functions/dynamic_table_refresh_history) table function to view refresh history.

## Privileges to drop a dynamic table

To drop a dynamic table, you must use a role that has the [OWNERSHIP](../sql-reference/sql/grant-ownership) privilege on that dynamic
table.

## Privileges to use dual warehouses

All privilege requirements for using INITIALIZATION\_WAREHOUSE are the same as WAREHOUSE.

| Operation | Privilege |
| --- | --- |
| CREATE DYNAMIC TABLE using INITIALIZATION\_WAREHOUSE | CREATE DYNAMIC TABLE and USAGE on both warehouses, the WAREHOUSE and INITIALIZATION\_WAREHOUSE. |
| ALTER DYNAMIC TABLE … SET / UNSET INITIALIZATION\_WAREHOUSE | OWNERSHIP or OPERATE on the dynamic table and USAGE on the applicable warehouse. |
| ALTER DYNAMIC TABLE … REFRESH on a dynamic table that uses INITIALIZATION\_WAREHOUSE | OPERATE on the dynamic table and USAGE on the applicable warehouse. |

See moreShow less

Expand

For more information, see [Understand warehouse usage for dynamic tables](dynamic-tables-warehouses).
