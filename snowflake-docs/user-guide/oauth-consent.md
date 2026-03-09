---
title: "Managing user consent for OAuth"
url: "https://docs.snowflake.com/en/user-guide/oauth-consent"
---

# Managing user consent for OAuth

This topic describes how to manage delegated authorizations for OAuth, that is, user consent given to one or more clients associated with
Snowflake integrations for a specified role.

## Adding delegated authorizations

Adding a delegated authorization to a user pre-authorizes consent to initiate a session using a specified role for a particular
integration. Without the delegated authorization, the user must authorize consent for the role after authentication. Note that a delegated
authorization only bypasses the authorization step for a given role; a user must always authenticate to request an authorization code.

The ability to add delegated authorizations is limited to custom clients. For public clients (that is, Tableau Cloud or Desktop), Snowflake
always displays the confirmation dialog for a given role.

Add user consent for a role using [ALTER USER](../sql-reference/sql/alter-user) with the ADD DELEGATED AUTHORIZATION keywords:

CopyExpand

```
ALTER USER <username> ADD DELEGATED AUTHORIZATION
    OF ROLE <role_name>
    TO SECURITY INTEGRATION <integration_name>;
```

Show lessSee more

Scroll to top

Where:

`username`
:   Specifies the user whose consent you are adding.

`role_name`
:   Specifies the role associated with the access token.

`integration_name`
:   Specifies the integration associated with the access tokens for a specific client.

Note

Only security administrators (that is, users with the SECURITYADMIN role) or higher can execute this SQL command.

For example, add user consent for the CUSTOM1 role to user JANE.SMITH for the MYINT integration:

CopyExpand

```
ALTER USER jane.smith ADD DELEGATED AUTHORIZATION
    OF ROLE custom1
    TO SECURITY INTEGRATION myint;
```

Show lessSee more

Scroll to top

## Viewing delegated authorizations

List the active delegated authorizations for which you have access privileges, using
[SHOW DELEGATED AUTHORIZATIONS](../sql-reference/sql/show-delegated-authorizations):

CopyExpand

```
SHOW DELEGATED AUTHORIZATIONS;

+-------------------------------+-----------+-----------+-------------------+--------------------+
| created_on                    | user_name | role_name | integration_name  | integration_status |
+-------------------------------+-----------+-----------+-------------------+--------------------+
| 2018-11-27 07:43:10.914 -0800 | JSMITH    | PUBLIC    | MY_OAUTH_INT      | ENABLED            |
+-------------------------------+-----------+-----------+-------------------+--------------------+
```

Show lessSee more

Scroll to top

List the active delegated authorizations for a specified user. Users can list their own delegated authorizations; otherwise, this command
variant requires the OWNERSHIP privilege on the user.

CopyExpand

```
SHOW DELEGATED AUTHORIZATIONS
    BY USER <username>;
```

Show lessSee more

Scroll to top

List the active delegated authorizations for a specified integration. This command variant requires the OWNERSHIP privilege on the
integration (that is, the ACCOUNTADMIN role):

CopyExpand

```
SHOW DELEGATED AUTHORIZATIONS
    TO SECURITY INTEGRATION <integration_name>;
```

Show lessSee more

Scroll to top

## Revoking delegated authorizations

A user can revoke consent from a specified integration. This has the effect of revoking any access token associated with the integration.

To revoke user consent for a given integration, execute the ALTER USER … REMOVE DELEGATED AUTHORIZATIONS command.

Note

Only security administrators (that is, users with the SECURITYADMIN role) or higher can execute this SQL command.

CopyExpand

```
ALTER USER <username>
  REMOVE DELEGATED AUTHORIZATIONS
  FROM SECURITY INTEGRATION <integration_name>
```

Show lessSee more

Scroll to top

To revoke user consent associated with a specific role, include the `OF ROLE role_name` parameter in the statement:

CopyExpand

```
ALTER USER <username>
  REMOVE DELEGATED AUTHORIZATION OF ROLE <role_name>
  FROM SECURITY INTEGRATION <integration_name>
```

Show lessSee more

Scroll to top

Where:

`username`
:   Specifies the user whose consent you are revoking.

`role_name`
:   Specifies the role associated with the access token.

`integration_name`
:   Specifies the integration associated with the access tokens for a specific client.

For example, remove user consent for the CUSTOM1 role from user JANE.SMITH for the MYINT integration:

CopyExpand

```
ALTER USER jane.smith
  REMOVE DELEGATED AUTHORIZATION OF ROLE custom1
  FROM SECURITY INTEGRATION myint;
```

Show lessSee more

Scroll to top
