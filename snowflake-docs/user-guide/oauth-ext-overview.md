---
title: "External OAuth overview"
url: "https://docs.snowflake.com/en/user-guide/oauth-ext-overview"
---

# External OAuth overview

This topic teaches you how to configure External OAuth servers that use OAuth 2.0 for accessing Snowflake.

External OAuth integrates the customer’s OAuth 2.0 server to provide a seamless SSO experience, enabling external client access to
Snowflake.

Snowflake supports the following external authorization servers, custom clients, and partner applications:

* [Okta](oauth-okta)
* [Microsoft Entra ID](oauth-azure)
* [Ping Identity PingFederate](oauth-pingfed)
* [External OAuth Custom Clients](oauth-ext-custom)
* [Microsoft Power BI](oauth-powerbi)
* [Sigma](oauth-ext-partner)

After configuring your organization’s External OAuth server, which includes any necessary [OAuth 2.0 Scopes](https://oauth.net/2/scope/)
mapping to Snowflake roles, the user can connect to Snowflake securely and programmatically without having to enter any additional
authentication or authorization factors or methods. The user’s access to Snowflake data is dependent on both their role and the role being
integrated into the access token for the session. For more information, refer to [Scopes](#scopes) (in this topic).

## Use cases and benefits

1. Snowflake delegates the token issuance to a dedicated authorization server to ensure that the OAuth Client and user properly
   authenticate. The result is centralized management of tokens issued to Snowflake.
2. Customers can integrate their policies for authentication (e.g. multi-factor, subnet, biometric) and authorization
   (e.g. no approval, manager approval required) into the authorization server. The result is greater security leading to more robust data
   protection by issuing challenges to the user. If the user doesn’t pass the policy challenge(s), the Snowflake session is not
   instantiated, and access to Snowflake data does not occur.
3. For programmatic clients that can access Snowflake and users that only initiate their Snowflake sessions through External OAuth, no
   additional authentication configuration (i.e. set a password) is necessary in Snowflake. The result is that service accounts or users
   used exclusively for programmatic access will only ever be able to use Snowflake data when going through the External OAuth configured
   service.
4. Clients can authenticate to Snowflake without browser access, allowing ease of integration with the External OAuth server.
5. Snowflake’s integration with External OAuth servers is cloud-agnostic.

   * It does not matter whether the authorization server exists in a cloud provider’s cloud or if the authorization server is on-premises.
     The result is that customers have many options in terms of configuring the authorization server to interact with Snowflake.

## General workflow

For each of the supported identity providers, the workflow for OAuth relating to External OAuth authorization servers can be summarized as
follows. Note that the first step only occurs once and the remaining steps occur with each attempt to access Snowflake data.

[![workflow overview](../_images/oauth-third-party.png)](../_images/oauth-third-party.png)

1. Configure your External OAuth authorization server in your environment and the security integration in Snowflake to establish a trust.
2. A user attempts to access Snowflake data through their business intelligence application, and the application attempts to verify the
   user.
3. On verification, the authorization server sends a JSON Web Token (i.e. OAuth token) to the client application.
4. The Snowflake driver passes a connection string to Snowflake with the OAuth token.
5. Snowflake validates the OAuth token.
6. Snowflake performs a user lookup.
7. On verification, Snowflake instantiates a session for the user to access data in Snowflake based on their role.

## Scopes

The scope parameter in the authorization server limits the operations and roles permitted by the access token and what the user can access
after instantiating a Snowflake session.

The ACCOUNTADMIN, GLOBALORGADMIN, ORGADMIN, and SECURITYADMIN roles are blocked by default. If it is necessary to use one or more of these roles,
use the [ALTER ACCOUNT](../sql-reference/sql/alter-account) command to set the [EXTERNAL\_OAUTH\_ADD\_PRIVILEGED\_ROLES\_TO\_BLOCKED\_LIST](../sql-reference/parameters.html#label-external-oauth-add-privileged-roles-to-blocked-list) account
parameter to FALSE.

* For Okta, PingFederate, and Custom, use the role scope pattern in the following table.
* For Microsoft Entra ID, refer to [Determine the OAuth flow in Microsoft Entra ID](oauth-azure.html#label-ext-oauth-azure-flow)
* If you do not want to manage Snowflake roles in your External OAuth server, pass the static value of SESSION:ROLE-ANY in the scope
  attribute of the token.

The following table summarizes External OAuth scopes. Note that
if you do not define a scope, the connection attempt to Snowflake will fail.

| Scope/Role Connection Parameter | Description |
| --- | --- |
| `session:role-any` | Maps to the ANY role in Snowflake.  Use this scope if the user’s default role in Snowflake is desirable.  The `external_oauth_any_role_mode` security integration parameter must be configured in order to enable ANY role for a given External OAuth Provider. For configuration details, refer to the ANY role section in [Okta](oauth-okta.html#label-any-role-okta), [Microsoft Entra ID](oauth-azure.html#label-any-role-aad), [PingFederate](oauth-pingfed.html#label-any-role-pingfed), or [Custom](oauth-ext-custom.html#label-any-role-ext-oauth-custom).  Note that with a [Power BI to Snowflake integration](oauth-powerbi), a PowerBI user cannot switch roles using this scope. |
| `session:role:custom_role` | Maps to a custom Snowflake role. For example, if your custom role is ANALYST, your scope is `session:role:analyst`. |
| `session:role:public` | Maps to the PUBLIC Snowflake role. |

See moreShow less

Expand

### Using secondary roles with External OAuth

Snowflake supports using [secondary roles](security-access-control-overview.html#label-access-control-role-enforcement) with External OAuth.

Snowflake OAuth does not support in-session role switching to secondary roles.

For more information, refer to:

* [Secondary roles with Okta](oauth-okta.html#label-secondary-roles-okta)
* [Secondary roles with Microsoft Entra ID](oauth-azure.html#label-secondary-roles-azure)
* [Secondary roles with PingFederate](oauth-pingfed.html#label-secondary-roles-pingfed)
* [Secondary roles with Custom Clients](oauth-ext-custom.html#label-secondary-roles-ext-custom)
* [Using secondary roles with Power BI SSO to Snowflake](oauth-powerbi.html#label-secondary-roles-powerbi)

## Configuring External OAuth support

Snowflake supports the use of partner applications and custom clients that support External OAuth.

Refer to the list below if you need to configure partner applications or custom clients:

* [Configuring partner applications](oauth-ext-partner).
* [Configuring custom clients configured by your organization](oauth-ext-custom).

## Restricting network traffic for External OAuth

You can associate a [network policy](network-policies) with the External OAuth security integration to restrict network traffic from the client to Snowflake as the resource server. This network policy governs login requests and queries against Snowflake.

When you associate a network policy with the security integration, it overrides network policies associated with the user or the account. For more information, see [Network policy precedence](network-policies.html#label-network-policy-precedence).

To associate a network policy with the External OAuth security integration, set the NETWORK\_POLICY parameter when creating or updating the integration. For example:

CopyExpand

```
CREATE SECURITY INTEGRATION external_oauth_azure_1
  TYPE = external_oauth
  ENABLED = true
  EXTERNAL_OAUTH_TYPE = azure
  EXTERNAL_OAUTH_ISSUER = '<AZURE_AD_ISSUER>'
  EXTERNAL_OAUTH_JWS_KEYS_URL = '<AZURE_AD_JWS_KEY_ENDPOINT>'
  EXTERNAL_OAUTH_TOKEN_USER_MAPPING_CLAIM = 'upn'
  EXTERNAL_OAUTH_SNOWFLAKE_USER_MAPPING_ATTRIBUTE = 'login_name'
  NETWORK_POLICY = 'allow_private_ip_only';
```

Show lessSee more

Scroll to top

## Error codes

Refer to the table below for descriptions of error codes associated with External OAuth:

| Error Code | Error | Description |
| --- | --- | --- |
| 390318 | OAUTH\_ACCESS\_TOKEN\_EXPIRED | OAuth access token expired. {0} |
| 390144 | JWT\_TOKEN\_INVALID | JWT token is invalid. |

See moreShow less

Expand

## Troubleshooting

* Use the [SYSTEM$VERIFY\_EXTERNAL\_OAUTH\_TOKEN](../sql-reference/functions/system_verify_ext_oauth_token) function to determine whether your External OAuth access token is
  valid or needs to be regenerated.
* If you encounter an error message associated with a failed External OAuth login attempt, and the error message has a UUID, you can
  ask an
  administrator that has a MONITOR privilege assigned to their role to use the UUID from the error message to get a more detailed
  description of the error using the [SYSTEM$GET\_LOGIN\_FAILURE\_DETAILS](../sql-reference/functions/system_get_login_failure_details.html#label-system-get-login-failure-details-example)
  function.
