---
title: "Share Data Products from SAP® Business Data Cloud to Snowflake"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/sap-sql/share-data-products"
---

# Share Data Products from SAP® Business Data Cloud to Snowflake

Feature — Limited General Availability

SAP® Business Data Cloud Connect for Snowflake is available by request through Snowflake. Please contact your Snowflake account manager to request access.

SAP® Snowflake is available by request through SAP®. Please contact your SAP® account manager to request access.

For more information on these solutions, see [Two Ways to Integrate Snowflake and SAP®](../about-sap-snowflake.html#label-two-ways-to-integrate-snowflake-and-sap).

The integration between SAP® and Snowflake relies on the
[catalog integration](#label-sap-snowflake-catalog-integration)
capability in Snowflake for zero-copy data sharing of Data Products
from SAP® Snowflake and SAP® Business Data Cloud Connect for Snowflake.

The steps to share Data Products from SAP® BDC to SAP® Snowflake accounts and
existing Snowflake accounts that use the SAP® BDC Connect for Snowflake are largely the same.

This topic describes the steps to create a catalog integration and share data products/

If you are using SAP® Snowflake, review the following section, as a reference.
if you are using SAP® BDC Connect for Snowflake, review and complete the steps in [SAP® Business Data Cloud Connect for Snowflake](#label-sap-business-data-cloud-connect-for-snowflake).

In this section you will:

1. Review [SAP® Snowflake](#label-sap-snowflake-catalog-integration) SAP® Snowflake, or
   [configure a catalog integration](#label-sap-business-data-cloud-connect-for-snowflake) for SAP® BDC Connect for Snowflake.
2. [In SAP® BDC, Choose Data Products to share with Snowflake](#label-sap-business-data-cloud-choose-data-products) to share data products with Snowflake.
3. If you are using SAP® Snowflake, [Create a Catalog Linked Database for shared Data Products](#label-sap-snowflake-create-catalog-linked-database)
   to create a catalog linked database for shared Data Products.

## SAP® Snowflake

As part of the provisioning process for a new SAP® Snowflake account, a catalog integration named `SAP_BDC_INTEGRATION` is automatically created in the SAP® Snowflake account and enrolled with SAP® Business Data Cloud. You can use this catalog integration to share data from SAP® Business Data Cloud or optionally create an additional catalog integration as described in the following section.

## SAP® Business Data Cloud Connect for Snowflake

Note

Before you can create a catalog integration with `SAP_BDC` as the `CATALOG_SOURCE`, you will need to accept
the SAP® BDC Connect for Snowflake Terms as an `ORGADMIN`.
Creating a catalog integration will fail with an error if these terms are not accepted.
An `ORGADMIN` needs to only do this once for the Snowflake organization.

To accept the SAP® BDC Connect for Snowflake Terms in Snowsight:

1. Sign in to Snowflake as a user with the `ORGADMIN` role.
2. Sign in to [Snowsight](../../../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in) as a user with the `ORGADMIN` role.
3. In the navigation menu, select Admin » Terms.
4. In the Snowflake Marketplace section, next to **SAP® BDC Connect for Snowflake Terms**, select Review.
5. select Acknowledge & Continue.

For existing Snowflake accounts that integrate with SAP® Business Data Cloud Connect for Snowflake, users need to first create and enroll a catalog integration prior to sharing data from SAP® Business Data Cloud to Snowflake.

To create and review the catalog integration, run the following command:

1. Create a Catalog Integration and enroll with SAP Business Data Cloud

> CopyExpand
>
> ```
> CREATE OR REPLACE CATALOG INTEGRATION MY_SAP_BDC_CATALOG_INT
>    CATALOG_SOURCE = SAP_BDC
>    TABLE_FORMAT = DELTA
>     REST_CONFIG = (
>       SAP_BDC_INVITATION_LINK = '<Invitation Link from SAP BDC>'
>       ACCESS_DELEGATION_MODE = VENDED_CREDENTIALS
>     )
>     ENABLED = TRUE
>     COMMENT = 'My SAP BDC catalog integration';
> ```
>
> Show lessSee more
>
> Scroll to top

1. Verify the catalog integration was created successfully>

   CopyExpand

   ```
   SHOW CATALOG INTEGRATIONS;
   ```

   Show lessSee more

   Scroll to top

> Which should produce results similar to:
>
> Expand
>
> ```
> MY_SAP_BDC_CATALOG_INT     CATALOG CATALOG true    2025-12-10 18:27:45.181 -0800
> ```
>
> Show lessSee more
>
> Scroll to top

## In SAP® BDC, Choose Data Products to share with Snowflake

In order to search for and share data products with Snowflake, the user must use the central SAP Business Data Cloud catalog and have a global role that grants them the following privileges:

* BDC Data Packages (read) - To access SAP Business Data Cloud.
* Catalog Asset (read) - To access the catalog and view objects in the Assets and Data Products collections.
* Cloud Data Product (share) - To share data products to target systems.

Users with these privileges can share data products from the SAP Business Data Cloud catalog with the desired SAP Snowflake account to make them available for consumption to specific roles in that account.

To share data products with Snowflake:

1. In the central SAP Business Data Cloud catalog, select data products to share with an SAP Snowflake account
2. From Catalog & Marketplace, search for (or use filters) to find the data products to be shared
3. From the search results, click the Share button in the data product to be shared (for example customer)
   to open the Manage Share Access dialog
4. In the Overview section, learn more about the data product by reviewing its details and available objects.
5. Under Target System:

   1. Choose the Snowflake account with the enrolled catalog integration to share with (if there is more than one).
   2. Click the Update button

A message appears letting you know that the share process has started. After the process finishes, a notification appears letting you know the result.

## Create a Catalog Linked Database for shared Data Products

If you are using SAP® Snowflake, you can create a catalog linked database for shared Data Products.

1. List shares available from SAP® Business Data Cloud for the enrolled catalog integration:

   CopyExpand

   ```
   SELECT SYSTEM$SAP_BDC_LIST_SHARES('MY_SAP_BDC_CATALOG_INT');
   ```

   Show lessSee more

   Scroll to top

> Which should produce results similar to:
>
> Expand
>
> ```
> ["usid:0c7785a5-951f-4f3c-9f9f-9df3a5524d84:ns:sap.s4com:r:cashflow:v:1",
>  "usid:0c7785a5-951f-4f3c-9f9f-9df3a5524d84:ns:sap.s4com:r:customer:v:1",
>  "usid:0c7785a5-951f-4f3c-9f9f-9df3a5524d84:ns:sap.s4com:r:entryviewjournalentry:v:1"]
> ```
>
> Show lessSee more
>
> Scroll to top

Each element represents a shared Data Product. The highlighted text is an example of the name of the Data Product shared from SAP® Business Data Cloud to Snowflake with the enrolled catalog integration `MY_SAP_BDC_CATALOG_INT`.

1. Create a catalog linked database for the shared data products:

   CopyExpand

   ```
   CREATE OR REPLACE DATABASE CUSTOMER
      LINKED_CATALOG = (
        CATALOG = MY_SAP_BDC_CATALOG_INT,
        CATALOG_NAME = 'shares/usid:0c7785a5-951f-4f3c-9f9f-9df3a5524d84:ns:sap.s4com:r:customer:v:1',
        ALLOWED_WRITE_OPERATIONS = NONE
      );
   ```

   Show lessSee more

   Scroll to top

   Which should produce results similar to:

   Expand

   ```
   Database CUSTOMER successfully created.
   ```

   Show lessSee more

   Scroll to top
2. Confirm link status

> CopyExpand
>
> ```
> SELECT SYSTEM$CATALOG_LINK_STATUS('CUSTOMER');
> ```
>
> Show lessSee more
>
> Scroll to top
>
> Which should produce results similar to:
>
> Expand
>
> ```
> {"failureDetails":[],"executionState":"RUNNING","lastLinkAttemptStartTime":"2025-12-17T21:13:29.611Z"}
> ```
>
> Show lessSee more
>
> Scroll to top

In this example, we only created a single catalog linked database `CUSTOMER`.
You can create additional catalog linked databases depending on Data Products shared
with the enrolled catalog integration in the Snowflake account.

## Next steps

After sharing data products, you can [Explore Data from SAP® Business Data Cloud](explore-data) the data that has been shared with Snowflake.
