---
title: "Openflow BYOC - Set up custom ingress"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/setup-openflow-byoc-custom-ingress"
---

# Openflow BYOC - Set up custom ingress

Feature — Generally Available

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../intro-regions.html#label-na-general-regions).

This topic describes the considerations for and steps required to set up an Openflow BYOC deployment with a custom ingress solution managed within your own AWS account.

## Benefits

Custom ingress for Openflow BYOC deployments provides your organization with:

* Stronger security with network-level restrictions that can limit access to only your VPN or private network.
* Full control over the URL and TLS certificate used to access Openflow to meet your security and compliance requirements.

## Considerations

With Snowflake managed ingress, Openflow creates the necessary DNS records, public load balancer, and manages the TLS certificate for the Openflow runtimes in your BYOC deployment.

When you enable custom ingress, Openflow will no longer automatically manage external DNS records, will not create a public load balancer automatically, and will no longer manage certificates for the Openflow runtimes. You must manage these resources within your own AWS account.

[![Openflow Managed Ingress compared with Custom Ingress, highlighting the additional requirements for DNS, load balancers, and certificates.](../../../_images/openflow-byoc-custom-ingress.svg)](../../../_images/openflow-byoc-custom-ingress.svg)

## Configure custom ingress in Snowflake Openflow

1. Enable custom ingress during deployment creation.

   > * During deployment creation, enable Custom ingress and specify your preferred fully qualified domain name (FQDN) in the Hostname field.
   > * You must be able to manage this DNS record and create a TLS certificate for this FQDN. Do not use a subdomain of `snowflakecomputing.com`.
   > * You must not include the protocol https:// or a trailing slash / in the FQDN.
   > * For example, if you specify `openflow01.your-domain.org`, you will access a runtime named “My Runtime” at `https://openflow01.your-domain.org/my-runtime/nifi/`.
2. Download the CloudFormation template. This file has all of the settings required for Openflow to run as your custom ingress domain.

## Configure custom ingress in AWS

Note

`{deployment-key}` represents the Openflow unique identifier applied to cloud resources created and managed by Openflow for a particular deployment.

This is in the `DataPlaneKey` parameter of the CloudFormation template, also available in Openflow through the View Details menu option for the deployment.

1. Add the following tag to the private subnets for your Openflow deployment:

   > * Key: kubernetes.io/role/internal-elb
   > * Value: `1`
2. If your private subnets are used by other EKS clusters, you must also tag them with the name of the Openflow cluster. This allows Openflow to create a load balancer alongside other load balancers.

   > * Key: kubernetes.io/cluster/{deployment-key}
   > * Value: `1`
3. Upload the CloudFormation template. Wait approximately 30 minutes for Openflow to create the internal network load balancer.

   > * You can find the internal network load balancer in the AWS Console under EC2 » Load Balancers.
   > * The load balancer will be named `runtime-ingress-{deployment-key}`.
4. Obtain the internal IP address of the Openflow-managed AWS internal network load balancer.

   > * Under EC2 » Load Balancers, navigate to the details page and copy the DNS name of the Load Balancer.
   > * Log into your agent EC2 instance (identified as `openflow-agent-{deployment-key}`) and run the command `nslookup {openflow-load-balancer-dns-name}`.
   > * Copy the IP addresses of the Openflow-managed AWS internal network load balancer. These are destinations for the target group of the load balancer you will create in a following step.
5. Provision a TLS certificate.

   > * Obtain a TLS certificate for the load balancer that will handle traffic to the Openflow runtime UIs. You can generate a certificate using AWS Certificate Manager (ACM) or import an existing certificate.
6. Create a network load balancer that will route traffic to the Openflow-managed AWS internal network load balancer.

   > 1. In your AWS account, create a Network Load Balancer with the following configuration:
   >
   >    * Name: We recommend the naming convention `custom-ingress-external-{deployment-key}`, where `{deployment-key}` is the key of your Openflow deployment.
   >    * Type: Network Load Balancer
   >    * Scheme: Internal or Internet-facing, depending on your requirements.
   >    * VPC: Select the VPC of your deployment
   >    * Availability Zones: Select both Availability Zones where your Openflow deployment is running.
   >    * Subnets: Select the private subnets of your VPC for an Internal Load Balancer, or the public subnets of your VPC for an Internet-facing Load Balancer.
   >    * Security groups: Select or create a security group that allows traffic on port `443`
   >    * Default SSL/TLS server certificate: Import your SSL/TLS certificate
   >    * Target group: Create a new target group with the following settings:
   >
   >      + Target type: IP addresses
   >      + Protocol: TLS
   >      + Port: 443
   >      + VPC: Verify the VPC matches your deployment
   >      + Type the IP address of the internal network load balancer created by Openflow (obtained in the previous step) as the target and select Include as pending below.
   > 2. Once the load balancer is created, copy the DNS name for the load balancer to use in the next step.
   > 3. For more information on how to create a network load balancer, see [Create a Network Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/create-network-load-balancer.html).
7. Create a DNS CNAME record that maps your custom ingress FQDN to the AWS load balancer’s DNS name.

   > * For detailed DNS configuration instructions in Route 53, see [Create records in Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-creating.html).

## Verification

1. The Openflow deployment shows a status of Active in the Deployments page.
2. Create a runtime in the Openflow deployment.
3. Once the runtime is Active, click on the runtime name or use the View canvas menu option to access the runtime’s UI.
4. Openflow directs you to the runtime with the hostname specified during deployment creation. For example, `https://openflow01.your-domain.org/my-runtime/nifi/`.
