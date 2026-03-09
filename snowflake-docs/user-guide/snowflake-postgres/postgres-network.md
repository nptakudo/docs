---
title: "Snowflake Postgres networking"
url: "https://docs.snowflake.com/en/user-guide/snowflake-postgres/postgres-network"
---

# Snowflake Postgres networking

By default, Snowflake Postgres will provision each new instance inside a new private network in the cloud region you have
selected. Each network is separate and private from other networks in the same cloud region.

By default, Snowflake Postgres instances do not allow incoming connections. Traffic to/from your Snowflake Postgres instances can be
enabled in either of these two ways:

* Attach a network policy containing Postgres ingress and/or egress network rules. This option is available for all accounts.
* Configure Private Link connections to/from cloud vendor private networks. This option is available for Business Critical edition or
  above accounts.

## Snowflake Postgres network policies and rules

[Network policies](../network-policies.html#label-network-policy-about) and [network rules](../network-policies.html#label-network-policy-network-rules) for Snowflake Postgres
instances function much the same as they do for other Snowflake resources with a few key differences:

* Network policies do not need to be [activated](../network-policies.html#label-associating-network-policies) to be used with Snowflake Postgres instances
  in the same way they are for Snowflake accounts, users, and other security integrations. Network policies for Snowflake Postgres instances
  are instead attached to the instances directly at instance creation time. Existing instances can also have their network policies changed.
* Snowflake Postgres instances only use the ALLOWED\_NETWORK\_RULE\_LIST and BLOCKED\_NETWORK\_RULE\_LIST properties of network policies.
  The BLOCKED\_IP\_LIST and ALLOWED\_IP\_LIST properties are ignored.
* Network rules for Snowflake Postgres instances should use either the Postgres Ingress or Postgres Egress modes. Rules using these modes
  are currently limited to type IPv4.
* Network rules using other modes other than Postgres Ingress or Postgres Egress in a network policy are ignored by Snowflake Postgres
  instances that use them.

Warning

Snowflake recommends making your network policies as restrictive as is practical. Applying a policy with a `0.0.0.0/0` networking rule will make the server open to connections from anywhere on the internet. For this reason, Snowflake recommends against using policies with `0.0.0.0/0` rules for your Snowflake Postgres instances.

### Privileges

* To create new network policies, Snowflake users must have the CREATE NETWORK POLICY privilege on the account.
* To create new network rules, Snowflake users must have the CREATE NETWORK RULE privilege on the schema in which they want to
  create the rules.
* To attach an existing network policy to a Snowflake instance, Snowflake users must own the network policy or the policy’s owner must
  [GRANT](../../sql-reference/sql/grant-privilege) usage on it.

### Snowflake Postgres network policy and rules example

Let’s say that:

* You want to allow incoming traffic to a new Postgres instance from your office, and your office network router’s public IP address is
  `23.206.171.35`.
* You also want to allow outgoing traffic from the new Postgres instance to your office Postgres server via a Postgres Foreign Data Wrapper
  connection.

For this we’ll create a new policy with both a Postgres Ingress network rule and a Postgres Egress network rule.

SnowsightSQL

1. [Create two new network rules](../network-policies.html#label-network-policy-create-rule). Use `23.206.171.35/32` as the sole network identifier for both, and use “Postgres Ingress” as the Mode for one and “Postgres Egress” for Mode of the other.
2. [Create a new network policy](../network-policies.html#label-network-policy-create-policy) with both new rules included in its Allowed list.
3. In the navigation menu, select Postgres.
4. Select + Create.
5. When selecting your desired instance configuration details make sure to select your new policy under Network policy select box. In the image below we have selected the policy that we named `OFFICE POLICY EXAMPLE`.

[![Create Snowflake Postgres with Network Policy](../../_images/snowflake-pg-create-with-policy.png)](../../_images/snowflake-pg-create-with-policy.png)

CopyExpand

```
-- Create the ingress rule
CREATE NETWORK RULE PG_INGRESS_FROM_OFFICE
  TYPE = IPV4
  VALUE_LIST = ('23.206.171.35/32')
  MODE = POSTGRES_INGRESS;

-- Create the egress rule
CREATE NETWORK RULE PG_EGRESS_TO_OFFICE
  TYPE = IPV4
  VALUE_LIST = ('23.206.171.35/32')
  MODE = POSTGRES_EGRESS;

-- Create a new policy using both rules in its allowed list
CREATE NETWORK POLICY "OFFICE POLICY EXAMPLE"
  ALLOWED_NETWORK_RULE_LIST = ('PG_INGRESS_FROM_OFFICE', 'PG_EGRESS_TO_OFFICE')
  COMMENT = 'Traffic to/from the office.';

-- Create a new Snowflake Postgres instance that uses the new policy
CREATE POSTGRES INSTANCE SNOWFLAKE_POSTGRES_DEMO
  COMPUTE_FAMILY = 'STANDARD_L'
  STORAGE_SIZE_GB = 50
  AUTHENTICATION_AUTHORITY = POSTGRES
  POSTGRES_VERSION = 17
  NETWORK_POLICY = '"OFFICE POLICY EXAMPLE"';
```

Show lessSee more

Scroll to top

### Creating ingress rules at instance creation time

Instead of creating your network policy and rules before creating your Snowflake Postgres instance, you can create a
policy with Postgres ingress rules when creating Snowflake Postgres instances via Snowsight.

1. In the navigation menu, select Postgres.
2. In the Postgres Instances page, select the Create button at the top right.
3. Choose your instance configuration but leave the Network policy choice blank.
4. After you select the Create, a new dialog displays the `snowflake_admin` Postgres user’s
   [connection credentials](connecting-to-snowflakepg). After saving those credentials in a secure location,
   select Continue to network settings.
5. In the Network Settings dialog (shown below) enter the IP address and/or CIDR values you wish to create Postgres ingress
   rules for, pressing enter to add each one to the list.
6. Expand the Details section to edit your new network rule and/or policy names if needed.
7. Select Save to create your new Postgres ingress network policy and have it automatically attached to your instance once it is active.

[![Create Snowflake Postgres ingress network policy at instance create time](../../_images/snowflakepg-instance-network-settings.png)](../../_images/snowflakepg-instance-network-settings.png)

## Snowflake Postgres Private Link

Private Link for Snowflake Postgres instances is available for Business Critical edition accounts and above.

To enable Private Link for a Snowflake Postgres instance, start by following the instructions to enable Private Link between your cloud
vendor account and your Snowflake account:

* [AWS PrivateLink and Snowflake](../admin-security-privatelink)
* [Azure Private Link and Snowflake](../privatelink-azure)
* [Google Cloud Private Service Connect and Snowflake](../private-service-connect-google)

### Privileges

To enable Private Link for Snowflake Postgres instances, Snowflake users must have the following privileges

* MANAGE POSTGRES PRIVATE CONNECTIVITY ON ACCOUNT
* OWNERSHIP or MANAGE for each given Snowflake Postgres instance

### Setting up Private Link for Snowflake Postgres instances

Once you have Private Link enabled between your cloud vendor and Snowflake accounts and the required privileges, you can enable Private Link
for Snowflake Postgres instances on a per-instance basis as follows.

SnowsightSQL

If you do not intend to set up any network policy rules for your instance in addition to your Private Link connection, select
Private Link for the Network Security option in the New instance dialog. If you do want to set up or use a network
policy select Network policy instead and follow the previous instructions on network policies.

Once an instance is active you can enable Private Link for it:

1. In the navigation menu, select Postgres and select your instance.
2. In the instance’s Instance details pane, select the edit icon in the Private Link section.
3. A confirmation dialog is shown asking you to confirm setting up Private Link for your cloud service provider. Select Enable.
   Note that this step can take up to 10 minutes to complete.

Once Private Link is active for your Snowflake Postgres instance you can establish new Private Link connections for it:

1. In the navigation menu, select Postgres and select your instance to see its details page.
2. Select the edit icon in the Private Link section to the right to expand the Private Link pane (shown below).
3. Use the displayed Service address to make a Private Link connection request from the private network on your cloud
   vendor account.
4. Refresh your Snowflake Postgres instance’s details page. The Private Link pane will now have a new connection entry
   for your request with neither the check mark (accept) nor x mark (reject) selected. Select the check mark
   to accept.
5. You can now connect to your Snowflake Postgres instance from hosts in cloud service provider’s private network.

[![Create Snowflake Postgres ingress network policy at instance create time](../../_images/snowflakepg-private-link.png)](../../_images/snowflakepg-private-link.png)

You can enable Private Link for an active instance with Snowflake SQL as follows:

CopyExpand

```
ALTER POSTGRES INSTANCE <name> ENABLE PRIVATELINK;
```

Show lessSee more

Scroll to top

That asynchronous operation can take up to 10 minutes. To track its status check the value of the `privatelink_service_identifier`
returned by DESCRIBE POSTGRES INSTANCE:

CopyExpand

```
DESCRIBE POSTGRES INSTANCE <name>;
```

Show lessSee more

Scroll to top

The same `privatelink_service_identifier` is shown for the instances entry in the output of SHOW POSTGRES INSTANCES:

CopyExpand

```
SHOW POSTGRES INSTANCES;
```

Show lessSee more

Scroll to top

When that `privatelink_service_identifier` column shows a non-NULL value you can use that identifier to make a Private Link connection
request from the private network on your cloud service provider account you have enabled for Private Link connections to your Snowflake
account.

After making that connection request from your cloud vendor account’s private network find the request for the Snowflake Postgres
instance:

CopyExpand

```
SHOW PRIVATELINK CONNECTIONS IN POSTGRES INSTANCE <name>;
```

Show lessSee more

Scroll to top

This command returns the following columns:

* `endpoint`
* `connection_id`
* `status`

Your connection request will be an entry with your cloud vendor private network’s Private Link `endpoint` value and a `status` value
of `pending`.

You can accept one or more pending Private Link connection requests by running an ALTER POSTGRES INSTANCE command:

CopyExpand

```
ALTER POSTGRES INSTANCE [IF EXISTS] <name> AUTHORIZE PRIVATELINK CONNECTIONS = ('<connection_id' [ , ... ]);
```

Show lessSee more

Scroll to top

You can revoke one or more pending or previously approved Private Link connection requests by running this command:

CopyExpand

```
ALTER POSTGRES INSTANCE [IF EXISTS] <name> REVOKE PRIVATELINK CONNECTIONS = ('<connection_id' [ , ... ]);
```

Show lessSee more

Scroll to top

### Connecting to Snowflake Postgres instances over Private Links

Instead of using the Snowflake Postgres instance’s `hostname`, connections to Snowflake Postgres instances via Private Link setups should
be made using the DNS hostname configured on your cloud service provider’s private network for the Private Link.
