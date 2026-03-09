---
title: "About Openflow Connector for Microsoft Dataverse"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/dataverse/about"
---

# About Openflow Connector for Microsoft Dataverse

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

The Openflow Connector for Microsoft Dataverse connects a Microsoft
Dataverse storage and Snowflake to ingest Microsoft Dataverse tables and
keeps them up to date on Snowflake side. The outcome of the connector
are selected tables replicated on Snowflake Account in a database and
schema specified by the user.

Use this connector if you’re looking to do the following:

* Integrate data from Microsoft Power Platform and Dynamics 365 applications with Snowflake for holistic business insights

## Rate limiting restrictions

[Microsoft Dataverse API limits](https://learn.microsoft.com/en-us/power-apps/developer/data-platform/api-limits?tabs=sdk#how-service-protection-api-limits-are-enforced) govern how many requests can be made within a given time frame. If your flow exceeds the allowed quota, syncs may slow down or fail with an error. This mostly occurs when your access token makes higher number of requests than the source typically allows. In such cases, we recommend applying for higher access quota (wherever applicable) or reducing the sync frequency.

### Limitations

* Only tables with enabled change tracking can be replicated
* Schema of destination tables is discovered from the database metadata
  through REST APIs. Whenever new columns are added to the table, they
  appear in the destination table. Changes and removals of columns are
  not reflected in the destination table.
* All [limitations of Microsoft Dataverse Web
  API](https://learn.microsoft.com/en-us/power-apps/maker/data-platform/api-limits-overview)
  apply.
* Supported set of column types is limited by set of types supported by
  [Snowpipe Streaming](../../../../snowpipe-streaming/data-load-snowpipe-streaming-overview.html#label-snowpipe-streaming-supported-java-data-types).
* Each instance of the connector supports a single schedule. If you need
  multiple schedules, then you need to install multiple instances of the
  connector.
* Empty tables are not replicated.
* Removal of a table is not replicated. If a table was replicated previously and is removed, it will remain in destination schema.
* Delta tokens used for change tracking expire after 7 days of inactivity by default. If the connector
  is not run for more than 7 days, the delta token expires and the connector must perform a full
  resync of the affected tables. This duration is controlled by the `ExpireChangeTrackingInDays`
  setting in the Microsoft Dataverse organization configuration.

### Next steps

[Set up the Openflow Connector for Microsoft Dataverse](setup)
