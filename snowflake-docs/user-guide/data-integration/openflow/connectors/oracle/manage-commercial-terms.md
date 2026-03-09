---
title: "Openflow Connector for Oracle: Enable and manage commercial terms"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/oracle/manage-commercial-terms"
---

# Openflow Connector for Oracle: Enable and manage commercial terms

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

Note

The Openflow Connector for Oracle is also subject to additional terms of service beyond the standard
connector terms of service. For more information, see the
[Openflow Connector for Oracle Addendum](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/openflow-oracle-terms/).

This topic describes how to enable the Openflow Connector for Oracle in the list of available connectors and manage
the licensing lifecycle.

Note

This task must be performed by the organization administrator (ORGADMIN).

Setting up the Openflow Connector for Oracle is a two-stage process. First, enable Oracle XStream services to make
the connector available for installation. Then, finalize the license configuration after
the connector detects your source database inventory.

## Part 1: Enable service (pre-installation)

By default, the Openflow Connector for Oracle is not displayed in the list of available connectors. You must accept the
[Openflow Connector for Oracle Addendum](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/openflow-oracle-terms/)
terms to make it available for installation. This is required for all license models.

1. Sign in to [Snowsight](../../../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Admin » Terms.
3. Locate the item Oracle Connector Terms in the list.
4. Select Review & Enable.

After you complete these steps, the following changes take effect:

* The Openflow Connector for Oracle listing becomes visible in the list of available connectors.
* A new tab titled Openflow for Oracle appears in the Admin » Terms tab.

## Part 2: License setup and lifecycle

Complete the steps for the license model you selected during configuration:

* [Option A: Embedded license (Snowflake-provided)](#label-oracle-embedded-license-setup)
* [Option B: Independent license / BYOL](#label-oracle-byol-license-setup)

### Option A: Embedded license (Snowflake-provided)

For this licensing model, you must activate the trial to enable the connector.

Note

Even if you install the connector, data replication does not start until this step is complete.

#### Step 1: Start the trial (prerequisite)

To start the trial:

1. Sign in to [Snowsight](../../../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Admin » Terms.
3. Select Openflow for Oracle tab.
4. Locate the Trial Status card (status: “Ready to Activate”).
5. Select Start Trial.
6. Accept the terms to start the 60-day trial period.

Note

This action enables the captureChangeOracle processor, allowing it to connect to
your database.

#### Step 2: Configure connector

After starting the trial, install and configure the connector. For more information,
see [Configure the connector](setup-connector).

After the connector successfully connects to the source database, a subscription is
automatically created and displayed in the Openflow for Oracle dashboard.

#### Step 3: Verify inventory

1. Sign in to [Snowsight](../../../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Admin » Terms.
3. Select the Openflow for Oracle tab.
4. Review the Subscription Inventory section.
5. Verify that the CPU core count matches your physical source database hardware.
6. If the core count is incorrect, update the runtime configuration.

#### Step 4: Lifecycle management

For more information about the licensing models and terms, see
[Licensing models and critical constraints](about.html#label-oracle-licensing-models).

The following table describes the actions available at each stage of the embedded
license lifecycle.

| Stage | Action | Result |
| --- | --- | --- |
| Trial period (Day 1 to 60) | Select Cancel Trial in the Openflow for Oracle dashboard before Day 60. | Oracle XStream services stop. No charges are incurred. |
| 36-month commitment (Day 61+) | No action required. If the trial is not canceled, the non-cancelable 36-month term begins automatically on Day 61. | The license can’t be canceled during this period. If your Snowflake agreement is terminated, the full remaining balance is due immediately. |
| Post-term S&M renewal (after month 36) | The license fee drops to $0. The annual Support & Maintenance (S&M) fee continues. You may opt out of S&M renewal in the Openflow for Oracle dashboard. | If you opt out and S&M coverage expires, the connector is permanently locked. To resume, you must purchase a new embedded license, which resets the 36-month commitment. |

See moreShow less

Expand

### Option B: Independent license / BYOL

If you are using the independent license (Bring Your Own License), no prior trial activation
is required.

#### Step 1: Configure the connector

To set up the connector with the independent/BYOL license, follow the steps in
[Configure the connector](setup-connector).

#### Step 2: Verify inventory (recommended)

Verify that Snowflake has correctly identified your database inventory.

1. Sign in to [Snowsight](../../../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Admin » Terms.
3. Select the Openflow for Oracle tab.
4. Review the database inventory details.

Note

The Start Trial button does not appear for this license model, and the
36-month lifecycle rules do not apply. You are responsible for maintaining a valid
Oracle license that includes XStream entitlements.
