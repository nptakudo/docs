---
title: "Create a Snowflake Open Catalog account"
url: "https://docs.snowflake.com/en/user-guide/opencatalog/create-open-catalog-account"
---

# Create a Snowflake Open Catalog account

Feature — Generally Available

Not available in government regions.

If you’re an existing Snowflake customer, you can sign up for Snowflake Open Catalog by using Snowsight or the CREATE ACCOUNT Snowflake SQL
command.

You typically only need one Open Catalog account for your organization. However, you can create multiple accounts, if needed.

**Note**

> To create an Open Catalog account, you must be a user with the organization administrator (ORGADMIN) role.

## Create an account by using Snowsight

To create an Open Catalog account by using Snowsight, do the following:

1. Sign in to Snowsight.
2. Select **Admin** > **Accounts**.
3. In the **+ Account** drop-down, select **Create Snowflake Open Catalog Account**.
4. In the **Create Snowflake Open Catalog Account** dialog, complete the fields:

   * **Cloud:** The cloud provider where you want to store Apache Iceberg™
     tables.
   * **Region:** The region where you want to store Iceberg tables.
   * **Edition:** The edition for your Open Catalog account.
5. Select **Next**.
6. In the **Create New Account** dialog, complete the **Account Name**, **User Name**, **Password**, and **Email** fields.
7. Select **Create Account**.

   Your new Open Catalog account is created and a confirmation box appears.
8. In the confirmation box, select the **Account Locator URL** to open
   the Account Locator URL in your web browser.
9. Bookmark the Account Locator URL. When signing in to Open
   Catalog, you must specify the Account Locator URL.

## Create an account by using Snowflake SQL

To create an Open Catalog account by using Snowflake SQL, run the following
CREATE ACCOUNT SQL command:

CopyExpand

```
CREATE ACCOUNT <account_name>
ADMIN_NAME = <admin_user_name>
ADMIN_PASSWORD = '<admin_user_password>'
MUST_CHANGE_PASSWORD = { TRUE | FALSE }
EMAIL = '<admin_user_email>'
EDITION = standard
REGION = <cloud_region>
POLARIS = TRUE;
```

Show lessSee more

Scroll to top

For more information, see [CREATE ACCOUNT](https://docs.snowflake.com/en/sql-reference/sql/create-account).

**Important**

> After you run the CREATE ACCOUNT SQL command, copy the accountLocatorUrl in the command output and save it for signing in to Open Catalog.

![accountLocatorUrl for a Snowflake Open Catalog account.](../../_images/account-locator-url.png)
