---
title: "Uninstalling the Snowflake Data Clean Rooms environment"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/uninstalling-clean-rooms"
---

# Uninstalling the Snowflake Data Clean Rooms environment

Feature — Generally Available

Currently available in [these regions](web-app-introduction.html#label-web-app-hosting).

Not available in government and VPS deployments.

To completely uninstall the clean room environment from your account, you must use the ACCOUNTADMIN role in the Snowflake account where the
clean room application is installed. This will delete the clean room environment for all users in your account, both as clean room
providers and clean room consumers.

Important

This procedure completely uninstalls the entire environment for your account, not just individual clean rooms.

To uninstall the clean room environment for your account:

1. [Delete all the clean rooms that you created as a provider.](manage-clean-rooms.html#label-dcr-single-cleanroom-delete)
2. [Uninstall all the clean rooms that you installed (joined) as a consumer.](manage-clean-rooms.html#label-dcr-single-cleanroom-unjoin)
3. [`Download the cleanroom uninstall notebook file`](../../_downloads/78a6e2a99ceb83d20fd7e15aca6ecbff/uninstall_dcr.ipynb) and
   [import it into Snowsight](../ui-snowsight/notebooks-create) in the account where you want to delete the clean rooms
   environment. You must be able to run the notebook by using the ACCOUNTADMIN role.
4. If you want to delete your organization from Snowflake Data Clean rooms, contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).
