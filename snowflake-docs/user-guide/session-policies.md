---
title: "Snowflake sessions and session policies"
url: "https://docs.snowflake.com/en/user-guide/session-policies"
---

# Snowflake sessions and session policies

[![Snowflake logo in black (no text)](../_images/logo-snowflake-black.png)](../_images/logo-snowflake-black.png) [Enterprise Edition Feature](intro-editions)

Session policies require Enterprise Edition or higher. To inquire about upgrading, contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

This topic describes Snowflake sessions and session policies and provides instructions for configuring session policies at the account or
user level.

## Snowflake sessions

A session begins when a user connects to Snowflake and authenticates successfully using a Snowflake programmatic client or [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
A session is independent of an identity provider (IdP) session. If the Snowflake session expires but the IdP session remains active,
a user can log in to Snowflake without entering their login credentials again (i.e. silent authentication).

A session is maintained indefinitely with continued user activity. After a period of inactivity in the session, known as the
idle session timeout, the user must authenticate to Snowflake again. The idle session timeout has a maximum value of four hours and
a session policy can modify the idle session timeout period. The idle session timeout applies to the following:

* [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
* [Snowflake CLI](../developer-guide/snowflake-cli/index).
* [SnowSQL (CLI client)](snowsql).
* Supported [connectors and drivers](../guides-overview-connecting).
* Third-party clients that connect to Snowflake using a supported connector or driver.

Snowflake recommends reusing existing sessions when possible and to close the connection to Snowflake when a session is no longer needed.

### Snowsight session expiration and logout behavior

* A Snowsight session remains active as long as the user is interacting with the application and has not exceeded the configured idle
  session timeout.
* The session idle timeout is controlled by your organization’s session policy (the default is 4 hours). If there is no activity for longer
  than this period, the session will expire and you will be logged out automatically.
* In addition to idle timeout, session persistence is also affected by authentication cookies:

  > + In most cases, closing and reopening your browser will end your Snowsight session, regardless of your idle time.
  > + If your authentication cookie expires (typically after 24 hours), you will be required to log in again, even if you have not been idle
  >   for longer than the session timeout.
* If your network connection is lost or you attempt to access Snowsight from a disallowed network, your session may be closed and you will be logged out.
* When a session is closed for any reason, any running queries or jobs associated with that session will be terminated after a short delay
  (usually within a few minutes).

Note

Session expiration can occur due to idle timeout, cookie expiration, browser restarts, or network policy violations. Closing your browser
or being inactive for an extended period may require you to log in again, even if you have not reached the configured idle timeout.

### Monitor session usage

You can monitor active sessions and session usage using Snowsight or a SQL view. You can view your own sessions,
or use a role with access to view the SESSIONS view to view sessions for your account. See [ACCOUNT\_USAGE schema SNOWFLAKE database roles](../sql-reference/account-usage.html#label-account-usage-snowflake-db-roles).

SQL:
:   Query the [SESSIONS](../sql-reference/account-usage/sessions) view in the ACCOUNT USAGE schema
    of the shared SNOWFLAKE database to monitor session usage.

Snowsight:
:   In the navigation menu, select Governance & security » Network policies, and then select the Sessions tab.
    You can review the session ID, user name, start time, client driver in use for the session, client net address, and authentication method.
    Hover over the start time to view the exact date and time that the session started, in your local time zone.

## Session policies

A session policy defines the idle session timeout period in minutes and provides the option to override the default idle timeout
value. The timeout period begins upon a successful authentication to Snowflake. The minimum configurable idle timeout value for a session
policy is `5` minutes.

If a session policy is not set, Snowflake uses a default value of `240` minutes (four hours).

When the session expires, the user must authenticate to Snowflake again. However, Snowflake does not enforce any setting defined by the
[Custom logout endpoint](admin-security-fed-auth-security-integration.html#label-sso-custom-logout-endpoint).

The session policy can be set for an account or user with configurable idle timeout periods to address compliance requirements. If a user
is associated with both an account and user-level session policy, the user-level session policy takes precedence. After the session policy
is set on the account or user, Snowflake enforces the session policy.

There are two properties that govern the session policy behavior:

* `SESSION_IDLE_TIMEOUT_MINS` for programmatic and Snowflake clients.
* `SESSION_UI_IDLE_TIMEOUT_MINS` for Snowsight.

For more information, see [Managing session policies](../sql-reference/ddl-user-security.html#label-session-policy-ddl).

### Secondary roles in a session policy

When a user connects to Snowflake and the session begins, the user can activate
[secondary roles](security-access-control-overview.html#label-access-control-role-enforcement) with a [USE SECONDARY ROLES](../sql-reference/sql/use-secondary-roles) command. However, as a
security administrator, you might want to manage the secondary roles that are available to an individual user, groups of users, or the
entire account. Managing secondary roles helps to scope the set of privileges available to a user for the duration of the session.

To meet these management needs, you can set the `ALLOWED_SECONDARY_ROLES` property in a session policy and set the session policy on
the account or a user in the account. This property controls the secondary roles that can be activated in a session. Setting this property
to an empty list `ALLOWED_SECONDARY_ROLES=()` disables secondary roles in a session.

For examples, see [Specifying secondary roles in a session policy](session-policies-using.html#label-session-policy-secondary-roles-examples).

Note

When you set the `ALLOWED_SECONDARY_ROLES` property in a session policy, the enforcement of the secondary roles begins immediately,
including existing sessions.

Prior to updating the session policy to limit secondary roles, consider your workload schedule and the access control for each
workload to avoid unnecessary workload disruption.

### Considerations

* If a client supports the CLIENT\_SESSION\_KEEP\_ALIVE option and the option is set to `TRUE`, the client preserves the Snowflake
  session indefinitely as long as the connection to Snowflake is active. Otherwise, if the option is set to `FALSE`, the session ends
  after 4 hours. When possible, avoid using this option since it can result in many open sessions and place a greater demand on resources
  which can lead to a performance degradation.
* You can use the [CLIENT\_SESSION\_KEEP\_ALIVE\_HEARTBEAT\_FREQUENCY](../sql-reference/parameters.html#label-client-session-keep-alive-heartbeat-frequency) parameter to specify the number of seconds
  between client attempts to update the token for the session. The web interface session can be refreshed as Snowflake objects continue to
  be used, such as executing DDL and DML statements. Snowflake checks for this behavior every 30 seconds.
* Creating a new worksheet or opening an existing worksheet continues to use the established user session but with its idle session timeout
  reset to 0.

### Limitations

Future grants:
:   [Future grants](../sql-reference/sql/grant-privilege.html#label-grant-privilege-schema-future-grants) of privileges on session policies are not supported.

    As a workaround, grant the APPLY SESSION POLICY privilege to a custom role to allow that role to apply session policies on a user or the
    Snowflake account.
