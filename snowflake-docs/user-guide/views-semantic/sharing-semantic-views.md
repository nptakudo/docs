---
title: "Sharing semantic views"
url: "https://docs.snowflake.com/en/user-guide/views-semantic/sharing-semantic-views"
---

# Sharing semantic views

Providers can share semantic views in [private listings](../../collaboration/provider-listings-creating-publishing.html#label-listings-create), in public listings on the [Snowflake Marketplace](https://app.snowflake.com/_deeplink/marketplace), and in [organizational listings](../collaboration/listings/organizational/org-listing-about).

## Share a semantic view in a listing

The example below describes how to share a semantic view on the Snowflake Marketplace.

SnowsightSQL

To use Snowsight to share a semantic view, follow these steps:

Note

You can also attach a semantic view to a [private listing](../../collaboration/provider-listings-creating-publishing.html#label-listings-create) or an [organizational listing](../collaboration/listings/organizational/org-listing-create).

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Marketplace » Provider Studio.
3. Select Create Listing » Snowflake Marketplace.
4. In the Create Listing window, enter a name for your listing.
5. Enter a subtitle and select a profile for your listing.
6. Select + Add data product.
7. Click + Select and select the database and schema that has the semantic view or views that you want to share.
8. In the database, select the semantic view or views that you want to attach to the listing.
9. To create the share, select Done and then Save.
10. Fill in the remaining details for the listing. For more information about these fields, refer to [Configure listings](../../collaboration/provider-listings-reference).

    * Access type

      + Free to offer a data product that is freely available to consumers.
      + Limited trial to offer a trial of your data product, with unlimited access to the data product available on request.
    * Description
    * Data dictionary
    * Business needs
    * Quick Start Examples
    * Categories
    * Documentation
    * Legal Terms
    * Attributes
    * Region Availability
11. Select Submit for approval, and then select one of the following:

    * Publish once approved
    * Submit for approval only

To use SQL to share a semantic view, follow these steps:

1. To create a share for your listing, use the [CREATE SHARE](../../sql-reference/sql/create-share) command:

   CopyExpand

   ```
   CREATE SHARE my_share;
   ```

   Show lessSee more

   Scroll to top
2. To ensure that the tables referenced in the view are also shared, run the following [GRANT <privilege> … TO SHARE](../../sql-reference/sql/grant-privilege-share) commands:

   CopyExpand

   ```
   GRANT REFERENCES ON SEMANTIC VIEW my_view TO SHARE my_share;
   GRANT SELECT ON SEMANTIC VIEW my_view TO SHARE my_share;
   ```

   Show lessSee more

   Scroll to top
3. Semantic views reference underlying tables. To ensure that the necessary privileges are granted on these tables, run the following [GRANT <privilege> … TO SHARE](../../sql-reference/sql/grant-privilege-share) command:

   CopyExpand

   ```
   GRANT SELECT ON TABLE my_table TO SHARE my_share;
   ```

   Show lessSee more

   Scroll to top

   Repeat this step for each table used by the semantic view.
4. To identify the tables that are referenced, run the [DESCRIBE SEMANTIC VIEW](../../sql-reference/sql/desc-semantic-view) command:

   CopyExpand

   ```
   DESCRIBE SEMANTIC VIEW my_semantic_view;
   ```

   Show lessSee more

   Scroll to top
5. To create a new secure object in the current account, use the [CREATE LISTING](../../sql-reference/sql/create-listing) command and attach the semantic view to the listing.
