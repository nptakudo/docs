---
title: "Troubleshooting Collaboration Data Clean Rooms"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/v2/troubleshooting"
---

# Troubleshooting Collaboration Data Clean Rooms

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) [Preview Feature](../../../release-notes/preview-features) — Open

Available to all accounts.

Required version

To use Collaboration Data Clean Rooms, you must upgrade to Snowflake Data Clean Rooms version 12.9 or later.

Consult the following troubleshooting tips when you encounter errors while you work with Collaboration Data Clean Rooms.

Error:
:   `Pending invitation for collaboration: <collaboration name> not found` although `GET_STATUS` shows the account as `INVITED`.

Cause:
:   If an initial join attempt has failed for some reason, later join attempts will likely fail with this reason.

Solution:
:   Delete and recreate the collaboration.

---

Error:
:   `Unknown user-defined function <function name>`

Cause:
:   If this is a procedure documented for the DCR Collaboration API, you might have misspelled the procedure.

    If you have not misspelled the procedure name, or if the procedure is a system procedure (that is, it has a `$` in the name), you might
    be using an older version of the API and need to upgrade your clean rooms API version.

Solution:
:   * Confirm that you spelled the procedure correctly, and if not, try again with the proper spelling.
    * To update your installation, run the following SQL code:

    CopyExpand

    ```
    USE ROLE ACCOUNTADMIN;
    CALL SAMOOHA_BY_SNOWFLAKE.APP_SCHEMA.PREPARE_MOUNT_SCRIPT();
    EXECUTE IMMEDIATE FROM @SAMOOHA_BY_SNOWFLAKE.APP_SCHEMA.MOUNT_CODE_STAGE/dcr_loader.sql;
    ```

    Show lessSee more

    Scroll to top

---

Error:
:   `Listing 'listing name' is not fulfilled to your current region. Please request the listing, or if already requested, retry after some time`

Cause:
:   You are using an older version of the clean rooms API. This issue was fixed in a more recent version.

Solution:
:   [Update your clean rooms installation.](../admin-tasks.html#label-dcr-updating-cleanrooms-environment)

---

Error:
:   `ReferenceUsageGrantMissingException: Reference usage grants are required for the following databases in your account ...` when a data provider tries to join the collaboration.
    Data providers will see this message when they try to join a collaboration, and they have shared data that they don’t have OWNERSHIP on.
    This is expected behavior.

Solution:
:   The error message includes a database name and a share name. Either someone with OWNERSHIP on the data, or an ACCOUNTADMIN, must run
    the following SQL command, providing the database and share names given in the error message:

    > CopyExpand
    >
    > ```
    > GRANT REFERENCE_USAGE ON DATABASE <database_name> TO SHARE <share_name>;
    > ```
    >
    > Show lessSee more
    >
    > Scroll to top
    >
    > After REFERENCE\_USAGE is successfully granted, the data provider can join the collaboration.
