---
title: "Set up Openflow - Snowflake Deployment - Task overview"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/setup-openflow-spcs"
---

# Set up Openflow - Snowflake Deployment - Task overview

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../intro-regions.html#label-na-general-regions).

To setup an Openflow - Snowflake Deployment, perform the following tasks:

| Order | Task | Description | Persona |
| --- | --- | --- | --- |
| 1 | [Setup core Snowflake](setup-openflow-spcs-sf) | Before creating a deployment, you must configure core Snowflake which include an Openflow admin role, required privileges, and network configuration. | Snowflake administrator |
| 2 | Optionally [Set up PrivateLink UI access](setup-openflow-spcs-configure-pr-ui) | Configure PrivateLink to access the Snowflake Openflow Runtime UI using private connectivity. | Snowflake administrator |
| 3 | [Create deployment](setup-openflow-spcs-deployment) | After configuring core Snowflake, you then create an Openflow deployment.  Optionally, configure a Openflow-specific event table to store Openflow logs and metrics. | Deployment engineer, Snowflake administrator for event table configuration |
| 4 | [Create Snowflake role](setup-openflow-spcs-create-rr) | After creating an Openflow - Snowflake Deployment, you must create a Snowflake role and associated external access integrations. | Data engineer |
| 5 | [Create runtime](setup-openflow-spcs-create-runtime) | Create a runtime associated with the previously created Snowflake role. | Data engineer |
| 6 | [Configure allowed domains for Openflow connectors](setup-openflow-spcs-sf-allow-list) | Configure access to external domains for Openflow connectors. | Data engineer |
| 7 | [Connect your data sources using Openflow connectors](connectors/about-openflow-connectors) | Configure one or more connectors in the Openflow - Snowflake Deployment. | Data engineer |

See moreShow less

Expand

Note that steps 3, 4 and 5 are typically repeated for each connector you want to configure in a given deployment.

## Next steps

[Set up Openflow - Snowflake Deployment: Core Snowflake](setup-openflow-spcs-sf)
