---
title: "Identifier-first login"
url: "https://docs.snowflake.com/en/user-guide/identifier-first-login"
---

# Identifier-first login

Identifier-first login allows Snowflake to identify a user *before* presenting authentication options. In this flow, Snowflake prompts the
user for their email address or username only, then displays authentication options based on the identity of the user.

An identifier-first login reduces confusion and login issues by only showing users’ valid authentication options. For example, identifier-first
login can do the following:

* In an environment that uses [multiple identity providers](admin-security-fed-auth-security-integration-multiple), it
  can restrict single sign-options to include only those identity providers that are associated with the user.
* It can hide the password option for users without passwords, who instead need to be using an identity provider to authenticate.

For examples of how authentication policies and the identifier-first login can be combined to customize the login experience for users, see
[Combining identifier-first login with authentication policies](authentication-policies.html#label-auth-policy-identifier-first-login-flow).

## Enable identifier-first login

A user with the ACCOUNTADMIN role can use the [ENABLE\_ICEBERG\_MERGE\_ON\_READ](../sql-reference/parameters.html#label-enable-identifier-first-login) parameter to enable the identifier-first login
flow for an account. For example:

CopyExpand

```
USE ROLE ACCOUNTADMIN;
ALTER ACCOUNT SET ENABLE_IDENTIFIER_FIRST_LOGIN = true;
```

Show lessSee more

Scroll to top
