---
title: "Set up Openflow - Snowflake Deployment: Create runtime"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/setup-openflow-spcs-create-runtime"
---

# Set up Openflow - Snowflake Deployment: Create runtime

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../intro-regions.html#label-na-general-regions).

A runtime is a containerized Apache NiFi instance that executes your data integration flows –
connectors and custom flow definitions. Each runtime is isolated for security and resource
control, and can scale from one node up to fifty to handle varying data volumes.

To create a runtime in your Snowflake deployment:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Ingestion » Openflow.
3. Select Launch Openflow. A new tab opens for the Openflow canvas.
4. In Openflow Control Plane, select Create a runtime. The Create Runtime dialog box appears.
5. In the Create Runtime populate the following fields:

   | Field | Description |
   | --- | --- |
   | Runtime Name | Enter a name for your runtime. |
   | Deployment drop down | Choose the deployment previously created in [Set up Openflow - Snowflake Deployment: Create deployment](setup-openflow-spcs-deployment) |
   | Node Type | Choose a node type from the Node type drop-down list. This specifies the size of your nodes. |
   | Min/Max node | In the Min/Max node range selector, select a range. The minimum value specifies the number of nodes that the runtime starts with when idle and the maximum value specifies the number of nodes that the runtime can scale up to, in the event of high data volume or CPU load. |
   | Snowflake Role | Choose the Snowflake role previously created in [Set up Openflow - Snowflake Deployment: Create Snowflake role](setup-openflow-spcs-create-rr). |
   | Usage Roles | Optionally, select the roles created to grant usage to the runtime for required databases, schema, and table access. |
   | External Access Integrations | Optionally, select the previously created external access integrations to grant access to external resources. |

   See moreShow less

   Expand
6. Select Create. The runtime takes a couple of minutes to be created.

Once created, view your runtime by navigating to the Runtimes tab of the Openflow control plane.
Select the runtime to open the Openflow canvas.

## [Optional] Grant MONITOR privileges on the runtime

If you created a [monitoring role](setup-openflow-spcs-deployment.html#label-openflow-spcs-monitoring-role) when setting up your deployment, you can add the runtime to that role. This allows data engineers or operations teams to monitor the runtime without having the OPENFLOW\_ADMIN role.

* To add the runtime to the monitoring role, run the following code, replacing `<OPENFLOW_RUNTIME_NAME>` with the name of the Openflow runtime integration:

  CopyExpand

  ```
  USE ROLE OPENFLOW_ADMIN;

  GRANT MONITOR ON OPENFLOW RUNTIME INTEGRATION <OPENFLOW_RUNTIME_NAME> TO ROLE <OPENFLOW_MONITOR_ROLE>;
  ```

  Show lessSee more

  Scroll to top

## Next step

Configure allowed domains for Openflow connectors.
See [Set up Openflow - Snowflake Deployment: Configure allowed domains for Openflow connectors](setup-openflow-spcs-sf-allow-list).
