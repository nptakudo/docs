---
title: "Authentication policies"
url: "https://docs.snowflake.com/en/user-guide/authentication-policies"
---

# Authentication policies

Authentication policies provide you with control over how a client or user authenticates by allowing you to specify:

* Whether users must [enroll in multi-factor authentication (MFA)](ui-snowsight-profile.html#label-snowsight-set-up-mfa).
* Which authentication methods require multi-factor authentication.
* The allowed authentication methods, such as [SAML](admin-security-fed-auth-overview), passwords,
  [OAuth](oauth-intro), [key pair authentication](key-pair-auth), and
  [programmatic access tokens](programmatic-access-tokens).
* The [SAML2 security integrations](admin-security-fed-auth-security-integration) that are available to users during the
  login experience. For example, if there are multiple security integrations, you can specify which identity provider (IdP) can be selected
  and used to authenticate.

  If you are using authentication policies to control which IdP a user can use to authenticate, you can further refine that control using
  the `ALLOWED_USER_DOMAINS` and `ALLOWED_EMAIL_PATTERNS` properties of the SAML2 security integrations associated with the
  IdPs. For more details, see [Using multiple identity providers for federated authentication](admin-security-fed-auth-security-integration-multiple).
* The clients that users can use to connect to Snowflake, such as [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in), [Snowflake CLI](../developer-guide/snowflake-cli/index), [drivers](../developer-guide/drivers), or
  [SnowSQL (CLI client)](snowsql). The `CLIENT_TYPES` property of an authentication policy is a best-effort method to block user logins based on specific clients. It should not be used as the sole control to establish a security boundary. Notably, it does not restrict access to the Snowflake REST APIs..

  By defining a “client policy” within an authentication policy, you can also set the minimum version that is allowed for specific client types.
* The [default and maximum expiration times](programmatic-access-tokens.html#label-pat-configure-settings) and the
  [network policy requirements](programmatic-access-tokens.html#label-pat-prerequisites-network) for programmatic access tokens.

You can set authentication policies on the account or users in the account. If you set an authentication policy on the account, then the
authentication policy applies to all users in the account. If you set an authentication policy on both an account and a user, then the
user-level authentication policy overrides the account-level authentication policy.

Note

If you already have access to the identifier-first login flow, you need to migrate your account from the unsupported
SAML\_IDENTITY\_PROVIDER account parameter using the [SYSTEM$MIGRATE\_SAML\_IDP\_REGISTRATION](../sql-reference/functions/system_migrate_saml_idp_registration) function.

## Use cases

The following non-exhaustive list describes use cases for authentication policies:

* You want to control whether a user, all users in an account, or specific authentication methods require MFA.
* You want to control the user login flows when there are multiple login options.
* You want to control the authentication methods, specific client types, minimum versions of clients, and security integrations available
  to specific users or all users.
* You have customers building services on top of Snowflake using Snowflake drivers, but the customers do not want their users accessing
  Snowflake through Snowsight.
* You want to offer multiple identity providers as authentication options for specific users.

## Limitations

* The `CLIENT_TYPES` property of an authentication policy is a best-effort method to block user logins based on specific clients. It should not be used as the sole control to establish a security boundary. Notably, it does not restrict access to the Snowflake REST APIs..

## Considerations

* Ensure authentication methods and security integrations listed in your authentication policies do not conflict. For example, if you add a
  SAML2 security integration in the list of allowed security integrations, and you only allow OAuth as an allowed authentication method,
  then you cannot create an authentication policy.
* Use an additional non-restrictive authentication policy for administrators in case users are locked out. For an example, see
  [Preventing a lockout](#label-authentication-policy-admin-policy).

## Security policy precedence

When more than one type of security policy is activated, precedence between the policies occur. For example,
[network policies](network-policies) take precedence over authentication policies, so if the IP address of a request
matches an IP address in the blocked list of the network policy, then the authentication policy is not checked, and evaluation stops at the
network policy.

The following list describes the order in which security policies are evaluated:

1. [Network policies](../sql-reference/sql/create-network-policy): Allow or deny IP addresses, VPC IDs, and VPCE IDs.
2. Authentication policies - Allow or deny clients, authentication methods, and security integrations.
3. [Password policies](../sql-reference/sql/create-password-policy) (For local authentication only): Specify password requirements such
   as character length, characters, password age, retries, and lockout time.
4. [Session policies](../sql-reference/sql/create-session-policy): Require users to re-authenticate after a period of inactivity

If a policy is assigned to both the account and the user authenticating, the user-level policy is enforced.

## Combining identifier-first login with authentication policies

By default, Snowsight provides a generic login experience that provides several options for logging in,
regardless if the options are relevant to users. This means that authentication is attempted regardless of whether the login option is a
valid option for the user.

You can alter this behavior to enable a identifier-first login flow for Snowsight. In this flow, Snowflake prompts the user for an
email address or username before presenting authentication options. Snowflake uses the email address or username to identify the user, and
then only displays the login options that are relevant to the user, and are allowed by the authentication policy set on the account or user.

For instructions for enabling the identifier-first login flow, see [Identifier-first login](identifier-first-login).

The following table provides example configuration on how the identifier-first login and authentication policies can be combined to control
the login experience of the user.

| Configuration | Result |
| --- | --- |
| The authentication policy’s AUTHENTICATION\_METHODS parameter only contains PASSWORD. | Snowflake prompts the user for an email address or username, and password. |
| The authentication policy’s AUTHENTICATION\_METHODS parameter only contains SAML, and there is an active SAML2 security integration. | Snowflake redirects the user to the identity provider’s login page if the email address or username matches only one SAML2 security integration. |
| The authentication policy’s AUTHENTICATION\_METHODS parameter contains both PASSWORD and SAML, and there is an active SAML2 security integration. | Snowflake displays a SAML SSO button if the email address or username matches only one SAML2 security integration, and the option to log in with an email address or username, and password. |
| The authentication policy’s AUTHENTICATION\_METHODS parameter only contains SAML, and there are multiple active SAML2 security integrations. | Snowflake displays multiple SAML SSO buttons if the email address or username matches multiple SAML2 security integrations. |
| The authentication policy’s AUTHENTICATION\_METHODS parameter contains both PASSWORD and SAML, and there are multiple active SAML2 security integrations. | Snowflake displays multiple SAML SSO buttons if the email address or username matches multiple SAML2 security integrations, and the option to log in with an email address or username, and password. |

See moreShow less

Expand

## Creating an authentication policy

An administrator can use the [CREATE AUTHENTICATION POLICY](../sql-reference/sql/create-authentication-policy) command to create a new authentication policy,
specifying which clients can connect to Snowflake, which authentication methods can be used, and which security integrations are
available to users. By default, all client types, authentication methods, and security integrations can be used
to connect to Snowflake. The `CLIENT_TYPES` property of an authentication policy is a best-effort method to block user logins based on specific clients. It should not be used as the sole control to establish a security boundary. Notably, it does not restrict access to the Snowflake REST APIs..

For example, the following commands create a custom `policy_admin` role and an authentication policy that allows
authentication using Snowsight. The user must authenticate with SAML or a password.

Note

To run this example, you must replace `<username>` in the GRANT ROLE command with your login username.

CopyExpand

```
USE ROLE ACCOUNTADMIN;

CREATE OR REPLACE DATABASE my_database;
USE DATABASE my_database;

CREATE OR REPLACE SCHEMA my_schema;
USE SCHEMA my_schema;

CREATE ROLE policy_admin;

GRANT USAGE ON DATABASE my_database TO ROLE policy_admin;
GRANT USAGE ON SCHEMA my_database.my_schema TO ROLE policy_admin;
GRANT CREATE AUTHENTICATION POLICY ON SCHEMA my_database.my_schema TO ROLE policy_admin;
GRANT APPLY AUTHENTICATION POLICY ON ACCOUNT TO ROLE policy_admin;

GRANT ROLE policy_admin TO USER <username>;
USE ROLE policy_admin;

CREATE AUTHENTICATION POLICY my_example_authentication_policy
  CLIENT_TYPES = ('SNOWFLAKE_UI')
  AUTHENTICATION_METHODS = ('SAML', 'PASSWORD');
```

Show lessSee more

Scroll to top

For detailed examples, see [Example login configurations](#label-authentication-policies-example-login-configurations).

## Setting an authentication policy on an account or user

When you set an authentication policy on an account or user, the restrictions specified in the authentication policy apply to the account or
user. You can use the [ALTER ACCOUNT](../sql-reference/sql/alter-account) or [ALTER USER](../sql-reference/sql/alter-user) commands to set an authentication
policy on an account or user.

In a Snowsight worksheet, use either of the following commands to set an authentication policy on an account or
user:

CopyExpand

```
ALTER ACCOUNT SET AUTHENTICATION POLICY my_example_authentication_policy;
```

Show lessSee more

Scroll to top

CopyExpand

```
ALTER USER example_user SET AUTHENTICATION POLICY my_example_authentication_policy;
```

Show lessSee more

Scroll to top

You can also set an authentication policy on all users of a [specific type](admin-user-management.html#label-user-management-types). For example, to set an
authentication policy on all users of type SERVICE within the account, but not on users of type PERSON, run the following command:

CopyExpand

```
ALTER ACCOUNT SET AUTHENTICATION POLICY my_example_authentication_policy
  FOR ALL SERVICE USERS;
```

Show lessSee more

Scroll to top

Only a security administrator (a user with the SECURITYADMIN role) or users with a role that has the APPLY
AUTHENTICATION POLICY privilege can set authentication policies on accounts or users. To grant this privilege to a role so users can set
an authentication policy on an account or user, execute one of the following commands:

CopyExpand

```
GRANT APPLY AUTHENTICATION POLICY ON ACCOUNT TO ROLE my_policy_admin;
```

Show lessSee more

Scroll to top

CopyExpand

```
GRANT APPLY AUTHENTICATION POLICY ON USER example_user TO ROLE my_policy_admin;
```

Show lessSee more

Scroll to top

For detailed examples, see [Example login configurations](#label-authentication-policies-example-login-configurations).

## Hardening user or account authentication using MFA

To improve the security of user logins, you can create an authentication policy that requires users to
[enroll in MFA](ui-snowsight-profile.html#label-snowsight-set-up-mfa), and then apply the authentication policy to individual users or the account. After users
enroll in MFA, the authentication policy requires users to authenticate with MFA.

Note

Snowflake is [deprecating single-factor password logins](security-mfa-rollout). When the rollout is complete, all users
who authenticate with a password must enroll in MFA.

Run the following command if you want to create an authentication policy that requires **password users** to authenticate with MFA when using any Snowflake client, not just Snowsight. Single sign-on (SSO) users won’t be required to use MFA.

CopyExpand

```
CREATE AUTHENTICATION POLICY require_mfa_authentication_policy
  MFA_ENROLLMENT = 'REQUIRED'
  MFA_POLICY=  (
    ENFORCE_MFA_ON_EXTERNAL_AUTHENTICATION = 'NONE'
  );
```

Show lessSee more

Scroll to top

Run the following command if you want to create an authentication policy that requires **password and single sign-on users** to authenticate with MFA.

CopyExpand

```
CREATE AUTHENTICATION POLICY require_mfa_authentication_policy
  MFA_ENROLLMENT = 'REQUIRED'
  MFA_POLICY=  (
    ENFORCE_MFA_ON_EXTERNAL_AUTHENTICATION = 'ALL'
  );
```

Show lessSee more

Scroll to top

To set this authentication policy for all users in an account, execute the following SQL statement:

CopyExpand

```
ALTER ACCOUNT SET AUTHENTICATION POLICY require_mfa_authentication_policy;
```

Show lessSee more

Scroll to top

Note

If you set the `MFA_ENROLLMENT` parameter, then the `CLIENT_TYPES` parameter must include
`SNOWFLAKE_UI`, because Snowsight is the only place users can
[enroll in multi-factor authentication (MFA)](ui-snowsight-profile.html#label-snowsight-set-up-mfa).

## Tracking authentication policy usage

Use the Information Schema table function [POLICY\_REFERENCES](../sql-reference/functions/policy_references) to return a row for each user that is assigned
to the specified authentication policy and a row for the authentication policy assigned to the Snowflake account.

The following syntax is supported for authentication policies:

CopyExpand

```
POLICY_REFERENCES( POLICY_NAME => '<authentication_policy_name>' )
```

Show lessSee more

Scroll to top

CopyExpand

```
POLICY_REFERENCES( REF_ENTITY_DOMAIN => 'USER', REF_ENTITY_NAME => '<username>')
```

Show lessSee more

Scroll to top

CopyExpand

```
POLICY_REFERENCES( REF_ENTITY_DOMAIN => 'ACCOUNT', REF_ENTITY_NAME => '<accountname>')
```

Show lessSee more

Scroll to top

Where `authentication_policy_name` is the fully qualified name of the authentication policy.

For example, execute the following query to return a row for each user that is assigned the authentication policy named
`authentication_policy_prod_1`, which is stored in the database named `my_db` and the schema named `my_schema`:

CopyExpand

```
SELECT *
FROM TABLE(
  my_db.INFORMATION_SCHEMA.POLICY_REFERENCES(
  POLICY_NAME => 'my_db.my_schema.authentication_policy_prod_1'
  )
);
```

Show lessSee more

Scroll to top

## Preventing a lockout

In situations where the authentication policy governing an account is strict, you can create a non-restrictive authentication policy for
an administrator to use as a recovery option in case of a lockout caused by a security integration. For example, you can include the
`PASSWORD` authentication method for the administrator only. The user-level authentication policy overrides the more restrictive
account-level policy.

CopyExpand

```
CREATE AUTHENTICATION POLICY admin_authentication_policy
  AUTHENTICATION_METHODS = ('SAML', 'PASSWORD')
  CLIENT_TYPES = ('SNOWFLAKE_UI', 'SNOWFLAKE_CLI', SNOWSQL', 'DRIVERS')
  SECURITY_INTEGRATIONS = ('EXAMPLE_OKTA_INTEGRATION');
```

Show lessSee more

Scroll to top

You can then assign this policy to an administrator:

CopyExpand

```
ALTER USER <administrator_name> SET AUTHENTICATION POLICY admin_authentication_policy
```

Show lessSee more

Scroll to top

## Replication of authentication policies

You can replicate authentication policies using failover and replication groups. For details, see
[Replication and security policies](account-replication-considerations.html#label-account-replication-considerations-security-policies).

## Example login configurations

This section provides examples of how you can use and combine authentication policies and SAML2 security integrations to control login flow
and security.

### Restricting user access to Snowflake by client type

The `CLIENT_TYPES` property of an authentication policy is a best-effort method to block user logins based on specific clients. It should not be used as the sole control to establish a security boundary. Notably, it does not restrict access to the Snowflake REST APIs..

Create an authentication policy named `restrict_client_type_policy` that only allows access through Snowsight:

CopyExpand

```
CREATE AUTHENTICATION POLICY restrict_client_type_policy
  CLIENT_TYPES = ('SNOWFLAKE_UI')
  COMMENT = 'Only allows access through the web interface';
```

Show lessSee more

Scroll to top

Set the authentication policy on a user:

CopyExpand

```
ALTER USER example_user SET AUTHENTICATION POLICY restrict_client_type_policy;
```

Show lessSee more

Scroll to top

### Allow authentication from multiple identity providers on an account

Create a SAML2 security integration that allows users to log in through SAML using Okta as an IdP:

CopyExpand

```
CREATE SECURITY INTEGRATION example_okta_integration
  TYPE = SAML2
  SAML2_SSO_URL = 'https://okta.example.com';
  ...
```

Show lessSee more

Scroll to top

Create a security integration that allows users to log in through SAML using Microsoft Entra ID as an IdP:

CopyExpand

```
CREATE SECURITY INTEGRATION example_entra_integration
  TYPE = SAML2
  SAML2_SSO_URL = 'https://entra-example_acme.com';
  ...
```

Show lessSee more

Scroll to top

Create an authentication policy associated with the `example_okta_integration` and `example_entra_integration` integrations:

CopyExpand

```
CREATE AUTHENTICATION POLICY multiple_idps_authentication_policy
  AUTHENTICATION_METHODS = ('SAML')
  SECURITY_INTEGRATIONS = ('EXAMPLE_OKTA_INTEGRATION', 'EXAMPLE_ENTRA_INTEGRATION');
```

Show lessSee more

Scroll to top

Set the authentication policy on an account:

CopyExpand

```
ALTER ACCOUNT SET AUTHENTICATION POLICY multiple_idps_authentication_policy;
```

Show lessSee more

Scroll to top

## Privileges and commands

### Authentication Policy Privilege Reference

Snowflake supports the following authentication policy privileges to determine whether users can create, set, and own authentication
policies.

Operating on an object in a schema requires at least one privilege on the parent database and at least one privilege on the parent schema.

| Privilege | Object | Usage |
| --- | --- | --- |
| CREATE | Schema | Enables creating a new authentication policy in a schema. |
| APPLY AUTHENTICATION POLICY | Account | Enables applying an authentication policy at the account or user level. |
| OWNERSHIP | Authentication policy | Grants full control over the authentication policy. Required to alter most properties of an authentication policy. |

See moreShow less

Expand

### Authentication policy DDL reference

For details about authentication policy privileges and commands, see the following reference documentation:

| Command | Privilege | Description |
| --- | --- | --- |
| [CREATE AUTHENTICATION POLICY](../sql-reference/sql/create-authentication-policy) | CREATE AUTHENTICATION POLICY on SCHEMA | Creates a new authentication policy. |
| [ALTER AUTHENTICATION POLICY](../sql-reference/sql/alter-authentication-policy) | OWNERSHIP on AUTHENTICATION POLICY | Modifies an existing authentication policy. |
| [DROP AUTHENTICATION POLICY](../sql-reference/sql/drop-authentication-policy) | OWNERSHIP on AUTHENTICATION POLICY | Removes an existing authentication policy from the system. |
| [DESCRIBE AUTHENTICATION POLICY](../sql-reference/sql/desc-authentication-policy) | OWNERSHIP on AUTHENTICATION POLICY | Describes the properties of an existing authentication policy. |
| [SHOW AUTHENTICATION POLICIES](../sql-reference/sql/show-authentication-policies) | OWNERSHIP on AUTHENTICATION POLICY or USAGE on SCHEMA | Lists all of the authentication policies in the system. |

See moreShow less

Expand
