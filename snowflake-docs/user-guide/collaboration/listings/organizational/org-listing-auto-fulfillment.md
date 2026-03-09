---
title: "Configure organizational listings for auto-fulfillment"
url: "https://docs.snowflake.com/en/user-guide/collaboration/listings/organizational/org-listing-auto-fulfillment"
---

# Configure organizational listings for auto-fulfillment

Auto-fulfillment ensures that data products in organizational listings are propagated across regions
automatically, eliminating the need for manual replication. This mechanism provides seamless regional
availability for data consumers, enhancing consistency and reducing administrative overhead in multi-region
data environments.

Before you begin, make sure you have the [necessary privileges](../../../../collaboration/provider-listings-auto-fulfillment-manage-privileges) to manage auto-fulfillment settings for organizational listings.

If your organization spans multiple regions, you can enable auto-fulfillment for your organizational listings
to ensure that data products are available in all regions where your organization has a presence.
Auto-fulfillment happens automatically if it’s enabled for your organization.

To find your account name (`account_name`), run this command:

CopyExpand

```
SHOW ACCOUNTS;
```

Show lessSee more

Scroll to top

To check if global data sharing is enabled for your organization account, run this command:

CopyExpand

```
SELECT SYSTEM$IS_GLOBAL_DATA_SHARING_ENABLED_FOR_ACCOUNT('<account_name>');
```

Show lessSee more

Scroll to top

To enable global data sharing for an organization account, run this command:

CopyExpand

```
CALL SYSTEM$ENABLE_GLOBAL_DATA_SHARING_FOR_ACCOUNT('<account_name>');
```

Show lessSee more

Scroll to top

To disable global data sharing for an organization account, run this command:

CopyExpand

```
CALL SYSTEM$DISABLE_GLOBAL_DATA_SHARING_FOR_ACCOUNT('<account_name>');
```

Show lessSee more

Scroll to top
