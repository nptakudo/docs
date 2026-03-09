---
title: "About Openflow - Snowflake Deployments"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/about-spcs"
---

# About Openflow - Snowflake Deployments

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../intro-regions.html#label-na-general-regions).

Openflow - Snowflake Deployment run on [Snowpark Container Services (SPCS)](../../../developer-guide/snowpark-container-services/overview) and
provide a streamlined and integrated solution for data integration and connectivity across interoperable storage like Iceberg and Snowflake native storage.
As a fully self-contained service within Snowflake, it’s easy to deploy and manage, offering a convenient and cost-effective environment for running your data flows.
A key advantage is its native integration with Snowflake’s security model, which allows seamless authentication, authorization, and network security, and simplified operations.

Although customers can have both BYOC and Snowflake Deployments, the following list use cases that are well-suited to Snowflake Deployments:

* Incorporating full-fidelity data in the bronze layer: Landing raw data from various sources directly into Snowflake and using Openflow Snowflake Deployments to extract and load.
* Enriching data: Running pipelines to enrich tables that already exist inside Snowflake.
* From ingest to insight in one place: Building applications where the entire data lifecycle (ingest, process and serve) happens within the Snowflake ecosystem.
* Transforming raw data to insights with AI: Ingesting unstructured data and then, for instance, using Snowflake Intelligence to search and understand it better, all in concert with users’ other structured data.
* Employing reverse ETL: Closing the loop on insight generation by sharing with external operational systems via APIs, messaging infrastructure, and more.

## Understanding Snowflake roles and External Access Integrations

Openflow - Snowflake Deployments must be able to interact with data sources and destinations
that are typically outside Snowflake. In addition these deployments must also be able
to communicate with and access Snowflake itself.
Snowflake roles and external access integrations provide this support.

### What is a Snowflake role?

A Snowflake role is a traditional Snowflake role, associated with a specific Openflow Runtime, and used for the following tasks:

* Grant access to external access integrations (EAIs).
  These EAIs specify rules that allow the runtime
  to access the data sources and destinations from within Snowflake itself.
* Grant access to Snowflake resources.
* Grant access to resources that are connector-specific

Snowflake roles are linked to Openflow session tokens, avoiding the need for customers
to create separate service users and key pairs for authentication to Snowflake.

### What is an External Access Integration(EAI) within Openflow?

An [External access integration](../../../developer-guide/external-network-access/external-network-access-overview) (EAI)
is a Snowflake object designed to provide secure access to external resources,
like source systems from which Openflow connectors pull external data.
Openflow Snowflake Deployments use EAIs and network rules together to define the
endpoints an Openflow connector can read from or write to.

Data engineers define and configure EAIs and Snowflake roles specific to a given connector and its underlying runtime.

## Typical Openflow - Snowflake Deployment workflow

The following sections describe Openflow - Snowflake Deployment concepts and workflows.

| User persona | Task |
| --- | --- |
| Snowflake administrator | * Configures core Snowflake and external access integrations.  See [Set up Openflow - Snowflake Deployment - Task overview](setup-openflow-spcs). * Creates a set of deployments in Snowflake.  The Openflow UI is used to manage deployments and runtime creation and maintenance. The Openflow UI   allows users to create, resize, upgrade, and delete runtimes in all deployments. |
| Data engineer (pipeline author, responsible for data ingestion) | * Works with a Snowflake administrator to configure required allow listed domains such   that Openflow - Snowflake Deployment can access the external data sources. * Creates Snowflake roles, external integrations, and other objects that can later be used by runtimes. * Uses the runtime canvas to build completely new flows or to configure deployed connectors.   Creates a completely new flow or uses an existing connector as-is or as a starting point to customize.  Connectors are a simple way to solve for a specific integration use case, and less technical users can deploy them without assistance from a data engineer. |
| Data engineer (pipeline operator) | Configures flow parameters and runs the flow. |
| Data engineer (responsible for transformation to silver and gold layers) | Responsible for transforming data from the bronze layer that was populated by the pipeline to silver and gold layers for analytics. |
| Business user | Makes use of gold layer objects for analytics. |

See moreShow less

Expand

## Limitations

* Openflow - Snowflake Deployment is not supported in trial accounts.
* Only a single Openflow - Snowflake Deployment is supported per account.
  However, an account can have many Openflow - Snowflake Deployment runtimes — each having a separate role and network access — which allows users to separate the workload.
* Users with a default role of ACCOUNTADMIN can’t login to Openflow - Snowflake Deployment runtimes and will get an error message when attempting to do so.
* Customers requiring private connectivity will need to configure [outbound PrivateLink](../../private-connectivity-outbound).
  Private Link is available to [Business Critical Edition](../../intro-editions.html#label-snowflake-editions-business-critical) only.

### Next steps

[Set up Openflow - Snowflake Deployment - Task overview](setup-openflow-spcs)
