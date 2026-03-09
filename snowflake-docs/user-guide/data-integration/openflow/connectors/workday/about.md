---
title: "About Openflow Connector for Workday"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/workday/about"
---

# About Openflow Connector for Workday

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) [Preview Feature](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/preview-terms-of-service/)

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

The Openflow Connector for Workday allows to ingest Workday reports into
Snowflake. It is built as the Apache NiFi flow and uses the RaaS
(Report-as-a-Service) API to fetch data from Workday. The connector
persists data in a dedicated table in the database and schema provided
in the configuration.

Use this connector if you’re looking to do the following:

* Get Workday data into Snowflake using Report-as-a-Service (RaaS) streams for enterprise-level analytics and planning

## Limitations

* Only advanced Workday reports are supported.
* Only reports in the JSON format are supported.
* All limitations of the RaaS API apply.
* The schema discovery is not supported - schema of a destination table
  is inferred based on data fetched from Workday.
* The incremental load is not supported - the connector uses the
  truncate & load ingestion strategy.

## Next steps

[Set up the Openflow Connector for Workday](setup)
