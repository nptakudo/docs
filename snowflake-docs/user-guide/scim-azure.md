---
title: "Microsoft Entra ID SCIM integration with Snowflake"
url: "https://docs.snowflake.com/en/user-guide/scim-azure"
---

# Microsoft Entra ID SCIM integration with Snowflake

Snowflake supports Microsoft Entra ID as a SCIM identity provider.

This topic provides details about provisioning users and groups from Microsoft Entra ID to Snowflake.

## Features

* Automatic Microsoft Entra ID User Provisioning to Snowflake.
* Use the `allowedInterfaces` custom attribute to prevent a provisioned user from using certain interfaces to access Snowflake.
* Automatic Microsoft Entra ID Group Provisioning to Snowflake.
* Synchronizing Microsoft Entra ID users and groups to Snowflake.
* If Microsoft Entra ID is configured for
  [SAML SSO to Snowflake](https://docs.microsoft.com/en-us/azure/active-directory/saas-apps/snowflake-tutorial), Microsoft Entra ID users
  provisioned to Snowflake can access Snowflake using SAML SSO.

  Note

  By default, Microsoft Entra ID users provisioned to Snowflake using SCIM are not assigned a password in Snowflake. This means that if SAML SSO is configured in Microsoft Entra ID, users will authenticate to Snowflake using SSO.

  SAML SSO is not a requirement if using SCIM to provision users and groups from Microsoft Entra ID to Snowflake. For additional options, see
  [Configure Entra ID single sign-on](https://docs.microsoft.com/en-us/azure/active-directory/saas-apps/snowflake-tutorial#configure-azure-ad-single-sign-on).

### Limitations

* Snowflake supports a maximum of 500 concurrent requests per account per SCIM endpoint (e.g. the `/Users` endpoint, the `/Groups` endpoint). After your account exceeds this threshold, Snowflake returns a `429` HTTP status code (i.e. too many requests). Note that this request limit usually only occurs during the initial provisioning when relatively large numbers of requests (i.e. more than 10 thousand) occur to provision users or groups.

### Not supported

* AWS PrivateLink and Google Cloud Private Service Connect. Customers wanting to provision users and groups to Snowflake from
  Microsoft Entra ID without traversing the public Internet need to have their Snowflake account in Microsoft Azure.
* If you are using Azure Private Link to access Snowflake, ensure that you are not using the Azure Private Link URL in the integration
  settings. Enter the public endpoint (i.e. without `.privatelink`), and ensure that the network policy allows access from the
  Azure IP addresses as shown in the [Prerequisites](#label-scim-azure-prereq) section. Otherwise, you cannot use this integration.
* Transferring ownership of existing users and roles. Microsoft Entra ID is the authoritative source for its users and groups. Group membership
  can be updated in Microsoft Entra ID. However, existing users and groups in Snowflake cannot be transferred to Microsoft Entra ID.
* Microsoft Entra ID does not currently support reading or provisioning
  [nested groups](https://docs.microsoft.com/en-us/azure/active-directory/app-provisioning/how-provisioning-works#scoping). Therefore, you
  cannot use the Snowflake Microsoft Entra ID SCIM integration to provision or manage nested groups in Snowflake. Please contact Microsoft to
  request the support of nested groups.
* Enabling or disabling password synchronization from Microsoft Entra ID to Snowflake.

  Setting the `SYNC_PASSWORD` property in the Snowflake security integration will not synchronize user passwords from Microsoft Entra ID
  to Snowflake. This is a Microsoft Entra ID limitation. To request support, please contact Microsoft Entra ID.

## Prerequisites

Before using SCIM to provision Microsoft Entra ID users and groups to Snowflake, verify the following:

1. An existing Microsoft Entra ID tenant.
2. An existing Snowflake tenant.

   * During the configuration process in Microsoft, you will need to input the URL of the Snowflake SCIM endpoint (i.e. Tenant URL
     in the Microsoft Entra ID SCIM configuration guide). The Snowflake SCIM endpoint consists of the Snowflake account URL appended with
     `/scim/v2/`. For example, if you use the account name URL format, the SCIM endpoint is
     `https://myorg-myaccount.snowflakecomputing.com/scim/v2/`. For a list of supported formats for the Snowflake account URL, see
     [Connecting with a URL](organizations-connect.html#label-connecting-via-url).
3. At least one user in Snowflake with the ACCOUNTADMIN role
4. Before provisioning users or groups, as it pertains to your account, ensure that the [network policy](network-policies)
   in Snowflake allows access from all of the Azure IP addresses for the [Public Cloud](https://www.microsoft.com/en-us/download/details.aspx?id=56519) or the [US Government Cloud](https://www.microsoft.com/en-us/download/details.aspx?id=57063). Currently, all Azure IP addresses are required to create a
   Microsoft Entra ID SCIM network policy. For more information, see [Managing SCIM Network Policies](#managing-scim-network-policies).

## Configuration

The Snowflake configuration process creates a SCIM security integration to allow users and roles created in Microsoft Entra ID to be owned by
the AAD\_PROVISIONER SCIM role in Snowflake and creates an access token to use in SCIM API requests. The access token (i.e. the
Secret Token in the Microsoft Entra ID SCIM configuration guide) is valid for six months. Upon expiration, create a new access token
manually using [SYSTEM$GENERATE\_SCIM\_ACCESS\_TOKEN](../sql-reference/functions/system_generate_scim_access_token) as shown below.

Note

To invalidate an existing access token for a SCIM integration, execute a [DROP INTEGRATION](../sql-reference/sql/drop-integration) statement.

To continue using SCIM with Snowflake, recreate the SCIM integration with a [CREATE SECURITY INTEGRATION](../sql-reference/sql/create-security-integration-scim) statement and generate a new access token using [SYSTEM$GENERATE\_SCIM\_ACCESS\_TOKEN](../sql-reference/functions/system_generate_scim_access_token).

### Microsoft Entra ID configuration

To use Microsoft Entra ID as a SCIM identity provider, follow the instructions in the
[Microsoft documentation](https://docs.microsoft.com/en-us/azure/active-directory/saas-apps/snowflake-provisioning-tutorial). While
completing these steps, do not re-use an existing enterprise application in Microsoft Entra ID. Failure to create a new enterprise
application for provisioning can result in unexpected behavior.

Note

If you are creating custom attributes and would like the `name` and `login_name` fields for the Snowflake user to have different
values, contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support) to enable separate mappings for your account before creating the custom attributes.

### Snowflake configuration

To facilitate the Snowflake configuration, you can copy the SQL below for use in this first [step](https://docs.microsoft.com/en-us/azure/active-directory/saas-apps/snowflake-provisioning-tutorial#setup-snowflake-for-provisioning). Each of the following statements is explained below.

CopyExpand

```
use role accountadmin;
create role if not exists aad_provisioner;
grant create user on account to role aad_provisioner;
grant create role on account to role aad_provisioner;
grant role aad_provisioner to role accountadmin;
create or replace security integration aad_provisioning
    type = scim
    scim_client = 'azure'
    run_as_role = 'AAD_PROVISIONER';
select system$generate_scim_access_token('AAD_PROVISIONING');
```

Show lessSee more

Scroll to top

Important

The example SQL statements use the ACCOUNTADMIN system role and the AAD\_PROVISIONER custom role is granted to the ACCOUNTADMIN role.

It is possible not to use the ACCOUNTADMIN role in favor of a less-privileged role. Using a less-privileged role can help to address compliance concerns relating to least-privileged access, however, using a less-privileged role can result in unexpected errors during the SCIM configuration and management process.

These errors could be the result of the less-privileged role not having sufficient rights to manage all of the roles through SCIM due to how the roles are created and the resultant role hierarchy. Therefore, in an effort to avoid errors in the configuration and management processes, choose one of the following options:

1. Use the ACCOUNTADMIN role as shown in the example SQL statements.
2. Use a role with the global MANAGE GRANTS privilege.
3. If neither of these first two options are desirable, use a custom role that has the OWNERSHIP privilege on all of the roles that will be managed using SCIM.

1. Login to Snowflake as an administrator and execute the following from either the Snowflake worksheet interface, Snowflake CLI, or SnowSQL.
2. Use the ACCOUNTADMIN role.

   > CopyExpand
   >
   > ```
   > use role accountadmin;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
3. Create the custom role AAD\_PROVISIONER. All users and roles in Snowflake created by Microsoft Entra ID will be owned by the scoped down
   AAD\_PROVISIONER role.

   > CopyExpand
   >
   > ```
   > create role if not exists aad_provisioner;
   > grant create user on account to role aad_provisioner;
   > grant create role on account to role aad_provisioner;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
4. Let the ACCOUNTADMIN role create the security integration using the AAD\_PROVISIONER custom role. For more information, see [CREATE SECURITY INTEGRATION](../sql-reference/sql/create-security-integration-scim).

   > CopyExpand
   >
   > ```
   > grant role aad_provisioner to role accountadmin;
   > create or replace security integration aad_provisioning
   >     type=scim
   >     scim_client='azure'
   >     run_as_role='AAD_PROVISIONER';
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
5. Create and copy the authorization token to the clipboard and store securely for later use. Use this token for each SCIM REST API request and place it in the request header. The access token expires after six months and a new access token can be generated with this statement.

   > CopyExpand
   >
   > ```
   > select system$generate_scim_access_token('AAD_PROVISIONING');
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

## Enabling Snowflake-initiated SSO

The SCIM provisioning process does not automatically enable single sign-on (SSO).

To use SSO after the SCIM provisioning process is complete, enable
[Snowflake-initiated SSO](admin-security-fed-auth-security-integration).

## Managing SCIM network policies

Applying a network policy to a SCIM security integration allows the SCIM network policy to be distinct from network policies that apply to the entire Snowflake account.
It allows the SCIM provider to provision users and groups without adding IP addresses to a network policy that controls access for normal users.

A network policy applied to a SCIM integration overrides a network policy applied to the entire Snowflake account.

After creating the SCIM security integration, create the SCIM network policy using this command:

> CopyExpand
>
> ```
> alter security integration aad_provisioning set network_policy = <scim_network_policy>;
> ```
>
> Show lessSee more
>
> Scroll to top

To unset the SCIM network policy, use this command:

> CopyExpand
>
> ```
> alter security integration aad_provisioning unset network_policy;
> ```
>
> Show lessSee more
>
> Scroll to top

Where:

`aad_provisioning`
:   Specifies the name of the Microsoft Entra ID SCIM security integration.

`scim_network_policy`
:   Specifies the Microsoft Entra ID SCIM network policy in Snowflake.

For more information, see [Controlling network traffic with network policies](network-policies) and [ALTER SECURITY INTEGRATION](../sql-reference/sql/alter-security-integration-scim).

## Using secondary roles with SCIM

Snowflake supports setting the [user](../sql-reference/sql/create-user) property `DEFAULT_SECONDARY_ROLES` to `'ALL'` with
SCIM to allow users to use [secondary roles](security-access-control-overview.html#label-access-control-role-enforcement) in a Snowflake session.

For a representative example, see [Update a user](scim-user-api-reference.html#label-scim-put-users).

## Replicating the Microsoft Entra ID SCIM security integration

Snowflake supports replication and failover/failback with the SCIM security integration from the source account to the target account.

For details, see [Replication of security integrations & network policies across multiple accounts](account-replication-security-integrations).

## Troubleshooting

* To verify that Microsoft Entra ID is sending updates to Snowflake, check the log events in Microsoft Entra ID for the Snowflake application and
  the SCIM audit logs in Snowflake to ensure Snowflake is receiving updates from Microsoft Entra ID. Use the following SQL to query the
  Snowflake SCIM audit logs, where `demo_db` is the name of your database.

  > CopyExpand
  >
  > ```
  > USE ROLE ACCOUNTADMIN;
  > USE SCHEMA snowflake.information_schema;
  >
  > SELECT * FROM TABLE(REST_EVENT_HISTORY('scim'));
  >
  > SELECT *
  >   FROM TABLE(REST_EVENT_HISTORY(
  >   'scim',
  >   DATEADD('MINUTES',-5,CURRENT_TIMESTAMP()),
  >   CURRENT_TIMESTAMP(),
  >   200))
  >   ORDER BY event_timestamp;
  > ```
  >
  > Show lessSee more
  >
  > Scroll to top
* If the user update fails, check the ownership of the user in Snowflake. If it is not owned by the `aad_provisioner` role (or the role set in the `run_as_role` parameter when creating the security integration in Snowflake), then the update will fail. Transfer the ownership by running the following SQL statement in Snowflake, and try again.

  > CopyExpand
  >
  > ```
  > grant ownership on user <username> to role AAD_PROVISIONER;
  > ```
  >
  > Show lessSee more
  >
  > Scroll to top
* If there are changes to the `UPN`
  [attribute value](https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/plan-connect-userprincipalname#what-is-userprincipalname)
  in Microsoft Entra ID after the initial SCIM provisioning, subsequent updates to the user will not work. A change to the `UPN`
  attribute value breaks the link between the Microsoft Entra ID user object and the Snowflake user object. If a change to the UPN attribute
  value occurs, reprovision the user with the correct `UPN` attribute value.

**Next topics:**

* [SCIM API references](scim-api-references)
