---
title: "About Snowflake Marketplace"
url: "https://docs.snowflake.com/en/collaboration/collaboration-marketplace-about"
---

# About Snowflake Marketplace

The [Snowflake Marketplace](https://app.snowflake.com/_deeplink/marketplace) is where you can explore, access, and provide listings to consumers. You can also use the Snowflake Marketplace to discover and access third-party data and services, as well as market your own data products across the Snowflake Data Cloud.

As a data provider, you can use listings on the Snowflake Marketplace to share curated data offerings with many consumers simultaneously, rather than maintain sharing relationships with each individual consumer. With [Paid listings](collaboration-listings-about.html#label-paid-listing), you can also charge for your data products.

As a consumer, you might use the data provided on the Snowflake Marketplace to explore and access the following:

* Historical data for research, forecasting, and machine learning.
* Up-to-date streaming data, such as current weather and traffic conditions.
* Specialized identity data for understanding subscribers and audience targets.
* New insights from unexpected sources of data.

The Snowflake Marketplace is available globally to all Snowflake accounts hosted on Amazon Web Services, Google Cloud, and Microsoft Azure, with the exception of Microsoft Azure Government. Support for Microsoft Azure Government is planned.

Note

If you’re using private connectivity to access the Snowflake Marketplace through [Snowsight](../user-guide/ui-snowsight-gs.html#label-snowsight-getting-started-sign-in), you must first create a CNAME
record, as described in the Snowflake documentation:

* [AWS PrivateLink and Snowflake](../user-guide/admin-security-privatelink)
* [Azure Private Link and Snowflake](../user-guide/privatelink-azure)
* [Google Cloud Private Service Connect and Snowflake](../user-guide/private-service-connect-google)

## What can I do in the Snowflake Marketplace?

After you join the Snowflake Marketplace, you can do the following:

* As a provider, you can do the following:

  + Publish listings for free-to-use datasets to generate interest and new opportunities among the Snowflake customer base.
  + Publish listings with samples of datasets that can be provided on request or customized for a specific consumer.
  + Share live datasets securely and in real-time without creating copies of the data or imposing data integration tasks on the consumer.
  + (Preview) Share public listings in Virtual Private Snowflake (VPS) deployments.
  + Eliminate the costs of building and maintaining APIs and data pipelines to deliver data to customers.

  For more information, see [Use listings as a provider](provider-becoming) and [Create and publish a listing](provider-listings-creating-publishing).
* As a consumer, you can do the following:

  + Discover and test third-party data sources.
  + Receive frictionless access to raw data products from vendors.
  + Combine new datasets with your existing data in Snowflake to derive new business insights.
  + Have datasets available instantly and updated continually for users.
  + Eliminate the costs of building and maintaining various APIs and data pipelines to load and update data.
  + Use the business intelligence (BI) tools of your choice.

  For more information, see [Use listings as a consumer](consumer-becoming) and [Explore listings](consumer-listings-exploring).

## Snowflake Marketplace version 2 listings in VPS deployments

[![Snowflake logo in black (no text)](../_images/logo-snowflake-black.png)](../_images/logo-snowflake-black.png) [Preview Feature](../release-notes/preview-features) — Open

Available to all accounts.

Providers can create [version 2 (V2) listings](collaboration-listings-about.html#label-v1-vs-v2-listings) (preview) in Snowflake Marketplace and offer those to specified consumers in VPS deployments using region groupings.

Available region groupings for VPS deployments include the following:

* AWS\_US\_EAST\_1 (“US East (N. Virginia)”)
* AWS\_US\_EAST\_2 (“US East (Ohio)”)
* AWS\_US\_WEST\_2 (“US West (Oregon)”)
* AWS\_EU\_WEST\_1 (“EU (Ireland)”)
* AWS\_EU\_WEST\_2 (“EU (London)”)
* AZURE\_EASTUS2 (“East US 2 (Virginia)”)
* AZURE\_CENTRALUS (“Central US (Iowa)”)

Note

Providers must add support to handle V2 listings (currently in preview) in any of their scripts before targeting region groups in a listing.

For more information on creating Snowflake Marketplace listings in VPS deployments, refer to the following topics:

* [As a Snowflake Marketplace provider, create a listing in a Virtual Private Snowflake (VPS) deployment](provider-listings-creating-publishing.html#label-listings-create-in-vps-deployment)
* [As a VPS provider, create a listing in Snowflake Marketplace](provider-listings-creating-publishing.html#label-listings-vps-provider-create-in-marketplace)

### Opt in to consume Snowflake Marketplace listings in a VPS deployment

To consume Snowflake Marketplace listings in a VPS deployment, consumers must first opt in to the feature. To opt in, contact [Create Support cases](../user-guide/ui-support.html#label-creating-support-cases). Enabling this feature can take from 1 to 3 business days.

After this feature is enabled on consumer accounts, consumers can access listings that have been shared with them. For more information, see [About accessing and consuming listings in VPS](virtual-private-snowflake/vps-collaboration-for-consumers).

### Limitations

* VPS providers can’t create monetized listings.
* VPS customers (providers and consumers) are only identified in the [LISTING\_TELEMETRY\_DAILY view](../sql-reference/data-sharing-usage/listing-telemetry-daily) when the EVENT\_TYPE is GET, REQUEST, or UNINSTALL.

  In these cases, the `region_group` field will be populated.

  Otherwise, such as when EVENT\_TYPE is LISTING\_CLICK or LISTING\_VIEW, the `region_group` field will be NULL.
* VPS customers (providers and consumers) can only consume app listings for allowlisted connector listings. The list of allowedlisted connectors includes the following:

  + [Snowflake Connector for Google Analytics Aggregate Data](../connectors/google/gaad/gaad-connector-about)
  + [Snowflake Connector for Google Analytics Raw Data](../connectors/google/gard/gard-connector-about)
  + [Snowflake Connector for ServiceNow®](../connectors/servicenow/about)
