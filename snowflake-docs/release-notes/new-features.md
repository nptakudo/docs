---
title: "Snowflake server release notes and feature updates"
url: "https://docs.snowflake.com/en/release-notes/new-features"
---

# Snowflake server release notes and feature updates

This topic lists the release notes for the most recent server releases and feature updates.
For earlier releases and feature updates, see [Server releases and feature updates earlier in 2026](new-features-2026).

Tip

To view a list of release note announcements, filtered by date and release type, see
[All release notes](/release-notes/all-release-notes).

If you have questions about any of these features, contact
[Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

## Upcoming (or in progress) server releases

* [10.8 Release Notes: Preview](2026/10_8)
  + [SQL updates](2026/10_8.html#sql-updates)
    - [User-defined types](2026/10_8.html#user-defined-types)
  + [Snowflake Cortex updates](2026/10_8.html#snowflake-cortex-updates)
    - [CKE document access history](2026/10_8.html#cke-document-access-history)
  + [Release notes change log](2026/10_8.html#release-notes-change-log)

## Recent server releases

* [10.7 Release Notes (with behavior changes): Mar 02, 2026-Mar 05, 2026](2026/10_7)
  + [Behavior change bundles](2026/10_7.html#behavior-change-bundles)
  + [Data lake updates](2026/10_7.html#data-lake-updates)
    - [Apache Iceberg™ tables: Support for fixed(L) data type](2026/10_7.html#iceberg-tm-tables-support-for-fixed-l-data-type)
  + [Release notes change log](2026/10_7.html#release-notes-change-log)
* [10.6 Release Notes: Feb 23, 2026-Feb 27, 2026](2026/10_6)
  + [Data lake updates](2026/10_6.html#data-lake-updates)
    - [Apache Iceberg™ tables: Partitioned writes with hierarchical paths (*Preview*)](2026/10_6.html#iceberg-tm-tables-partitioned-writes-with-hierarchical-paths-preview)
  + [Data governance updates](2026/10_6.html#data-governance-updates)
    - [Data quality: Non-owners can associate a data metric function with an object (*General availability*)](2026/10_6.html#data-quality-non-owners-can-associate-a-data-metric-function-with-an-object-general-availability)
  + [Release notes change log](2026/10_6.html#release-notes-change-log)
* [10.5 Release Notes: Feb 16, 2026-Feb 19, 2026](2026/10_5)
  + [Security updates](2026/10_5.html#security-updates)
    - [SAML2 federated authentication: Support for metadata URL](2026/10_5.html#saml2-federated-authentication-support-for-metadata-url)
    - [Tri-Secret Secure supports secure share area accounts](2026/10_5.html#tri-secret-secure-supports-secure-share-area-accounts)
  + [Data governance updates](2026/10_5.html#data-governance-updates)
    - [DUPLICATE\_COUNT DMF: Ability to specify multiple columns](2026/10_5.html#duplicate-count-dmf-ability-to-specify-multiple-columns)
  + [Release notes change log](2026/10_5.html#release-notes-change-log)

For earlier server releases, see [Server releases earlier in 2026](weekly-releases-2026).

## Recent feature updates

* [Mar 06, 2026: SYSTEM$GET\_CATALOG\_LINKED\_DATABASE\_CONFIG function (*General availability*)](2026/other/2026-03-06-system-get-catalog-linked-database-config)
* [Mar 05, 2026: AI\_COMPLETE document intelligence (*Preview*)](2026/other/2026-03-05-ai-complete-document-intelligence)
* [Mar 05, 2026: Snowflake Data Clean Rooms updates](2026/other/2026-03-05-dcr)
  + [Clean Rooms API Version: 13.5](2026/other/2026-03-05-dcr.html#clean-rooms-api-version-13-5)
* [Mar 05, 2026: Preventing a semantic view metric from being aggregated across specific dimensions](2026/other/2026-03-05-semantic-views-semi-additive-metrics)
* [Mar 05, 2026: Exporting a semantic view to a Tableau Data Source (TDS) file (*Preview*)](2026/other/2026-03-05-semantic-views-tableau-tds)
* [Mar 04, 2026: Support for Apache Iceberg™ version 3 (*Preview*)](2026/other/2026-03-04-iceberg-v3-support-preview)
* [Mar 2, 2026: Monitor and control Cortex AI Functions spending (*General availability*)](2026/other/2026-02-25-ai-functions-cost-management)
* [Mar 02, 2026: No limit on the number of backup sets per object](2026/other/2026-03-02-backups-no-limit-backup-sets)
* [Mar 02, 2026: Support for new dbt Core versions for dbt Projects on Snowflake](2026/other/2026-03-02-dbt-core-versions)
* [Mar 02, 2026: Simplified pricing for hybrid tables](2026/other/2026-03-02-hybrid-tables-pricing)
* [Mar 02, 2026: Query Delta-based Apache Iceberg™ tables with deletion vectors](2026/other/2026-03-02-iceberg-delta-deletion-vectors)
* [Mar 02, 2026: Using standard SQL clauses to query semantic views (*General availability*)](2026/other/2026-03-02-semantic-views-standard-sql)
* [Feb 27, 2026: Openflow Connector for Oracle (*General availability*)](2026/other/2026-02-27-openflow-oracle-ga)
* [Feb 27, 2026: Restricted caller’s rights in Streamlit in Snowflake (*Preview*)](2026/other/2026-02-27-sis-restricted-callers-rights)
* [Feb 26, 2026: Snowflake Data Clean Rooms updates](2026/other/2026-02-26-dcr)
  + [Clean Rooms API Version: 13.4](2026/other/2026-02-26-dcr.html#clean-rooms-api-version-13-4)
* [Feb 25, 2026: Account Usage CORTEX\_AGENT\_USAGE\_HISTORY view (*General availability*)](2026/other/2026-02-25-cortex-agent-usage-history-view)
* [Feb 25, 2026: Joining logical tables that contain ranges of values in a semantic view (*Preview*)](2026/other/2026-02-25-semantic-views-range-joins)
* [Feb 25, 2026: Account Usage SNOWFLAKE\_INTELLIGENCE\_USAGE\_HISTORY view (*General availability*)](2026/other/2026-02-25-snowflake-intelligence-usage-history-view)
* [Feb 24, 2026: View invoices in Snowsight](2026/other/2026-02-24-billing-invoices)
* [Feb 24, 2026: User-defined actions for budgets](2026/other/2026-02-24-budget-user-defined-actions)
* [Feb 24, 2026: Enforcement of privatelink-only access (*General availability*)](2026/other/2026-02-24-enforce-privatelink-access-only)
* [Feb 24, 2026: Snowflake Postgres (*General availability*)](2026/other/2026-02-24-snowflake-postgres-ga)
* [Feb 23, 2026: Simplified setup for Data Quality Monitoring](2026/other/2026-02-23-data-quality-monitoring-setup)
  + [Cortex Data Quality (*Preview*)](2026/other/2026-02-23-data-quality-monitoring-setup.html#cortex-data-quality-preview)
  + [User interface for creating data quality checks (*Preview*)](2026/other/2026-02-23-data-quality-monitoring-setup.html#user-interface-for-creating-data-quality-checks-preview)
* [Feb 23, 2026: Grouped Query History in Snowsight (*General availability*)](2026/other/2026-02-23-grouped-query-history-ui)
* [Feb 20, 2026: Snowflake Native Apps: Configuration (*Preview*)](2026/other/2026-02-20-nativeapps-configuration)
* [Feb 20, 2026: USE AI FUNCTIONS account privilege for Cortex AI Functions](2026/other/2026-02-20-use-ai-functions-privilege)
* [Feb 19, 2026: Snowflake Data Clean Rooms updates](2026/other/2026-02-19-dcr)
  + [Clean Rooms API Version: 13.3](2026/other/2026-02-19-dcr.html#clean-rooms-api-version-13-3)
* [Feb 19, 2026: Machine learning experiments (*General availability*)](2026/other/2026-02-19-ml-experiments-ga)
* [Feb 18, 2026: Snowflake Container Runtime versioning for ML Jobs (*Preview*)](2026/other/2026-02-18-container-runtime-versions-preview)
* [Feb 18, 2026: Account Usage New CORTEX\_AGENT\_USAGE\_HISTORY view (*Preview*)](2026/other/2026-02-18-cortex-agent-usage-history-view)
* [Feb 18, 2026: Support for changing refresh user and secondary roles](2026/other/2026-02-18-dynamic-tables-execute-as-user)
* [Feb 18, 2026: Row timestamps for pipeline latency and event tracking (*General availability*)](2026/other/2026-02-18-row-timestamps)
* [Feb 18, 2026: Account Usage New SNOWFLAKE\_INTELLIGENCE\_USAGE\_HISTORY view (*Preview*)](2026/other/2026-02-18-snowflake-intelligence-usage-history-view)
* [Feb 17, 2026: Access history improvements](2026/other/2026-02-17-access-history)
* [Feb 16, 2026: Sharing Streamlit in Snowflake apps (*Preview*)](2026/other/2026-02-16-sis)
* [Feb 13, 2026: Run Security Essentials scanners on demand](2026/other/2026-02-13-adhoc-security-essentials)
* [Feb 13, 2026: Snowflake Native Apps: Inter-App Communication (*Preview*)](2026/other/2026-02-13-nativeapps-iac)
* [Feb 12, 2026: Snowflake Data Clean Rooms updates](2026/other/2026-02-12-dcr)
  + [Clean Rooms API Version: 13.2](2026/other/2026-02-12-dcr.html#clean-rooms-api-version-13-2)
* [Feb 12, 2026: New checkout experience for private offers with flat-fee pricing (*General availability*)](2026/other/2026-02-12-marketplace-checkout-experience-ga)
* [Feb 12, 2026: Strong Authentication Hub (*Preview*)](2026/other/2026-02-12-strong-authentication-hub)
* [Feb 10, 2026: Snowflake Native Apps: Shareback (*General Availability*)](2026/other/2026-02-10-nativeapps-shareback)
* [Feb 09, 2026: Performance Explorer enhancements (*Preview*)](2026/other/2026-02-09-performance-explorer-enhancements-preview)

For earlier feature updates, see [Feature updates earlier in 2026](feature-releases-2026).
