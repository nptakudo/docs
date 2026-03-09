---
title: "Drop an external volume by using Snowsight"
url: "https://docs.snowflake.com/en/user-guide/tables-iceberg-drop-external-volume"
---

# Drop an external volume by using Snowsight

[![Snowflake logo in black (no text)](../_images/logo-snowflake-black.png)](../_images/logo-snowflake-black.png) [Preview Feature](../release-notes/preview-features) — Open

Available to all accounts.

Dropping an external volume removes the [external volume](tables-iceberg.html#label-tables-iceberg-external-volume-def) from the account, but retains a version of the
external volume so that it can be recovered using [UNDROP EXTERNAL VOLUME](../sql-reference/sql/undrop-external-volume). For more information, see [Usage Notes for DROP EXTERNAL VOLUME](../sql-reference/sql/drop-external-volume.html#label-drop-external-volume-usage-notes).

Note

To drop an external volume by using SQL, use the [DROP EXTERNAL VOLUME](../sql-reference/sql/drop-external-volume) command.

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. Switch to a role that has OWNERSHIP privilege on the external volume you want to drop.

   For instructions, see [Switch your primary role](ui-snowsight-gs.html#label-switching-your-active-role).
3. In the navigation menu, select Catalog » External data.
4. Select the External volumes tab.
5. Select the external volume you want to drop.
6. Select … » Drop external volume.
7. Select Drop external volume again.
