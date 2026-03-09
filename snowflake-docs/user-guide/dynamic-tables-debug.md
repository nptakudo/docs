---
title: "Debugging dynamic tables"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-debug"
---

# Debugging dynamic tables

This topic addresses solutions for troubleshooting dynamic tables that don’t run as expected.

Some actions might be restricted due to limitations on using dynamic tables or if you don’t have the necessary privileges. For more
information, see [Dynamic table limitations](dynamic-tables-limitations) and [Dynamic table access control](dynamic-tables-privileges).

If you encounter an issue not listed here, contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

| Issue | Solution |
| --- | --- |
| I can’t see the metadata for my dynamic table. | To view the metadata and Information Schema of a dynamic table, you must use a role that has the MONITOR privilege on that dynamic table. For more information, see [Privileges to view a dynamic table’s metadata](dynamic-tables-privileges.html#label-dynamic-tables-privileges-view-metadata). |
| My dynamic table is suspended. | A dynamic table might be suspended for several reasons:   * It was suspended directly using the [ALTER DYNAMIC TABLE … SUSPEND](../sql-reference/sql/alter-dynamic-table.html#label-alter-dynamic-table-suspend-resume)   command. * It is downstream of a suspended dynamic table. * It failed to refresh five consecutive times (skips don’t contribute to this count). * It is a replicated dynamic table, either in a replication group or failover group.   See [Replication and dynamic tables](account-replication-considerations.html#label-replication-and-dynamic-tables). * It was cloned from a dynamic table that has one or more base tables dropped at the time of cloning.   To see the reason why your dynamic table was suspended, do the following:   1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in). 2. In the navigation menu, select Transformation » Dynamic tables. 3. Select your dynamic table and go to the Table Details tab. 4. Hover over Scheduling State under Details. A dialog detailing the reason and date of the suspension appears. |

See moreShow less

Expand
