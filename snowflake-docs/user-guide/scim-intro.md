---
title: "Snowflake SCIM support"
url: "https://docs.snowflake.com/en/user-guide/scim-intro"
---

# Snowflake SCIM support

Snowflake supports SCIM 2.0, lets you integrate Snowflake with Okta and Microsoft Azure AD as identity providers. You can use
custom identity providers, which are identity providers that are neither Okta nor Microsoft Azure. You can provision users and groups
(roles) from the identity provider into Snowflake, which functions as the service provider.

Note

SCIM roles in Snowflake must own any users or roles that are imported from the identity provider. If the Snowflake SCIM role does not own
the imported users or roles, updates in the identity provider are not be synced to Snowflake. Snowflake SCIM roles correlate with their
identity provider (IdP):

* Okta SCIM Role: `okta_provisioner`
* Microsoft Entra ID SCIM Role: `aad_provisioner`
* Custom SCIM Role: `generic_scim_provisioner`

For more information on how to use the Snowflake SCIM Role, see the SCIM configuration sections for [Okta](scim-okta.html#label-scim-okta-config),
[Microsoft Entra ID](scim-azure.html#label-scim-azure-config), and the [Custom SCIM integration](scim-custom.html#label-scim-custom-config).

## Use cases

The Snowflake SCIM API can address the following use cases.

* [Managing users](scim-user-api-reference): Administrators can provision and manage their users from their organization’s
  identity provider to Snowflake. User management is a one-to-one mapping from the identity provider to Snowflake.
* [Managing groups](scim-group-api-reference): Administrators can provision and manage their groups (i.e. Roles) from
  their organization’s identity provider to Snowflake. Role management is a one-to-one mapping from the identity provider to Snowflake.
* [Auditing SCIM API requests](#label-scim-auditing-rest-api-requests): Administrators can query the `rest_event_history` table to determine whether the
  identity provider is sending updates (i.e. SCIM API requests) to Snowflake.

## SCIM API

Identity providers can use a SCIM client to make RESTful API requests to the Snowflake SCIM server. After validating the API request,
Snowflake performs actions requested by the identity providers on users or groups.

Snowflake authenticates SCIM API requests from identity providers through an OAuth Bearer token in the `Authorization` header of HTTP
requests. The token is valid for six months. You must ensure your token is not expired when authenticating. If your token expires, you can
generate a new access token using the [SYSTEM$GENERATE\_SCIM\_ACCESS\_TOKEN](../sql-reference/functions/system_generate_scim_access_token) function.

Caution

The Snowflake SCIM API lets administrators manage users and groups from the customer’s identity provider to Snowflake. If you make
changes to users and groups in Snowflake directly, the changes do not synchronize back to the customer’s identity provider.

For more information about making SCIM API requests to Snowflake, see [SCIM API references](scim-api-references).

## Auditing SCIM API requests

You can query Snowflake to find information about SCIM API requests that were made over a span of time. You can use this information to see
if your organization’s active users match the users provisioned into Snowflake.

For example, to determine which SCIM API requests were made in the last five minutes, with a maximum of 200 requests to be returned, you can
use the Information Schema table function [REST\_EVENT\_HISTORY](../sql-reference/functions/rest_event_history):

CopyExpand

```
use role accountadmin;
use database demo_db;
use schema information_schema;
select *
    from table(rest_event_history(
        'scim',
        dateadd('minutes',-5,current_timestamp()),
        current_timestamp(),
        200))
    order by event_timestamp;
```

Show lessSee more

Scroll to top

For more information on how to modify this query, see [DATEADD](../sql-reference/functions/dateadd) and
[CURRENT\_TIMESTAMP](../sql-reference/functions/current_timestamp).

## Supported SCIM security integrations

See [SCIM security integrations](scim-security-integrations).

## Replicating security integrations

Snowflake supports replication and failover/failback with the SCIM security integration from the source account to the target account.

For details, see [Replication of security integrations & network policies across multiple accounts](account-replication-security-integrations).
