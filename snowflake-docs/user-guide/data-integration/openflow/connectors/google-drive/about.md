---
title: "About Openflow Connector for Google Drive"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/google-drive/about"
---

# About Openflow Connector for Google Drive

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

The Openflow Connector for Google Drive connects a Google Workspace Shared Drive and Snowflake to ingest
files and user permissions and keeps them up to date. Openflow Connector for Google Drive also supports the
Cortex Search service and can make ingested files ready for conversational
analysis for use in AI Assistants using SQL, Python or REST APIs.

Use this connector if you’re looking to do the following:

* Ingest Google Drive content for your own custom processing in Snowflake
* Ingest Google Drive content and make it ready for chat in your AI assistants with Snowflake Cortex

## Limitations

1. [Cortex Parse Document limitations and requirements](../../../../snowflake-cortex/parse-document.html#label-parse-document-requirements).
2. [Cortex Search limitations](../../../../snowflake-cortex/cortex-search/cortex-search-overview.html#label-cortex-search-overview-limitations).
3. Changes caused by moving or renaming folders aren’t captured during
   incremental ingestion.
4. The connector supports only explicit Google Permissions for Users and
   Groups. It does not currently support authentication models for links
   shared with Anyone.
5. The connector ingests only the supported file types and ignores
   others.

Please note, the limitations are listed for the predefined versioned
flow. If the flow was customized, and it doesn’t use some of the
predefined components, the limitations related to these components won’t
apply.

## Next steps

[Set up the Openflow Connector for Google Drive](setup)
