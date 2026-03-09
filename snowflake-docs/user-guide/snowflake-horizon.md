---
title: "Snowflake Horizon Catalog"
url: "https://docs.snowflake.com/en/user-guide/snowflake-horizon"
---

# Snowflake Horizon Catalog

Horizon Catalog is the universal catalog for your entire data estate. It provides context and governance for AI, enables any architecture
across clouds and regions, works with any engine and data format — and has zero risk of vendor lock-in.

## Context for AI

[Snowflake Intelligence](snowflake-cortex/snowflake-intelligence) empowers users across the organization to engage with
data intuitively by allowing them to ask questions and immediately obtain answers, insights, and visualizations. Snowflake Intelligence brings all your
structured and unstructured data together and uses helpful [AI agents](snowflake-cortex/cortex-agents) that understand
your business through your [semantic views](views-semantic/overview) and search services. Powered by [Cortex AI Functions](snowflake-cortex/aisql), [Cortex Analyst](snowflake-cortex/cortex-analyst), and [Cortex Search](snowflake-cortex/cortex-search/cortex-search-overview), Snowflake Intelligence delivers clear, trustworthy insights while keeping everything secure and
fully governed inside Snowflake. With easy access to [leading models](snowflake-cortex/snowflake-intelligence/reference.html#label-snowflake-intelligence-models) and cross-region inference,
every user can explore, discover, and act with confidence.

## Easy and safe data discovery across clouds

Horizon Catalog gives users one place to find all data resources with consistent metadata about Snowflake data, Apache Iceberg™ data, and
external relational sources and BI tools. Horizon Catalog expands visibility through
[Internal Marketplace](collaboration/listings/organizational/org-listing-about) listings so teams can discover governed
data products without copying data. Horizon Catalog enforces [access control](security-access-control-overview), protects
sensitive fields with [dynamic data masking](security-column-intro), applies
[row access policies](security-row-intro), and [identifies sensitive data](classify-intro) through data
classification.

Horizon Catalog supports enforcing row access and data masking policies on Apache Iceberg tables that you query from Apache Spark™ through Snowflake Horizon
Catalog. For more information,
see [Enforce data protection policies when querying Apache Iceberg™ tables from Apache Spark™](tables-iceberg-query-using-external-query-engine-snowflake-horizon-enforce-access-policies).

## Enterprise-grade security and governance

Horizon Catalog makes it easy to keep data safe, consistent, and well understood across your entire organization. It applies
[sensitive data classification](classify-intro), [retention](tables-iceberg-metadata), and
[data access policies](security-access-control-overview) the same way everywhere, giving every engine a shared view of your
metadata, [lineage](ui-snowsight-lineage), and rules. With [access history](ui-snowsight-lineage) and
[Time Travel](data-time-travel), teams can confidently review past activity and data states. Governance flows naturally to
external storage, Iceberg tables, and the [Snowflake Marketplace](../collaboration/collaboration-marketplace-about), so shared data
products always carry their tags and permissions wherever they go.

## Interoperability without vendor lock-in

Horizon Catalog connects all compute engines and formats through one governed environment. It presents consistent metadata and permissions
to Snowflake, Spark, and engines that read [Apache Iceberg](tables-iceberg). Horizon Catalog governs data inside Snowflake and in
external storage through [external tables](tables-external-intro) and Iceberg tables. It carries governance into the
Marketplace by [sharing](../guides-overview-sharing) data products through
[internal exchanges](collaboration/listings/organizational/org-listing-about) while preserving tags and access rules.
Horizon Catalog ensures every engine sees the same definitions, lineage, and policy behavior.

## A central spot for managing business continuity

Within Horizon, you [manage](account-replication-failover-failback) primary and secondary environments with ease, keeping
them consistent through database and account replication so your data, policies, and configurations stay aligned across every region and
account.
