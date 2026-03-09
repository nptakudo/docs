---
title: "Manage Openflow"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/manage"
---

# Manage Openflow

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../intro-regions.html#label-na-general-regions).

This topic describes the steps to manage Openflow components.

## Delete a deployment

Deleting a deployment removes the management compute pool and all deployment-level
configuration. You must delete all runtimes first. Any data or objects
already integrated into Snowflake aren’t affected.

Warning

Deleting a deployment can’t be undone. Before you delete, make sure all runtimes
have been removed and you no longer need the deployment configuration.

From the AWS Console:

1. Navigate to EC2 Instances.
2. Select the `openflow-agent-{deployment-key}` instance with your deployment key.
3. Click Connect at the top of the page.
4. Switch from EC2 Instance Connect to Connect using EC2 Instance Connect Endpoint. Leave the default EC2 Instance Connect Endpoint
   in place.
5. Click Connect. A new browser tab or window will appear with a
   command-line interface.
6. Run `./destroy.sh` from the shell.

   * This may take 20-30 minutes. If your connection is interrupted, the process continues running in the background.
   * You can log back in and view its status with the command: `journalctl -u docker -f -n 250`
   * The `destroy` process is complete when you see output of `delete successful`.
7. Navigate to
   [CloudFormation](https://us-east-1.console.aws.amazon.com/cloudformation/home)
   in the AWS Console for your region.
8. Delete the CloudFormation stack for your deployment.

From Snowsight:

1. In the navigation menu, select Ingestion » Openflow.
2. Select Launch Openflow.
3. Select the Deployments tab.
4. In the row of the deployment you want to delete, select the More options icon.
5. Select Delete.
6. In the confirmation dialog, type `delete` to confirm deletion.
7. Click Delete deployment.

## Upgrade a deployment

A deployment includes several components: the agent, deployment service, deployment UI,
runtime gateway, and runtime operator. You can upgrade via the UI or, for BYOC deployments,
via the deployment agent script. For details on what’s included in each release, see
[Openflow version history](version-history).

Note

Only the owner of a deployment can perform an upgrade.

### Upgrade from the UI

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Ingestion » Openflow.
3. Select Launch Openflow.
4. Select the Deployments tab.
5. Look for the upgrade arrow to the left of the deployment name. This indicates an upgrade is available.

   ![Deployments tab showing the upgrade arrow indicator next to a deployment name](../../../_images/upgrade-available-deployment.png)
6. Select [![Three vertical dots indicating more options](../../../_images/vertical-more-icon.png)](../../../_images/vertical-more-icon.png) next to the deployment » Upgrade.

### Upgrade via the deployment agent (BYOC)

For BYOC deployments, use the deployment agent script to upgrade the agent, deployment service, deployment UI, runtime gateway, and runtime operator.

#### Connect to the deployment agent

1. Navigate to Openflow.
2. Select the Deployments tab.
3. View your deployment details and note the deployment key.
4. In your AWS account, view the EC2 instances and filter using the deployment key.
5. Locate the deployment agent EC2 instance named `openflow-agent-{deployment-key}`.
6. Connect using EC2 Instance Connect Endpoint and accepting all defaults.
7. Run the remaining commands from the new browser tab or window that appears with a command-line interface.

#### Check for available upgrades

CopyExpand

```
cat ~/.upgrade
```

Show lessSee more

Scroll to top

The script will display the latest available version of the various deployment components.

If no upgrades are available, you will see an output similar to this:

Expand

```
AGENT_IMAGE_VERSION_UPGRADE=
OPERATOR_CHART_VERSION_UPGRADE=
GATEWAY_IMAGE_VERSION_UPGRADE=
DPS_CHART_VERSION_UPGRADE=
DPUI_CHART_VERSION_UPGRADE=
```

Show lessSee more

Scroll to top

Otherwise, you will see the version that upgraded components will use, such as:

Expand

```
AGENT_IMAGE_VERSION_UPGRADE=0.17.0
OPERATOR_CHART_VERSION_UPGRADE=0.31.0
GATEWAY_IMAGE_VERSION_UPGRADE=
DPS_CHART_VERSION_UPGRADE=
DPUI_CHART_VERSION_UPGRADE=
```

Show lessSee more

Scroll to top

#### Upgrading the AMI for the Openflow BYOC deployment

When you upgrade your Openflow BYOC deployment, Openflow will find and upgrade to the latest AMI for Amazon Linux 2023 recommended by
[AWS Systems Manager](https://aws.amazon.com/systems-manager/).

If a new AMI is found, it will restart all Openflow services in your deployment, and runtimes will be temporarily halted.
Openflow runtimes and connectors maintain data integrity across restarts automatically.

Snowflake does not automatically upgrade deployments. You determine upgrade timing and frequency.

#### Initiate the upgrade

If the output indicates that upgrades are available, run the following script to initiate the upgrade. Older Openflow deployments may use the script `upgrade-data-plane.sh` instead.

CopyExpand

```
./upgrade.sh
```

Show lessSee more

Scroll to top

You will see output similar to this:

Expand

```
openflow-data-plane-agent-aws is set to version 0.16.0
   Upgrade set to version 0.17.0
openflow-dataplane-service-chart is set to version 0.47.0
   No upgrade is available
openflow-dataplane-ui-chart is set to version 0.5.0
   No upgrade is available
openflow-runtime-gateway is set to version 2025.6.8.2
   No upgrade is available
runtime-operator-chart is set to version 0.30.0
   Upgrade set to version 0.31.0
```

Show lessSee more

Scroll to top

Then, you have two options:

* Wait for an automatic upgrade: The system will automatically initiate the upgrade process within approximately 10 minutes.
* Manual upgrade: To start the upgrade immediately, run the following command:

CopyExpand

```
./create.sh
```

Show lessSee more

Scroll to top

#### Monitor the upgrade process

To track the progress of the upgrade, use the `journalctl` command:

CopyExpand

```
journalctl -u openflow-apply-infrastructure -f -n 250
```

Show lessSee more

Scroll to top

#### Verify a successful upgrade

A successful upgrade will typically show output similar to this:

Expand

```
All resources applied successfully and log uploaded to s3
openflow-apply-infrastructure.service: Deactivated successfully
```

Show lessSee more

Scroll to top

## Upgrade a runtime

Snowflake periodically releases runtime updates that introduce new Openflow processors, newer versions of
existing processors, or new runtime functionality. When updates are available, an indicator
appears next to the runtime name in the UI. For details on what’s included in each release, see
[Openflow version history](version-history).

Note

Only the owner of a deployment can perform an upgrade.

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Ingestion » Openflow.
3. Select Launch Openflow.
4. Select the Runtimes tab.
5. Look for the upgrade arrow to the left of the runtime name. This indicates an upgrade is available.

   ![Runtimes tab showing the upgrade arrow indicator next to a runtime name](../../../_images/upgrade-available-runtime.png)
6. Select [![Three vertical dots indicating more options](../../../_images/vertical-more-icon.png)](../../../_images/vertical-more-icon.png) next to the runtime » Upgrade.

## Upgrade a connector

Connector updates are made available by Snowflake when functionality is added,
processing logic is improved, or new processor versions are used–for example, to add support for a new source API version.

When connector updates are available, you will see an Upgrade icon in your process group on the canvas.

Note

You can only upgrade connectors after you have [upgraded their runtime](#label-openflow-upgrading-a-runtime).

To upgrade a connector, do the following:

1. In the navigation menu, select Ingestion » Openflow.
2. Select Launch Openflow.
3. Select the Runtimes tab.
4. Select the runtime name, or select View Canvas in the More Options menu to navigate to the canvas.
5. Find the processor groups with a red upgrade arrow next to their names. For each of these groups, change the version:

   1. Recommended: Check to see whether the parameter uses a custom value for the Parameter context. If so, make a note of the custom value. You will need to reapply it after the upgrade.

      1. Right-click the process group and select Parameters.
      2. Select Parameters in the Parameter Contexts list.
      3. Select the Inheritance tab, and check if it uses custom values. If so, make a note of the custom values.
   2. Right-click the group and select Version » Change Version.
   3. Select the latest available version and select Change.
   4. Confirm that the connector was upgraded to the latest version. The upgraded version should show a green check mark.
   5. Confirm that all processors in the connector’s process group are running. If not, start them.

      You can also validate the version by hovering over the speech bubble at the bottom right of the process group.
   6. If you noted a custom parameter value in step 4, reapply the custom value. For more information, see [Openflow connectors](connectors/about-openflow-connectors).

### Configure Snowflake Connector Flow Registry

Important

Early preview releases of Openflow did not configure a runtime for connector upgrades.
If you don’t see the Version option when right clicking on a process group, you
have to configure the Snowflake Connector Flow Registry and manually enable version control for existing connectors.

To configure the Snowflake Connector Flow Registry, do the following:

1. Navigate to the canvas.
2. Click on the menu in the top right corner and select Controller Settings.
3. Switch to the Registry Clients tab.
4. Click the + icon to add a new Registry Client.
5. Select the ConnectorFlowRegistryClient and select Add.
6. Click More Options for the ConnectorFlowRegistryClient row and select Edit.
7. Enter `/nifi/configuration_resources/connector_flow_registry` as the value
   for Storage Location and select Apply.

After configuring the Snowflake Connector Flow Registry you can now enable version control for your existing connectors.

To enable version control for existing connectors, do the following:

1. Navigate to the canvas and locate the process group where you want to add version control.
2. Right click on the process group and select Version » Set Version.
3. In the Set Version dialog, choose the flow that matches your process group.

   For example, choose **sqlserver** if you are using the SQL Server connector.

   Note that flow names do not exactly match the connector name.
4. Select the latest version and then select Set version to enable version control.
5. From the canvas, right click on the process group again and select Version » Revert Local Changes
   to apply the latest connector version.
6. Review the list of changes and select Revert.
7. Confirm that your connector was upgraded to the latest version which should now show a green check mark.
   You can also validate the version by hovering over the speech bubble at the bottom right of the process group.
