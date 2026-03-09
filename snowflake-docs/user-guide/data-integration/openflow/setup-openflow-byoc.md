---
title: "Set up Openflow - BYOC"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/setup-openflow-byoc"
---

# Set up Openflow - BYOC

Feature — Generally Available

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../intro-regions.html#label-na-general-regions).

This topic describes the steps to set up Openflow.

Setting up Openflow involves the following steps:

* [Create a deployment in your cloud](#create-a-deployment-in-your-cloud)
* [Create a Runtime environment in your cloud](#create-a-runtime-environment-in-your-cloud)

## Prerequisites

The prerequisites to be completed on your Snowflake and AWS accounts are as follows:

### Snowflake account

You’ll need to first define privileges at the Snowflake account level.

1. Run the following SQL commands to grant the required privileges to the Openflow admin role:

   CopyExpand

   ```
   USE ROLE ACCOUNTADMIN;

   GRANT CREATE OPENFLOW DATA PLANE INTEGRATION ON ACCOUNT TO ROLE $openflow_admin_role;
   GRANT CREATE OPENFLOW RUNTIME INTEGRATION ON ACCOUNT TO ROLE $openflow_admin_role;
   ```

   Show lessSee more

   Scroll to top

   The new privileges are assigned to the ACCOUNTADMIN role as part of the default set of privileges, and that role can grant the privileges to a role of their choosing for the Openflow admin role, denoted as $openflow\_admin\_role in the code.
2. Next, set `default_secondary_roles` to `ALL` for all Openflow users:

   1. Sign in to Snowflake with a role that your ACCOUNTADMIN assigned for using Openflow.

      This may not be any of the following roles: ACCOUNTADMIN, ORGADMIN, GLOBALORGADMIN, or SECURITYADMIN.

      If you see a blank screen or the error “message: Invalid consent request” when logging into Openflow, change your role to a role that is not one of these listed roles.

      For more information, see [Prerequisites](#prerequisites).
   2. Run the following code, replacing $openflow\_user for each Openflow user:
   > CopyExpand
   >
   > ```
   > USE ROLE ACCOUNTADMIN;
   > ALTER USER $openflow_user SET DEFAULT_SECONDARY_ROLES = ('ALL');
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
   >
   > This setting is required because Openflow actions are authorized by using any of the authenticated user’s roles, and not just the default role.

#### Deployment integration privileges

The deployment integration object represents a set of resources provisioned to deploy one or more Snowflake Openflow runtimes. For organizations bringing
their own cloud resources, the deployment integration object represents a managed Kubernetes cluster along with its associated nodes.

Users with the CREATE DATA PLANE INTEGRATION privilege on the Snowflake account can create and delete the deployment integration objects.

Additional privileges can be defined on deployment integration objects directly to support differentiation of access.

You can grant the following privileges on a deployment integration object:

* OWNERSHIP: Enables full control over deployment actions objects, including deletion of the deployment.
* USAGE: Enables creation of runtime child objects.

#### Runtime privileges

The runtime object represents a cluster of one or more Snowflake Openflow runtime servers, provisioned to run flow definitions. For Kubernetes deployments, the runtime object represents a stateful set of Snowflake Openflow runtime containers deployed in a namespace, along with supporting components.

Users with the OWNERSHIP privilege on the parent deployment integration object and the CREATE RUNTIME INTEGRATION account-level privilege can create runtime integration objects. Additional privileges can be defined on runtime integration objects directly to support differentiation of access.

You can grant the following privileges on a runtime integration object:

* OWNERSHIP: Enables full control over runtime actions, including deletion of the associated runtime and modification of runtime flow definitions.
* USAGE: Enables read access to the deployed runtime for observing health and status, without making any changes.

#### Snowflake role

A Snowflake role is a Snowflake role that is associated with a specific Openflow runtime and used for the following tasks:

* Grant access to Snowflake resources.
* Grant access to connector-specific resources

Snowflake roles are linked to Openflow Snowflake Managed Token, avoiding the need for customers to create separate service users and key pairs for authentication to Snowflake.

Note

<RUNTIMENAME> denotes the name of the associated runtime.

To create a Snowflake role:

1. Create the required Snowflake role.

   CopyExpand

   ```
   USE ROLE ACCOUNTADMIN;
   CREATE ROLE IF NOT EXISTS OPENFLOW_RUNTIME_ROLE_<RUNTIMENAME>
   ```

   Show lessSee more

   Scroll to top
2. Grant the Snowflake role access to a warehouse.
   Snowflake recommends using a dedicated warehouse for data ingestion.
   This warehouse should be used when configuring your connectors for runtimes where you will be using this Snowflake role.

   CopyExpand

   ```
   GRANT USAGE, OPERATE ON WAREHOUSE <OPENFLOW_INGEST_WAREHOUSE> TO ROLE OPENFLOW_RUNTIME_ROLE_<RUNTIMENAME>;
   ```

   Show lessSee more

   Scroll to top
3. Allow the Snowflake role to use, create or otherwise access Snowflake objects.

   > Note
   >
   > Depending on the Openflow connector being created the required underlying objects will vary.
   > The example below is for illustration purposes only.

   CopyExpand

   ```
   GRANT USAGE ON DATABASE <OPENFLOW_DATABASE> TO ROLE OPENFLOW_RUNTIME_ROLE_<RUNTIMENAME>;
   GRANT USAGE ON SCHEMA <OPENFLOW_SCHEMA> TO ROLE OPENFLOW_RUNTIME_ROLE_<RUNTIMENAME>;
   ```

   Show lessSee more

   Scroll to top
4. Allow the user to use the Snowflake role

   CopyExpand

   ```
   GRANT ROLE OPENFLOW_RUNTIME_ROLE_<RUNTIMENAME> TO USER <username>;
   ```

   Show lessSee more

   Scroll to top

#### Example for role setup

Consider a scenario where the following roles should be set up:

* **accountadmin:** Out-of-the box role from Snowflake, which has these two CREATE privileges:

  + CREATE OPENFLOW DATA PLANE INTEGRATION
  + CREATE OPENFLOW RUNTIME INTEGRATION
* **deployment\_manager:** Can create, manage, and delete deployments.
* **deployment1\_runtime\_manager\_1:** Can create a runtime only within deployment 1. It can modify and delete a runtime that it created within deployment 1, but not a runtime created by deployment1\_runtime\_manager\_2.
* **deployment1\_runtime\_manager\_2:** Can create a runtime only within deployment 1. It can modify and delete a runtime that it created within deployment 1, but not a runtime created by deployment1\_runtime\_manager\_1.
* **deployment1\_runtime\_viewer\_1:** Can view a runtime canvas within deployment 1 that was created by deployment1\_runtime\_manager\_1.
* **deployment1\_runtime\_viewer\_2:** Can view a runtime canvas within deployment 1 that was created by deployment1\_runtime\_manager\_2.
* **deployment2\_runtime\_manager:** Can create a runtime only within deployment 2.
* **deployment2\_runtime\_viewer:** Can view a runtime canvas within deployment 2.

To set up Openflow with these roles, follow these steps:

1. Create new roles and assign the relevant privileges:

   CopyExpand

   ```
   use role ACCOUNTADMIN;
   create role if not exists deployment_manager;
   create role if not exists deployment1_runtime_manager_1;
   create role if not exists deployment1_runtime_manager_2;
   create role if not exists deployment1_runtime_viewer_1;
   create role if not exists deployment1_runtime_viewer_2;
   create role if not exists deployment2_runtime_manager;
   create role if not exists deployment2_runtime_viewer;

   -- Assign create deployment privilege to roles. (This privilege cannot be granted in Openflow UI.)

   grant create openflow data plane integration on account to role deployment_manager;

   -- Assign create runtime privilege to roles. (This privilege cannot be granted in the Control Pane UI.)

   grant create openflow runtime integration on account to role deployment1_runtime_manager_1;
   grant create openflow runtime integration on account to role deployment1_runtime_manager_2;
   grant create openflow runtime integration on account to role deployment2_runtime_manager;

   -- Grant roles to users. (Repeat this step for each user.)

   grant role <role name> to user <username>;
   ```

   Show lessSee more

   Scroll to top
2. To create a deployment, follow these steps:

   1. Sign in to Snowsight as deployment\_manager.
   2. In the navigation menu, select Ingestion » Openflow.
   3. To create deployment 1, select Create a deployment, and grant the USAGE privilege to deployment1\_runtime\_manager\_1 and deployment1\_runtime\_manager\_2.
   4. To create deployment 2, select Create a deployment, and grant the USAGE privilege to deployment2\_runtime\_manager.
3. To create a runtime in deployment 1, follow these steps:

   1. Log in as deployment1\_runtime\_manager\_1.
   2. Create a runtime as described in the following sections. deployment1\_runtime\_manager\_1 should be able to create runtimes and manage any runtimes it created within this deployment.
   3. In the Openflow UI, select deployment1\_runtime\_viewer\_1 and grant it the USAGE privilege.

### AWS account

Ensure the following on your AWS account:

* You have an AWS account with permissions required to create a CloudFormation stack.
* An AWS administrator in your organization can execute CloudFormation script to set up Amazon Elastic Kubernetes Service (EKS) inside a new VPC (created by
  CloudFormation) or an existing VPC. See [Prerequisites for BYO-VPC (existing VPC)](#prerequisites-for-byo-vpc-existing-vpc).

Note

To learn about how the Openflow installation happens in your AWS account and the permissions that are configured by the CloudFormation template, see [Installation process](#installation-process).

#### Prerequisites for BYO-VPC (existing VPC)

If you want to use an existing VPC and your own subnets, ensure that you have the following:

* For Snowflake managed ingress, two public subnets with:

  > + Different availability zones
  > + At least /27 CIDR ranges with 32 available IPs.
  > + Routes for destination 0.0.0.0/0 and target internet gateway or some other egress routing to the internet.
  > + A tag that allows Openflow to create a load balancer:
  >
  >   > - Key: `kubernetes.io/role/elb`
  >   > - Value: `1`
  > + If your public subnets are used by other EKS clusters, a tag that allows Openflow to create a load balancer alongside other load balancers:
  >
  >   > - Key: `kubernetes.io/cluster/{deployment-key}`
  >   > - Value: `1`
  >
  > Note
  >
  > Managing your own ingress eliminates the need for public subnets, but requires additional configuration in your AWS account.
  > For more information, see [Openflow BYOC - Set up custom ingress](setup-openflow-byoc-custom-ingress).
* Two private subnets with:

  > + Different availability zones
  > + At least /24 CIDR ranges with 255 available IPs. This limits the number and
  >   scale of runtimes you can create, so it may be more appropriate to use a larger range for the deployment.
  > + Connectivity to Snowflake and AWS services from Private Subnet 1 where the Openflow deployment runs.
  >
  >   > - Among many options, you can connect using route tables with a NAT Gateway, a Transit Gateway, or PrivateLink VPC Endpoints.
  >   > - Without this connectivity, the Openflow deployment will not initialize or set up properly and no infrastructure will be provisioned.
  > + For Snowflake managed ingress, egress connectivity to [LetsEncrypt.org](https://letsencrypt.org), which will provision a TLS certificate.

## Accept the Openflow terms of service

This step is only required once for your organization.

1. Sign in to Snowflake as a user with the ORGADMIN role.
2. In the navigation menu, select Ingestion » Openflow.
3. Accept Openflow terms of services.

## Create a deployment in your cloud

### Configure the deployment in your Snowflake account

Important

Sign in to Snowflake with a role that your ACCOUNTADMIN assigned for using Openflow.

This may not be any of the following roles: ACCOUNTADMIN, ORGADMIN, GLOBALORGADMIN, or SECURITYADMIN.

If you see a blank screen, or the error: “message: Invalid consent request”, when logging into Openflow, change your role to a role that is not one of these listed roles.

For more information, see [Prerequisites](#prerequisites).

1. In the navigation menu, select Ingestion » Openflow.
2. Select Launch Openflow.
3. In the Openflow UI, select Create a deployment.
4. On the Deployments tab, select Create a deployment.
   The **Creating a deployment** wizard opens.
5. In the Prerequisites step, ensure that you meet all the requirements, and then select Next.
6. In the Deployment location step, select Amazon Web Services as the deployment location, enter a name for your deployment, and then select Next.
7. In the Configuration step, select one of the following options:

   * Managed VPC: Choose this option if you want your VPC to be managed by Snowflake.
   * Bring your own VPC: Choose this option if you want to use an existing VPC.

9. In the PrivateLink step, you can select if you want to establish communication with Snowflake over the private link.
   Enabling this option requires additional setup in your AWS account. For more information, see [AWS PrivateLink and Snowflake](../../admin-security-privatelink).

   * If the PrivateLink option is enabled, the End user authentication over PrivateLink step displays.

     + If enabled, browser-based authentication redirects use PrivateLink endpoints.
     + If disabled, end-user authentication uses public Snowflake URLs.

     Regardless of this setting, Deployment communications to Snowflake will use PrivateLink.

     If you access Snowsight through a PrivateLink URL, ensure it is enabled.
     If you access Snowsight through a non-PrivateLink URL, leave it disabled.
10. In the Custom Ingress step, you can choose to manage your own ingress configuration for the Openflow deployment, such as specifying custom security groups, load balancer settings, or other network controls.

    Enabling this option requires additional setup in your AWS account. For more information, see [Openflow BYOC - Set up custom ingress](setup-openflow-byoc-custom-ingress).
11. Select Create Deployment.
12. Once your deployment is configured, a dialog box appears that lets you download the CloudFormation template to complete the setup process in your AWS account. Download this template. Note that Openflow doesn’t support modifying the CloudFormation template. Don’t modify any values after downloading the template, other than choosing drop-down options.
13. (Optional) To encrypt EBS volumes for your Openflow BYOC deployment, see [Openflow BYOC - Set up encrypted EBS volumes](setup-openflow-byoc-encrypted-volumes).

### Apply the CloudFormation template in your AWS account

1. In your AWS account, create a new CloudFormation Stack using the template. After the Openflow deployment agent’s Amazon Elastic Compute Cloud (EC2) instance is created, it completes the rest of the [Installation process](#installation-process) using infrastructure as code scripts.
   You can track the installation progress as described in [Track the installation progress](#track-the-installation-progress).

   If you’re using an existing VPC, upon uploading the CloudFormation template, select the respective values in the drop-down lists for the two private subnets and your VPC.

### Create a network rule for Openflow in your Snowflake account

This step is required only if you’re using network policies to control access to Snowflake. A network policy is a set of rules that control which IP addresses can access your Snowflake account.

1. Navigate to your Snowflake account.
2. Identify the NAT gateway public IP address that was created as part of the CloudFormation stack. You can find this either by searching for NAT Gateway on AWS console or checking the output of the CloudFormation stack.

   The NAT gateway is responsible for Openflow egress for both the Data Plane Agent (DPA) and EKS. Both DPA and EKS run in the Private Subnet 1 of the installation.
3. Create a network rule for Openflow and add it to your existing network policy. Replace {$NAT\_GATEWAY\_PUBLIC\_IP} in the following code snippet with the NAT gateway public IP address that was created as part of the CloudFormation stack.

   CopyExpand

   ```
   USE ROLE ACCOUNTADMIN;
   USE DATABASE {REPLACE_WITH_YOUR_DB_NAME};

   CREATE NETWORK RULE allow_openflow_deployment
   MODE = INGRESS
   TYPE = IPV4
   VALUE_LIST = ('{$NAT_GATEWAY_PUBLIC_IP}/32');
   ```

   Show lessSee more

   Scroll to top
4. Find your currently active network policy.

   CopyExpand

   ```
   SHOW PARAMETERS LIKE 'NETWORK_POLICY' IN ACCOUNT;
   ```

   Show lessSee more

   Scroll to top
5. Copy the value column from the output, and use it to create a network rule:

   CopyExpand

   ```
   ALTER NETWORK POLICY {ENTER_YOUR_ACTIVE_NETWORK_POLICY_NAME} ADD ALLOWED_NETWORK_RULE_LIST = (allow_openflow_deployment);
   ```

   Show lessSee more

   Scroll to top

### Set up an event table to log Openflow events (required)

Use one of the following options to set up an event table:

* Create a new Openflow-specific event table (recommended):

  CopyExpand

  ```
  USE ROLE ACCOUNTADMIN;

  CREATE DATABASE IF NOT EXISTS openflow;
  USE openflow;
  CREATE SCHEMA IF NOT EXISTS openflow;
  USE SCHEMA openflow;

  GRANT CREATE EVENT TABLE
    ON SCHEMA openflow.openflow
    TO ROLE $role_of_deployment_owner;
  USE ROLE $role_of_deployment_owner;
  CREATE EVENT TABLE IF NOT EXISTS openflow.openflow.openflow_events;
  -- Find the Data Plane Integrations
  SHOW OPENFLOW DATA PLANE INTEGRATIONS;
  ALTER OPENFLOW DATA PLANE INTEGRATION
    $openflow_dataplane_name
    SET EVENT_TABLE = 'openflow.openflow.openflow_events';
  ```

  Show lessSee more

  Scroll to top
* Create an account-specific event table:

  CopyExpand

  ```
  USE DATABASE openflow;
  CREATE SCHEMA IF NOT EXISTS openflow.telemetry;
  CREATE EVENT TABLE IF NOT EXISTS openflow.telemetry.events;
  ALTER ACCOUNT SET EVENT_TABLE = openflow.telemetry.events;
  ```

  Show lessSee more

  Scroll to top
* Use an existing account-specific event table:

  CopyExpand

  ```
  USE ROLE ACCOUNTADMIN;
  ALTER ACCOUNT SET EVENT_TABLE = 'existing_database.existing_schema.existing_event_table';
  ```

  Show lessSee more

  Scroll to top

### Verify the deployment

1. In the navigation menu, select Ingestion » Openflow. Creating a deployment takes about 45 minutes on AWS. Once it’s created, you can view your deployment in the Deployments tab of Openflow UI with its state marked as Active.

## Create a runtime environment in your cloud

1. In Openflow Control Plane, select Create a runtime. The Create Runtime dialog box appears.
2. From the Deployment drop-down list, choose the deployment in which you want to create a runtime.
3. Enter a name for your runtime.
4. Choose a node type from the Node type drop-down list. This specifies the size of your nodes.
5. In the Min/Max node range selector, select a range. The minimum value specifies the
   number of nodes that the runtime starts with when idle and the maximum value specifies the
   number of nodes that the runtime can scale up to, in the event of high data volume or CPU load.
6. Select Create. The runtime takes a couple of minutes to get created.

Once created, you can view your runtime by navigating to the Runtimes tab of the Openflow control plane. Click the runtime to open the Openflow canvas.

## Next step

Deploy a connector in a runtime. For a list of connectors available in Openflow, see [Openflow connectors](connectors/about-openflow-connectors).

## Networking considerations: Openflow EKS to source systems

For BYOC deployments, take note of the following considerations:

* Openflow CloudFormation stack creates one VPC with two public subnets and two private subnets.
* Public subnets host the AWS Network Load Balancer, which is created later. Private subnets host the EKS Cluster and all of the EC2 instances backing the node groups. Openflow runtimes run within Private subnet 1.
* NAT Gateway is currently the egress for both DPA and EKS. Both DPA and EKS run in the Private subnet 1 of the installation.

For BYO-VPC deployments, take note of the following considerations:

* Openflow requires you to enter the two private subnets that will run Openflow and two public subnets for the AWS Load Balancer.
* You have to provide your own egress routing to the Internet from those private subnets, which can be the central NAT Gateway.
* No Internet Gateway is created by Openflow. You have to provide appropriate public internet egress routing.

The network connectivity generally is as follows:
**An Openflow EC2 Instance** (Agent or EKS) runs in a **private subnet** that requires **Route Table entries** to send egress traffic to a **Transit Gateway**, a **PrivateLink VPC Endpoint**, or a **NAT Gateway** connected to an **Internet Gateway**.

### Example: BYOC deployment with a new VPC to communicate with RDS in a different VPC of the same account

To enable communication between the Openflow EKS cluster and the RDS instance, you need to create a new
security group, with the EKS cluster security group as the source for the inbound rule for RDS connectivity, and attach the group in RDS.

1. Find the EKS cluster security group, navigate to EKS and find your deployment key.
   You can also find it on the Openflow UI by performing the following steps:

   1. Sign in to Openflow.
   2. Go to the Deployments tab.
   3. Select the More options icon next to your deployment.
   4. Select View details. The value in the field Key is your deployment key.
2. After finding the deployment key, you can use it to filter your AWS resources by the key value.
3. Create a new security group that allows access from the Openflow EKS cluster using the relevant database
   port. For PostgreSQL the default port is 5432.
4. Attach it in RDS as a new security group.

If you need to troubleshoot, the [Reachability Analyzer](https://docs.aws.amazon.com/vpc/latest/reachability/getting-started.html) can be useful.
It will give you detailed information about what may be blocking connectivity by using tracing capabilities within the AWS platform.

See the following AWS docs for accessing DB instances using VPC peering and the associated security group configuration:

* [Scenarios for accessing a DB instance in a VPC - Amazon Relational Database Service](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.Scenarios.html#USER_VPC.Scenario3)
* [Update your security groups to reference peer security groups - Amazon Virtual Private Cloud](https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-security-groups.html)

## Configuring PrivateLink in AWS

This section explains how to access and configure Openflow using private connectivity.

### Access Openflow over PrivateLink

Before starting with the private link configuration, enable PrivateLink for your account as described in [AWS PrivateLink and Snowflake](../../admin-security-privatelink).

1. Using the `ACCOUNTADMIN` role, call the `SYSTEM$GET_PRIVATELINK_CONFIG` function in your Snowflake account and identify the value for `openflow-privatelink-url`. This is the URL for accessing Openflow over PrivateLink.
2. Create a `CNAME` record in your DNS to resolve the URL value to your VPC endpoint.
3. Confirm that your DNS settings can resolve the value.
4. Confirm that you can connect to Openflow UI using this URL from your browser.

### Configure a new deployment using PrivateLink

Note

Snowflake recommends that you use the Bring your own VPC version of Openflow deployment and create a VPC endpoint in your VPC before applying the CloudFormation template.

Before starting with the PrivateLink configuration, make sure that PrivateLink is enabled for your account as described in [AWS PrivateLink and Snowflake](../../admin-security-privatelink).

Perform the following steps:

1. Retrieve Snowflake’s VPC endpoint service ID and Openflow PrivateLink URLs:

   > 1. Run the following SQL command using the `ACCOUNTADMIN` role:
   >
   >    > CopyExpand
   >    >
   >    > ```
   >    > SELECT SYSTEM$GET_PRIVATELINK_CONFIG()
   >    > ```
   >    >
   >    > Show lessSee more
   >    >
   >    > Scroll to top
   > 2. From the output, identify and save the values for the following keys:
   >
   >    * `privatelink-vpce-id`
   >    * `openflow-privatelink-url`
   >    * `external-telemetry-privatelink-url`
2. Create a VPC endpoint with parameters:

   > * Type: PrivateLink Ready partner services
   > * Service: `privatelink-vpce-id` value obtained in the previous step.
   > * VPC: The VPC where your Openflow deployment will be running.
   > * Subnets: Select two availability zones and private subnets where your Openflow deployment will be running.
3. Set up Route 53 private hosted zone with the following parameters:

   > 1. Domain: `privatelink.snowflakecomputing.com`
   > 2. Type: Private hosted zone
   > 3. Select the region and VPC where your Openflow deployment will be running.
4. Add two `CNAME` records for the URLs identified in the first step:

   > 1. For `openflow-privatelink-url`
   >
   >    > * Record name: `openflow-privatelink-url` value obtained in the first step
   >    > * Record type: `CNAME`
   >    > * Value: DNS name of your VPC endpoint
   > 2. For `external-telemetry-privatelink-url`
   >
   >    > * Record name: `external-telemetry-privatelink-url` value obtained in the first step
   >    > * Record type: `CNAME`
   >    > * Value: DNS name of your VPC endpoint
5. Create a dedicated security group for the deployment and enable traffic from the security group to the VPC endpoint:

   > 1. Open the security group associated with your VPC endpoint.
   > 2. Add an inbound rule to the security group that allows All traffic from the security group created for your deployment.
6. Create a new deployment and apply the CloudFormation Stack following the instructions in the [Create a deployment in your cloud](#create-a-deployment-in-your-cloud) section and ensure that:

   * The PrivateLink option is enabled. The End user authentication over PrivateLink option can be either enabled or disabled.
   * The security group created for the deployment is used when creating the CloudFormation stack.
7. Wait until the EKS cluster for your deployment is created. To confirm successful creation, navigate to AWS Console under Elastic Kubernetes Service. Verify that a cluster identified as `<deployment-key>` displays status ACTIVE.
8. Allow for traffic from your EKS to the VPC endpoint:

   > 1. Open the security group associated with your VPC endpoint.
   > 2. Add an inbound rule to the security group that allows All traffic from the security group assigned to your EKS cluster. The EKS cluster’s security group starts with `eks-cluster-sg-<deployment-key>-`.

### Configuring VPC Gateway Endpoints for S3 in AWS

Configuring an AWS VPC Gateway Endpoint for S3 is the primary method to allow an Agent EC2 instance in a private subnet to access the Amazon Linux 2023 repository privately,
without requiring an Internet Gateway, a NAT Gateway, or a public IP address on the instance. The Agent EC2 instance uses this repository to install its dependencies, for instance Docker.

To configure a VPC Gateway Endpoint for S3:

1. Open a browser to the AWS VPC dashboard.
2. In the navigation pane, select Endpoints.
3. Click Create endpoint and create a new VPC endpoint with parameters:

   > * Type: AWS services
   > * Service: `com.amazonaws.<your-region>.s3` of type `Gateway`
   > * VPC: Select the VPC of your deployment
   > * Route tables: Select the route table(s) that are associated with your private subnet(s)
   > * Policy: Choose Full access

## Configuring private deployments

Private deployments are a feature that allows you to deploy Openflow in a VPC without the need for public internet ingress or egress.

To configure private deployments, you need to choose the following options when creating a new deployment:

1. In the Deployment location step, select Amazon Web Services as the deployment location.
2. In the VPC Configuration step, select Bring your own VPC to use an existing VPC.
3. In the PrivateLink step, enable the PrivateLink feature. Enabling this option requires additional setup in your AWS account, see [Configuring PrivateLink in AWS](#configuring-privatelink-in-aws). The End user authentication over PrivateLink option can be either enabled or disabled.
4. In the Custom ingress step, enable the custom ingress feature. Enabling this option requires additional setup in your AWS account. For more information, see [Openflow BYOC - Set up custom ingress](setup-openflow-byoc-custom-ingress).

Private deployments require that your existing VPC is able to access the following domains:

* `*.amazonaws.com`, a detailed list of services being accessed includes:

  > + `com.amazonaws.iam`
  > + `com.amazonaws.<your-region>.s3`
  > + `com.amazonaws.<your-region>.ec2`
  > + `com.amazonaws.<your-region>.ecr.api`
  > + `com.amazonaws.<your-region>.ecr.dkr`
  > + `com.amazonaws.<your-region>.secretsmanager`
  > + `com.amazonaws.<your-region>.sts`
  > + `com.amazonaws.<your-region>.eks`
  > + `com.amazonaws.<your-region>.autoscaling`
* `*.privatelink.snowflakecomputing.com`
* `oidc-eks.<your-region>.api.aws`
* `shield.us-east-1.amazonaws.com`

## Installation process

Between the CloudFormation stack and the Openflow Agent, there are
several coordinated steps that the BYOC deployment installation process
manages. The goal is to separate responsibilities between a cold-start
that gives organizations an easy way to provide inputs to their BYOC
deployment (solved via CloudFormation), and the configuration of the
deployment and its core software components that will need to change
over time (solved by the Openflow Agent).

The deployment Agent facilitates the creation of the Openflow deployment infrastructure and
installation of the deployment software components including the deployment service. The deployment agent authenticates
with Snowflake System Image Registry to obtain Openflow container images.

The steps are as follows:

Note

When using BYO-VPC, you will choose a VPC ID and two private subnet IDs from the template, and
the CloudFormation stack will use the selected ones rather than creating the resources mentioned in steps 1a, 1b, and 1c.

1. The CloudFormation template creates the following and configures with the AWS permissions mentioned in [Configured AWS permissions](#configured-aws-permissions):

   1. One VPC with two public subnets and two private subnets. Public
      subnets host the AWS Network Load Balancer (created later).
      Private Subnets host the EKS cluster and all of the EC2 instances
      backing the NodeGroups. Openflow runtimes run within a private
      subnet.
   2. Internet Gateway for egress from the VPC
   3. NAT Gateway for egress from the private subnets
   4. AWS Secrets Manager entry for the OIDC configuration input by the user
   5. IAM role and instance profile for the Openflow Agent to use from its EC2 instance
   6. An EC2 instance for Openflow deployment agent, complete with a UserData
      script to automatically run the initialization process. This
      script sets environment variables for the Openflow deployment agent to use,
      derived from the input CloudFormation parameters.
   7. EC2 Instance Connect endpoint for the Openflow deployment agent to upgrade
      the deployment when needed.

      * When using BYO-VPC, by default the CloudFormation stack will create an EC2 Instance Connect endpoint. However, this default behavior can be modified. When using the managed VPC option, the CloudFormation stack will always create an EC2 Instance Connect endpoint.
      * The Instance Connect endpoint can be shared across many VPCs.
      * If a deployment is deleted, along with deleting the CloudFormation stack, it will also remove the endpoint. This would block access to other BYO-VPC agents if the endpoint is shared.
      * To add an EC2 Instance Connect endpoint, perform the following steps in your AWS account:

        1. In the left navigation, navigate to VPC » Endpoints.
        2. Select Create Endpoint.
        3. Choose the endpoint type as EC2 Instance Connect Endpoint.
        4. Select a VPC. Leave all the security groups clear (not selected) to use the default VPC security group.
        5. When selecting a subnet, use the same value as Private Subnet 1 in the CloudFormation parameters.
        6. Select Create. It takes approximately 5 minutes for the endpoint to be created.
   8. S3 Bucket that stores the Terraform state, logs, and outputs for
      the Openflow Agent
2. The Openflow deployment agent creates the following:

   1. An EKS cluster containing:
   > * Node groups
   > * Autoscaling groups
   > * AWS VPC Container Network Interface (CNI) add-on
   > * Amazon Elastic Block Store (EBS) CSI add-on

   1. Secrets manager records for PostgreSQL, OAuth credentials, and so on.
   2. IAM policies and roles for various K8s service accounts to
      retrieve their secrets from AWS Secrets Manager.
   3. K8s components
   > * Namespaces
   > * Cluster autoscaler
   > * EBS CSI expandable storage
   > * AWS Load Balancer Controller, which creates the publicly accessible Network Load Balancer
   > * Let’s Encrypt certificate issuer
   > * Nginx Ingress, configured for Let’s Encrypt
   > * Metrics Server
   > * Certificate manager from [Jetstack](http://jetstack.io/)
   > * [External secrets operator](http://external-secrets.io/)
   > * Service accounts for Temporal, deployment service, and OIDC
   > * Secrets stores for Temporal, deployment service, and OIDC
   > * External secrets for Temporal and deployment service. The external secret for OIDC is created and managed by the runtime operator.
   > * PostgreSQL
   > * Temporal
   > * Self-signed certificate issuer and ingress configuration for communications between runtime nodes
   > * Openflow runtime operator
   > * Openflow deployment service

By default, all AWS accounts have a quota of five Elastic IP addresses
per region, because public (IPv4) internet addresses are a scarce public
resource. Snowflake strongly recommends that you use Elastic IP
addresses primarily for their ability to remap the address to another
instance in the case of instance failure, and to use DNS hostnames for
all other inter-node communication.

### Track the installation progress

After the CloudFormation stack moves into the CREATE\_COMPLETE state, the Openflow agent automatically creates the rest of the infrastructure.

There are a few steps that can take 10-15 minutes each, such as:

1. Creating the EKS cluster
2. Installing the EBS CSI add-on to the EKS cluster
3. Creating the RDS PostgreSQL database

Status reporting for the Openflow agent is not available yet. In the meantime, you
can view logs on the Openflow agent to verify whether the BYOC deployment is ready for runtimes. To do this, perform the following steps:

1. In the EC2 instances list, locate the following two instances:

   * openflow-agent-{data-plane-key}: This is the Openflow agent that you will use to manage runtimes
   * {data-plane-key}-mgmt-group: This is a node in the BYOC deployment’s EKS cluster that runs an operator and other core software
2. Right-click on the openflow-agent-{data-plane-key} instance and select Connect.
3. Switch from EC2 Instance Connect to Connect using EC2 Instance Connect Endpoint. Leave the default EC2 Instance Connect Endpoint
   in place.
4. Click Connect. A new browser tab or window will appear with a
   command-line interface.
5. Run the following command to tail the installation logs of the docker image that is configuring your deployment:

   CopyExpand

   ```
   journalctl -xe -f -n 100 -u docker
   ```

   Show lessSee more

   Scroll to top
6. Once the installation is complete, you’ll see the following output:

   Expand

   ```
   {timestamp} - app stack applied successfully
   {timestamp} - All resources applied successfully
   ```

   Show lessSee more

   Scroll to top

### Configured AWS permissions

This section lists the AWS permissions configured by Openflow BYOC stack based on the roles.

Note

{key} represents the deployment key that uniquely identifies cloud resources created and managed by Openflow for a particular deployment.

**Administrative user**

`cloudformation` and all of the following permissions.

**IAM Role: openflow-agent-role-{key}**

CopyExpand

```
{
   "Version": "2012-10-17",
   "Statement": [
      {
            "Action": [
               "autoscaling:DescribeTags",
               "ec2:DescribeImages",
               "ec2:DescribeInstances",
               "ec2:DescribeLaunchTemplates",
               "ec2:DescribeLaunchTemplateVersions",
               "ec2:DescribeNetworkInterfaces",
               "ec2:DescribeSecurityGroups",
               "ec2:DescribeSubnets",
               "ec2:DescribeTags",
               "ec2:DescribeVolumes",
               "ec2:DescribeVpcs",
               "ec2:DescribeVpcAttribute",
               "iam:GetRole",
               "iam:GetOpenIDConnectProvider",
               "ecr:GetAuthorizationToken",
               "ec2:RunInstances",
               "ec2:CreateLaunchTemplate",
               "ec2:CreateSecurityGroup",
               "ec2:CreateTags",
               "ec2:DeleteTags"
            ],
            "Resource": "*",
            "Effect": "Allow"
      },
      {
            "Condition": {
               "StringLike": {
                  "aws:ResourceTag/Name": [
                        "{key}-oidc-provider"
                  ]
               }
            },
            "Action": [
               "iam:CreateOpenIDConnectProvider",
               "iam:DeleteOpenIDConnectProvider",
               "iam:TagOpenIDConnectProvider"
            ],
            "Resource": "arn:aws:iam::{Account_ID}:oidc-provider/oidc.eks.{Region}.amazonaws.com/id/*",
            "Effect": "Allow"
      },
      {
            "Action": [
               "iam:DeletePolicy",
               "iam:CreatePolicy",
               "iam:GetPolicy",
               "iam:GetPolicyVersion",
               "iam:ListPolicyVersions"
            ],
            "Resource": [
               "arn:aws:iam::{Account_ID}:policy/dp-service-role-policy-{key}",
               "arn:aws:iam::{Account_ID}:policy/oauth2-role-policy-{key}",
               "arn:aws:iam::{Account_ID}:policy/temporal-service-role-policy-{key}",
               "arn:aws:iam::{Account_ID}:policy/oidc-service-role-policy-{key}",
               "arn:aws:iam::{Account_ID}:policy/dps-temporal-role-policy-{key}"
               "arn:aws:iam::{Account_ID}:policy/dps-postgres-role-policy-{key}"
            ],
            "Effect": "Allow"
      },
      {
            "Action": [
               "iam:UpdateAssumeRolePolicy",
               "iam:PutRolePolicy",
               "iam:ListInstanceProfilesForRole",
               "iam:ListAttachedRolePolicies",
               "iam:ListRolePolicies",
               "iam:GetRolePolicy",
               "iam:CreateRole",
               "iam:AttachRolePolicy",
               "iam:DeleteRole",
               "iam:DeleteRolePolicy",
               "iam:DetachRolePolicy",
               "iam:TagRole"
            ],
            "Resource": [
               "arn:aws:iam::{Account_ID}:role/openflow-agent-role-{key}",
               "arn:aws:iam::{Account_ID}:role/{key}-*",
               "arn:aws:iam::{Account_ID}:role/dps-temporal-role-{key}",
               "arn:aws:iam::{Account_ID}:role/dps-postgres-role-{key}",
               "arn:aws:iam::{Account_ID}:role/dp-service-role-{key}",
               "arn:aws:iam::{Account_ID}:role/oauth2-role-{key}",
               "arn:aws:iam::{Account_ID}:role/oidc-service-role-{key}"
            ],
            "Effect": "Allow"
      },
      {
            "Action": [
               "autoscaling:CreateOrUpdateTags",
               "autoscaling:DeleteTags"
            ],
            "Resource": "arn:aws:autoscaling:{Region}:{Account_ID}:autoScalingGroup:*:autoScalingGroupName/eks-{key}-*",
            "Effect": "Allow"
      },
      {
            "Condition": {
               "StringLike": {
                  "aws:ResourceTag/Name": [
                        "{key}-EC2SecurityGroup-*",
                        "k8s-traffic-{key}-*",
                        "eks-cluster-sg-{key}-*",
                        "{key}-cluster-sg",
                        "postgres-{key}-sg"
                  ]
               }
            },
            "Action": [
               "ec2:AuthorizeSecurityGroupEgress",
               "ec2:AuthorizeSecurityGroupIngress",
               "ec2:RevokeSecurityGroupEgress",
               "ec2:DeleteSecurityGroup",
               "ec2:CreateTags",
               "ec2:DeleteTags",
               "ec2:CreateNetworkInterface",
               "ec2:DeleteNetworkInterface"
            ],
            "Resource": "arn:aws:ec2:{Region}:{Account_ID}:security-group/*",
            "Effect": "Allow"
      },
      {
            "Condition": {
               "StringLike": {
                  "aws:ResourceTag/elbv2.k8s.aws/cluster": "{key}"
               }
            },
            "Action": [
               "ec2:AuthorizeSecurityGroupEgress",
               "ec2:AuthorizeSecurityGroupIngress",
               "ec2:RevokeSecurityGroupEgress",
               "ec2:DeleteSecurityGroup",
               "ec2:CreateTags",
               "ec2:DeleteTags",
               "ec2:CreateNetworkInterface",
               "ec2:DeleteNetworkInterface"
            ],
            "Resource": "arn:aws:ec2:{Region}:{Account_ID}:security-group/*",
            "Effect": "Allow"
      },
      {
            "Action": [
               "ec2:CreateSecurityGroup"
            ],
            "Resource": "arn:aws:ec2:{Region}:{Account_ID}:vpc/vpc-018d2da0fde903de4",
            "Effect": "Allow"
      },
      {
            "Condition": {
               "StringLike": {
                  "ec2:ResourceTag/Name": "openflow-agent-{key}"
               }
            },
            "Action": [
               "ec2:AttachNetworkInterface"
            ],
            "Resource": "arn:aws:ec2:{Region}:{Account_ID}:instance/*",
            "Effect": "Allow"
      },
      {
            "Condition": {
               "StringLike": {
                  "aws:ResourceTag/Name": "{key}-*-group"
               }
            },
            "Action": [
               "ec2:DeleteLaunchTemplate"
            ],
            "Resource": "arn:aws:ec2:{Region}:{Account_ID}:launch-template/*",
            "Effect": "Allow"
      },
      {
            "Action": [
               "eks:CreateCluster",
               "eks:CreateAccessEntry",
               "eks:CreateAddon",
               "eks:CreateNodegroup",
               "eks:DeleteCluster",
               "eks:DescribeCluster",
               "eks:ListClusters",
               "eks:ListNodeGroups",
               "eks:DescribeUpdate",
               "eks:UpdateClusterConfig",
               "eks:TagResource"
            ],
            "Resource": "arn:aws:eks:{Region}:{Account_ID}:cluster/{key}",
            "Effect": "Allow"
      },
      {
            "Action": [
               "eks:DescribeAddon",
               "eks:DescribeAddonVersions",
               "eks:UpdateAddon",
               "eks:DeleteAddon",
               "eks:DescribeUpdate"
            ],
            "Resource": "arn:aws:eks:{Region}:{Account_ID}:addon/{key}/*",
            "Effect": "Allow"
      },
      {
            "Action": [
               "eks:DeleteNodegroup",
               "eks:DescribeNodegroup",
               "eks:ListNodegroups",
               "eks:UpdateNodegroupConfig",
               "eks:TagResource",
               "eks:DescribeUpdate"
            ],
            "Resource": "arn:aws:eks:{Region}:{Account_ID}:nodegroup/{key}/*",
            "Effect": "Allow"
      },
      {
            "Action": [
               "s3:CreateBucket",
               "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::byoc-tf-state-{key}",
            "Effect": "Allow"
      },
      {
            "Action": [
               "s3:DeleteObject",
               "s3:GetObject",
               "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::byoc-tf-state-{key}/*",
            "Effect": "Allow"
      },
      {
            "Action": [
               "secretsmanager:CreateSecret",
               "secretsmanager:DeleteSecret",
               "secretsmanager:DescribeSecret",
               "secretsmanager:GetResourcePolicy",
               "secretsmanager:GetSecretValue",
               "secretsmanager:PutSecretValue",
               "secretsmanager:UpdateSecretVersionStage"
            ],
            "Resource": "arn:aws:secretsmanager:{Region}:{Account_ID}:secret:*-{key}*",
            "Effect": "Allow"
      },
      {
            "Action": [
               "ecr:BatchCheckLayerAvailability",
               "ecr:BatchGetImage",
               "ecr:DescribeImages",
               "ecr:DescribeRepositories",
               "ecr:GetDownloadUrlForLayer",
               "ecr:ListImages"
            ],
            "Resource": "arn:aws:ecr:{Region}:{Account_ID}:*",
            "Effect": "Allow"
      },
      {
            "Action": [
               "ecr:CreateRepository",
               "ecr:CompleteLayerUpload",
               "ecr:InitiateLayerUpload",
               "ecr:PutImage",
               "ecr:UploadLayerPart"
            ],
            "Resource": "arn:aws:ecr:{Region}:{Account_ID}:repository/snowflake-openflow/*",
            "Effect": "Allow"
      },
      {
            "Condition": {
               "StringLike": {
                  "iam:AWSServiceName": "eks.amazonaws.com"
               }
            },
            "Action": [
               "iam:CreateServiceLinkedRole"
            ],
            "Resource": "arn:aws:iam::*:role/aws-service-role/eks.amazonaws.com/AWSServiceRoleForAmazonEKS",
            "Effect": "Allow"
      },
      {
            "Condition": {
               "StringLike": {
                  "iam:AWSServiceName": "eks-nodegroup.amazonaws.com"
               }
            },
            "Action": [
               "iam:CreateServiceLinkedRole"
            ],
            "Resource": "arn:aws:iam::*:role/aws-service-role/eks-nodegroup.amazonaws.com/AWSServiceRoleForAmazonEKSNodegroup",
            "Effect": "Allow"
      },
      {
            "Action": [
               "eks:AssociateAccessPolicy",
               "eks:ListAssociatedAccessPolicies",
               "eks:DisassociateAccessPolicy"
            ],
            "Resource": "arn:aws:eks:{Region}:{Account_ID}:access-entry/{key}/*",
            "Effect": "Allow"
      },
      {
            "Action": "iam:PassRole",
            "Resource": "*",
            "Effect": "Allow"
      }
   ]
}
```

Show lessSee more

Scroll to top

**IAM Role: {key}-cluster-ServiceRole**

AWS-managed policies:

* AmazonEKSClusterPolicy
* AmazonEKSVPCResourceController

CopyExpand

```
{
   "Version": "2012-10-17",
   "Statement": [
      {
            "Action": [
               "cloudwatch:PutMetricData"
            ],
            "Effect": "Allow",
            "Resource": "*"
      }
   ]
}
{
   "Version": "2012-10-17",
   "Statement": [
      {
            "Action": [
               "ec2:DescribeAccountAttributes",
               "ec2:DescribeAddresses",
               "ec2:DescribeInternetGateways"
            ],
            "Effect": "Allow",
            "Resource": "*"
      }
   ]
}
```

Show lessSee more

Scroll to top

**IAM Role: {key}-addon-vpc-cni-Role**

AWS-managed policies:

* AmazonEKS\_CNI\_Policy

**IAM Role: {key}-eks-role**

AWS-managed policies:

* AmazonEBSCSIDriverPolicy
* AmazonEC2ContainerRegistryReadOnly
* AmazonEKS\_CNI\_Policy
* AmazonEKSWorkerNodePolicy
* AmazonSSMManagedInstanceCore
* AutoScalingFullAccess
* ElasticLoadBalancingFullAccess

CopyExpand

```
{
   "Version": "2012-10-17",
   "Statement": [
      {
            "Action": [
               "ec2:CreateSecurityGroup",
               "ec2:CreateTags"
            ],
            "Effect": "Allow",
            "Resource": [
               "arn:aws:ec2:{Region}:{Account_ID}:security-group/*",
               "arn:aws:ec2:{Region}:{Account_ID}:vpc/{VPC_ID}"
            ],
            "Sid": "CreateOpenflowEKSSecurityGroupAndTags"
      },
      {
            "Action": [
               "ec2:AuthorizeSecurityGroupIngress",
               "ec2:DeleteSecurityGroup"
            ],
            "Condition": {
               "StringLike": {
                  "aws:ResourceTag/Name": "eks-cluster-sg-{key}-*"
               }
            },
            "Effect": "Allow",
            "Resource": [
               "arn:aws:ec2:{Region}:{Account_ID}:security-group/*"
            ],
            "Sid": "OpenflowManageEKSSecurityGroup"
      }
   ]
}
```

Show lessSee more

Scroll to top

Note

{VPC\_ID} represents the identifier of the VPC that was either created by BYOC or used by BYO-VPC.

**IAM Role: oidc-service-role-{key}**

CopyExpand

```
{
   "Statement": [
      {
            "Action": [
               "secretsmanager:GetSecretValue",
               "secretsmanager:DescribeSecret",
               "secretsmanager:GetResourcePolicy",
               "secretsmanager:ListSecretVersionIds"
            ],
            "Effect": "Allow",
            "Resource": [
               "arn:aws:secretsmanager:{Region}:{Account_ID}:secret:oidc-{key}*"
            ]
      }
   ],
   "Version": "2012-10-17"
}
```

Show lessSee more

Scroll to top

**IAM Role: dps-postgres-role-{key}**

CopyExpand

```
{
   "Statement": [
      {
            "Action": [
               "secretsmanager:GetSecretValue",
               "secretsmanager:DescribeSecret",
               "secretsmanager:GetResourcePolicy",
               "secretsmanager:ListSecretVersionIds"
            ],
            "Effect": "Allow",
            "Resource": [
               "arn:aws:secretsmanager:{Region}:{Account_ID}:secret:postgres_creds-{key}*"
            ]
      }
   ],
   "Version": "2012-10-17"
}
```

Show lessSee more

Scroll to top

**IAM Role: dps-temporal-role-{key}**

CopyExpand

```
{
   "Statement": [
      {
            "Action": [
               "secretsmanager:GetSecretValue",
               "secretsmanager:DescribeSecret",
               "secretsmanager:GetResourcePolicy",
               "secretsmanager:ListSecretVersionIds"
            ],
            "Effect": "Allow",
            "Resource": [
               "arn:aws:secretsmanager:{Region}:{Account_ID}:secret:temporal_creds-{key}*"
            ]
      }
   ],
   "Version": "2012-10-17"
}
```

Show lessSee more

Scroll to top

**IAM Role: dp-service-role-{key}**

CopyExpand

```
{
   "Statement": [
      {
            "Action": [
               "secretsmanager:GetSecretValue",
               "secretsmanager:DescribeSecret",
               "secretsmanager:GetResourcePolicy",
               "secretsmanager:ListSecretVersionIds"
            ],
            "Effect": "Allow",
            "Resource": [
               "arn:aws:secretsmanager:{Region}:{Account_ID}:secret:dps_creds-{key}*",
               "arn:aws:secretsmanager:{Region}:{Account_ID}:secret:snowflake-oauth2-{key}*"
            ]
      }
   ],
   "Version": "2012-10-17"
}
```

Show lessSee more

Scroll to top

**IAM Role: oauth2-role-{key}**

CopyExpand

```
{
   "Statement": [
      {
            "Action": [
               "secretsmanager:GetSecretValue",
               "secretsmanager:DescribeSecret",
               "secretsmanager:GetResourcePolicy",
               "secretsmanager:ListSecretVersionIds"
            ],
            "Effect": "Allow",
            "Resource": [
               "arn:aws:secretsmanager:{Region}:{Account_ID}:secret:snowflake-oauth2-{key}*"
            ]
      }
   ],
   "Version": "2012-10-17"
}
```

Show lessSee more

Scroll to top

**IAM Role: {key}-nodegroup-NodeInstanceRole**

AWS-managed policies:

* AmazonEBSCSIDriverPolicy
* AmazonEC2ContainerRegistryReadOnly
* AmazonEKS\_CNI\_Policy
* AmazonEKSWorkerNodePolicy
* AmazonSSMManagedInstanceCore
* AutoScalingFullAccess
* ElasticLoadBalancingFullAccess

CopyExpand

```
{
   "Version": "2012-10-17",
   "Statement": [
      {
            "Action": [
               "servicediscovery:CreateService",
               "servicediscovery:DeleteService",
               "servicediscovery:GetService",
               "servicediscovery:GetInstance",
               "servicediscovery:RegisterInstance",
               "servicediscovery:DeregisterInstance",
               "servicediscovery:ListInstances",
               "servicediscovery:ListNamespaces",
               "servicediscovery:ListServices",
               "servicediscovery:GetInstancesHealthStatus",
               "servicediscovery:UpdateInstanceCustomHealthStatus",
               "servicediscovery:GetOperation",
               "route53:GetHealthCheck",
               "route53:CreateHealthCheck",
               "route53:UpdateHealthCheck",
               "route53:ChangeResourceRecordSets",
               "route53:DeleteHealthCheck",
               "appmesh:*"
            ],
            "Effect": "Allow",
            "Resource": "*"
      }
   ]
}
{
   "Version": "2012-10-17",
   "Statement": [
      {
            "Action": [
               "autoscaling:DescribeAutoScalingGroups",
               "autoscaling:DescribeAutoScalingInstances",
               "autoscaling:DescribeLaunchConfigurations",
               "autoscaling:DescribeScalingActivities",
               "autoscaling:DescribeTags",
               "ec2:DescribeInstanceTypes",
               "ec2:DescribeLaunchTemplateVersions"
            ],
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "autoscaling:SetDesiredCapacity",
               "autoscaling:TerminateInstanceInAutoScalingGroup",
               "ec2:DescribeImages",
               "ec2:GetInstanceTypesFromInstanceRequirements",
               "eks:DescribeNodegroup"
            ],
            "Effect": "Allow",
            "Resource": "*"
      }
   ]
}
{
   "Version": "2012-10-17",
   "Statement": [
      {
            "Action": [
               "iam:CreateServiceLinkedRole"
            ],
            "Condition": {
               "StringEquals": {
                  "iam:AWSServiceName": "elasticloadbalancing.amazonaws.com"
               }
            },
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "ec2:DescribeAccountAttributes",
               "ec2:DescribeAddresses",
               "ec2:DescribeAvailabilityZones",
               "ec2:DescribeInternetGateways",
               "ec2:DescribeVpcs",
               "ec2:DescribeVpcPeeringConnections",
               "ec2:DescribeSubnets",
               "ec2:DescribeSecurityGroups",
               "ec2:DescribeInstances",
               "ec2:DescribeNetworkInterfaces",
               "ec2:DescribeTags",
               "ec2:GetCoipPoolUsage",
               "ec2:DescribeCoipPools",
               "elasticloadbalancing:DescribeLoadBalancers",
               "elasticloadbalancing:DescribeLoadBalancerAttributes",
               "elasticloadbalancing:DescribeListeners",
               "elasticloadbalancing:DescribeListenerCertificates",
               "elasticloadbalancing:DescribeSSLPolicies",
               "elasticloadbalancing:DescribeRules",
               "elasticloadbalancing:DescribeTargetGroups",
               "elasticloadbalancing:DescribeTargetGroupAttributes",
               "elasticloadbalancing:DescribeTargetHealth",
               "elasticloadbalancing:DescribeTags"
            ],
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "cognito-idp:DescribeUserPoolClient",
               "acm:ListCertificates",
               "acm:DescribeCertificate",
               "iam:ListServerCertificates",
               "iam:GetServerCertificate",
               "waf-regional:GetWebACL",
               "waf-regional:GetWebACLForResource",
               "waf-regional:AssociateWebACL",
               "waf-regional:DisassociateWebACL",
               "wafv2:GetWebACL",
               "wafv2:GetWebACLForResource",
               "wafv2:AssociateWebACL",
               "wafv2:DisassociateWebACL",
               "shield:GetSubscriptionState",
               "shield:DescribeProtection",
               "shield:CreateProtection",
               "shield:DeleteProtection"
            ],
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "ec2:AuthorizeSecurityGroupIngress",
               "ec2:RevokeSecurityGroupIngress"
            ],
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "ec2:CreateSecurityGroup"
            ],
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "ec2:CreateTags"
            ],
            "Condition": {
               "Null": {
                  "aws:RequestTag/elbv2.k8s.aws/cluster": "false"
               },
               "StringEquals": {
                  "ec2:CreateAction": "CreateSecurityGroup"
               }
            },
            "Effect": "Allow",
            "Resource": "arn:aws:ec2:*:*:security-group/*"
      },
      {
            "Action": [
               "ec2:CreateTags",
               "ec2:DeleteTags"
            ],
            "Condition": {
               "Null": {
                  "aws:RequestTag/elbv2.k8s.aws/cluster": "true",
                  "aws:ResourceTag/elbv2.k8s.aws/cluster": "false"
               }
            },
            "Effect": "Allow",
            "Resource": "arn:aws:ec2:*:*:security-group/*"
      },
      {
            "Action": [
               "ec2:AuthorizeSecurityGroupIngress",
               "ec2:RevokeSecurityGroupIngress",
               "ec2:DeleteSecurityGroup"
            ],
            "Condition": {
               "Null": {
                  "aws:ResourceTag/elbv2.k8s.aws/cluster": "false"
               }
            },
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "elasticloadbalancing:CreateLoadBalancer",
               "elasticloadbalancing:CreateTargetGroup"
            ],
            "Condition": {
               "Null": {
                  "aws:RequestTag/elbv2.k8s.aws/cluster": "false"
               }
            },
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "elasticloadbalancing:CreateListener",
               "elasticloadbalancing:DeleteListener",
               "elasticloadbalancing:CreateRule",
               "elasticloadbalancing:DeleteRule"
            ],
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "elasticloadbalancing:AddTags",
               "elasticloadbalancing:RemoveTags"
            ],
            "Condition": {
               "Null": {
                  "aws:RequestTag/elbv2.k8s.aws/cluster": "true",
                  "aws:ResourceTag/elbv2.k8s.aws/cluster": "false"
               }
            },
            "Effect": "Allow",
            "Resource": [
               "arn:aws:elasticloadbalancing:*:*:targetgroup/*/*",
               "arn:aws:elasticloadbalancing:*:*:loadbalancer/net/*/*",
               "arn:aws:elasticloadbalancing:*:*:loadbalancer/app/*/*"
            ]
      },
      {
            "Action": [
               "elasticloadbalancing:AddTags",
               "elasticloadbalancing:RemoveTags"
            ],
            "Effect": "Allow",
            "Resource": [
               "arn:aws:elasticloadbalancing:*:*:listener/net/*/*/*",
               "arn:aws:elasticloadbalancing:*:*:listener/app/*/*/*",
               "arn:aws:elasticloadbalancing:*:*:listener-rule/net/*/*/*",
               "arn:aws:elasticloadbalancing:*:*:listener-rule/app/*/*/*"
            ]
      },
      {
            "Action": [
               "elasticloadbalancing:ModifyLoadBalancerAttributes",
               "elasticloadbalancing:SetIpAddressType",
               "elasticloadbalancing:SetSecurityGroups",
               "elasticloadbalancing:SetSubnets",
               "elasticloadbalancing:DeleteLoadBalancer",
               "elasticloadbalancing:ModifyTargetGroup",
               "elasticloadbalancing:ModifyTargetGroupAttributes",
               "elasticloadbalancing:DeleteTargetGroup"
            ],
            "Condition": {
               "Null": {
                  "aws:ResourceTag/elbv2.k8s.aws/cluster": "false"
               }
            },
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "elasticloadbalancing:AddTags"
            ],
            "Condition": {
               "Null": {
                  "aws:RequestTag/elbv2.k8s.aws/cluster": "false"
               },
               "StringEquals": {
                  "elasticloadbalancing:CreateAction": [
                        "CreateTargetGroup",
                        "CreateLoadBalancer"
                  ]
               }
            },
            "Effect": "Allow",
            "Resource": [
               "arn:aws:elasticloadbalancing:*:*:targetgroup/*/*",
               "arn:aws:elasticloadbalancing:*:*:loadbalancer/net/*/*",
               "arn:aws:elasticloadbalancing:*:*:loadbalancer/app/*/*"
            ]
      },
      {
            "Action": [
               "elasticloadbalancing:RegisterTargets",
               "elasticloadbalancing:DeregisterTargets"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:elasticloadbalancing:*:*:targetgroup/*/*"
      },
      {
            "Action": [
               "elasticloadbalancing:SetWebAcl",
               "elasticloadbalancing:ModifyListener",
               "elasticloadbalancing:AddListenerCertificates",
               "elasticloadbalancing:RemoveListenerCertificates",
               "elasticloadbalancing:ModifyRule"
            ],
            "Effect": "Allow",
            "Resource": "*"
      }
   ]
}
{
   "Version": "2012-10-17",
   "Statement": [
      {
            "Action": [
               "route53:ChangeResourceRecordSets"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:route53:::hostedzone/*"
      }
   ]
}
{
   "Version": "2012-10-17",
   "Statement": [
      {
            "Action": [
               "route53:GetChange"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:route53:::change/*"
      }
   ]
}
{
   "Version": "2012-10-17",
   "Statement": [
      {
            "Action": [
               "route53:ListResourceRecordSets",
               "route53:ListHostedZonesByName"
            ],
            "Effect": "Allow",
            "Resource": "*"
      }
   ]
}
{
   "Version": "2012-10-17",
   "Statement": [
      {
            "Action": [
               "ec2:CreateSnapshot",
               "ec2:AttachVolume",
               "ec2:DetachVolume",
               "ec2:ModifyVolume",
               "ec2:DescribeAvailabilityZones",
               "ec2:DescribeInstances",
               "ec2:DescribeSnapshots",
               "ec2:DescribeTags",
               "ec2:DescribeVolumes",
               "ec2:DescribeVolumesModifications"
            ],
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "ec2:CreateTags"
            ],
            "Condition": {
               "StringEquals": {
                  "ec2:CreateAction": [
                        "CreateVolume",
                        "CreateSnapshot"
                  ]
               }
            },
            "Effect": "Allow",
            "Resource": [
               "arn:aws:ec2:*:*:volume/*",
               "arn:aws:ec2:*:*:snapshot/*"
            ]
      },
      {
            "Action": [
               "ec2:DeleteTags"
            ],
            "Effect": "Allow",
            "Resource": [
               "arn:aws:ec2:*:*:volume/*",
               "arn:aws:ec2:*:*:snapshot/*"
            ]
      },
      {
            "Action": [
               "ec2:CreateVolume"
            ],
            "Condition": {
               "StringLike": {
                  "aws:RequestTag/ebs.csi.aws.com/cluster": "true"
               }
            },
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "ec2:CreateVolume"
            ],
            "Condition": {
               "StringLike": {
                  "aws:RequestTag/CSIVolumeName": "*"
               }
            },
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "ec2:DeleteVolume"
            ],
            "Condition": {
               "StringLike": {
                  "ec2:ResourceTag/ebs.csi.aws.com/cluster": "true"
               }
            },
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "ec2:DeleteVolume"
            ],
            "Condition": {
               "StringLike": {
                  "ec2:ResourceTag/CSIVolumeName": "*"
               }
            },
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "ec2:DeleteVolume"
            ],
            "Condition": {
               "StringLike": {
                  "ec2:ResourceTag/kubernetes.io/created-for/pvc/name": "*"
               }
            },
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "ec2:DeleteSnapshot"
            ],
            "Condition": {
               "StringLike": {
                  "ec2:ResourceTag/CSIVolumeSnapshotName": "*"
               }
            },
            "Effect": "Allow",
            "Resource": "*"
      },
      {
            "Action": [
               "ec2:DeleteSnapshot"
            ],
            "Condition": {
               "StringLike": {
                  "ec2:ResourceTag/ebs.csi.aws.com/cluster": "true"
               }
            },
            "Effect": "Allow",
            "Resource": "*"
      }
   ]
}
{
   "Version": "2012-10-17",
   "Statement": [
      {
            "Action": [
               "route53:ChangeResourceRecordSets"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:route53:::hostedzone/*"
      }
   ]
}
{
   "Version": "2012-10-17",
   "Statement": [
      {
            "Action": [
               "route53:ListHostedZones",
               "route53:ListResourceRecordSets",
               "route53:ListTagsForResource"
            ],
            "Effect": "Allow",
            "Resource": "*"
      }
   ]
}
```

Show lessSee more

Scroll to top
