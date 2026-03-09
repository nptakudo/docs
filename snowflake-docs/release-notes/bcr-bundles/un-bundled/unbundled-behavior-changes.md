---
title: "Unbundled behavior changes"
url: "https://docs.snowflake.com/en/release-notes/bcr-bundles/un-bundled/unbundled-behavior-changes"
---

# Unbundled behavior changes

Unbundled behavior changes are not associated or released with a behavior change bundle.

To help you manage your operations and minimize disruption to your Snowflake service, we document behavior changes that may impact your usage,
including:

* [Recently implemented changes](#label-unbundled-changes-recently-implemented-changes) that were previously pending/disabled, were not part of a behavior change bundle, and cannot be disabled.
* [Upcoming pending changes](#label-unbundled-changes-upcoming-pending-changes) that will not be part of a behavior change bundle and cannot be enabled in advance.
* [Canceled behavior changes](unbundled-cancelled-behavior-changes.html#label-unbundled-changes-canceled-changes) that have been removed from BCR bundles and will not be implemented.

If you have questions about any of these behavior changes, please feel free to contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

## Recently implemented changes

This table lists behavior changes that were implemented independently of a behavior change bundle.
These changes include certain types of changes to Snowflake clients (connectors, drivers, etc.), platforms, and libraries. Such changes cannot be disabled.

See also [Upcoming pending changes](#label-unbundled-changes-upcoming-pending-changes).

| Release Date | Functional Area | Implemented Behavior Change | Additional Notes |
| --- | --- | --- | --- |
| **Weeks of December 2, 2025 (early adopters) and January 7, 2026 (late adopters)** | Snowsight Templates learning environment | [Snowsight Templates learning environment](bcr-1992) | This change is being rolled out gradually. |
| **Week of November 17, 2025** | Data quality monitoring | [Data quality: DATA\_METRIC\_USER database role granted to the PUBLIC role](bcr-2155) |  |
| **Week of November 10, 2025** | Native Apps | [Snowflake Native Apps: Changes to restrictions on version name, setup file name](bcr-2169) |  |
| **Week of November 10, 2025** | Security | [Authentication for local applications: Built-in security integration for Snowflake OAuth](bcr-2056) | This change is being rolled out gradually to all accounts. |
| **Week of August 4, 2025** | Data quality monitoring | [Data quality: DATA\_QUALITY\_MONITORING\_LOOKUP application role granted to the PUBLIC role](bcr-2068) |  |
| **Week of June 23, 2025** | Snowflake Native Apps | [Snowflake Native Apps: Changes to privileges commonly used by apps](bcr-1952) |  |
| **Week of March 17, 2025** | Data Lineage | [Data Lineage: VIEW LINEAGE privilege granted to the PUBLIC role](bcr-1933) |  |
| **Week of March 3, 2025** | Document AI | [Document AI: CREATE MODEL privilege required to create, publish, and train model builds](bcr-1904) |  |
| **Week of February 24, 2025** | Virtual Private Cloud IDs | [Amazon Virtual Private Cloud ID for external stage, external function, and external volume](bcr-vpc-change-2025-02-03) |  |
| **Week of January 20th, 2025** | Secure objects | [Secure objects: Redaction of information in error messages](bcr-1858) |  |
| **Week of June 17th, 2024** | HTTP Error Codes | [Change in HTTP error code for URL not found error](bcr-1669) |  |
| **May 10, 2024** | Snowflake Cortex ML Functions Changes | [Cortex ML Functions - New column in single-series Forecasting and Anomaly Detection results](bcr-cortex-forecast-anomaly-detection-series-column) |  |
| **March 26, 2024** | SQL Changes — Organization Usage Views | [Organization Usage: Updated billing views](bcr-1584) |  |
| **March 04-05, 2024** | Data Pipelines: Dynamic Tables | [Dynamic tables: Changes to ACCOUNT\_USAGE.TABLES and INFORMATION\_SCHEMA.TABLES](bcr-account-usage-and-info-schema-changes) |  |

See moreShow less

Expand

For additional released, but archived, unbundled BCRs see: [Archived implemented unbundled behavior changes](unbundled-behavior-changes-implemented-archive).

## Upcoming pending changes

The following table lists unbundled behavior changes that are pending.
Pending unbundled behavior changes have not been released yet.

Important

All information in this table, including planned versions and dates, is subject to change; the information is provided only as a guideline
for any updates you need to make to accommodate the changes.

If a link is not provided to the individual pending behavior change, the release in which the bundle was introduced has not started or is
still in progress.

| Planned Release | Functional Area | Pending Behavior Change | Additional Notes |
| --- | --- | --- | --- |
| This change is planned for February 2026. | Network Connectivity | [GCP PSC propagated connection limit set to 0](bcr-2193) |  |
|  | Virtual Warehouses | [Warehouses: Enable QAS by default for newly created Gen2 and multi-cluster warehouses (Postponed)](bcr-2113) | This behavior change was originally in the 2025\_07 bundle. It has been postponed and a new release date has not been determined. |
| This change is planned for March 2026. | SQL Changes - General | [SQL general: New default column sizes for string and binary data types (Postponed)](bcr-2118) |  |
| This change is planned for February 28, 2026. | Document AI decommission | [Document AI decommission (Pending)](bcr-2156) |  |
| This change is planned for October 2025. | Snowflake Native Apps: Decommissioning of Python versions 3.8 and 3.9 | [Snowflake Native Apps: Deprecation of Python versions 3.8 and 3.9 (Pending)](bcr-2072) | Snowflake Native Apps will no longer support any decommissioned versions of Python when Python version 3.9 is deprecated in October, 2025. For more information on Snowflake’s Python runtime support policy, see [Snowflake Python Runtime Support](../../../developer-guide/python-runtime-support-policy). |
| This change will occur gradually. | Worksheets to Workspaces upgrade | [Defaulting accounts from Worksheets to Workspaces](bcr-2117) | This change will roll out gradually beginning the week of September 22, 2025. |
| This change is planned for September 2025. | New VNET subnet IDs required for rules that filter based on subnet ID | [Azure access: New VNET subnet IDs required for rules that filter based on subnet ID (Pending)](bcr-1955-2078) |  |
| This change will occur gradually across all AWS regions from January 5 - January 31 2025. | Change of Certificate Authority & OCSP Allowlist for AWS Customers | [Change of Certificate Authority and OCSP Allowlist for AWS Customers](bcr-1657) |  |
| This change will occur gradually across all regions from October - November 2024 (Azure & GCP), February 2025 (AWS). | TLS Cipher Suite Requirements | [Changes in TLS Cipher Suite Requirements](bcr-1727) |  |
|  | Snowflake CLI, Connectors, Drivers, and SQL API Changes | [SnowSQL- Change to the value of the sql\_split property (Pending)](bcr-792) |  |
|  | Account Usage and Information Schema views: Changes to DATA\_TYPE output for string columns. | [Account Usage and Information Schema views: Changes to DATA\_TYPE output for string columns (Postponed)](bcr-1960) | This behavior change was originally in the 2025\_03 bundle and intended to become enabled by default in the 2025\_04 bundle. However, it has been postponed and a new release date has not been determined.  This change is not available for testing. |
|  | SHOW FUNCTIONS and SHOW PROCEDURES commands. | [SHOW FUNCTIONS and SHOW PROCEDURES commands: The complete data type for arguments is displayed in output (Postponed)](bcr-1944) | This behavior change was originally in the 2025\_03 bundle and intended to become enabled by default in the 2025\_04 bundle. However, it has been postponed and a new release date has not been determined.  This change is not available for testing. |
|  | Data Loading / Unloading Changes. | [Data loading, data unloading, and file staging DML commands: Single-character pattern matches (Postponed)](bcr-209969) | This change was originally planned for February, 2021; however, it has been postponed and a new release date has not been determined.  This change is not available for testing. |
|  | Information Schema: TABLE\_PRIVILEGES view. | [TABLE\_PRIVILEGES View: Update GRANTOR column value in the consumer account (Postponed)](bcr-1321) | This behavior change was originally planned for **September, 2023**; however, it has been postponed and a new release date has not been determined.  This change is not available for testing. |
|  | Cloning: Table history. | [Cloning: Table history not preserved on clone (Postponed)](../2023_07/bcr-908) | This behavior change was originally in the 2023\_07 bundle and intended to become enabled by default in the 2023\_08 bundle. However, it has been postponed and a new release date has not been determined.  This change is not available for testing. |

See moreShow less

Expand
