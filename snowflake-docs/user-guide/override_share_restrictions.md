---
title: "Override share restrictions"
url: "https://docs.snowflake.com/en/user-guide/override_share_restrictions"
---

# Override share restrictions

[![Snowflake logo in black (no text)](../_images/logo-snowflake-black.png)](../_images/logo-snowflake-black.png) Provider sharing not enabled for all accounts

Provider sharing is enabled by default for most, but not all accounts.

If you encounter errors when attempting to share data with consumers, the feature may not be enabled for your account. To inquire about
enabling it, please contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

To allow sharing data from a Business Critical account to a non-Business Critical account, or from a HIPAA-compliant account to a non-HIPAA-
compliant account, a user with a role granted the OVERRIDE SHARE RESTRICTIONS global privilege can specify the SHARE\_RESTRICTIONS parameter
for a specific share offered by their provider account.

## Grant the OVERRIDE SHARE RESTRICTIONS privilege to another role

The OVERRIDE SHARE RESTRICTIONS global privilege is granted to the ACCOUNTADMIN role by default, but it can be granted to other roles.
To grant OVERRIDE SHARE RESTRICTIONS to a role, use the ACCOUNTADMIN role and the [GRANT <privileges> … TO ROLE](../sql-reference/sql/grant-privilege) command.

Syntax:

`GRANT OVERRIDE SHARE RESTRICTIONS ON ACCOUNT TO ROLE <role_name>`

Where:

`<role_name>` is the role to which the privilege is granted.

For example, to grant the privilege to the SYSADMIN role:

CopyExpand

```
use role accountadmin;
grant override share restrictions on account to role sysadmin;
```

Show lessSee more

Scroll to top

## Set the SHARE\_RESTRICTIONS parameter on a share

As a provider in a Business Critical account, you can share data with a consumer in a non-Business Critical account using the
SHARE\_RESTRICTIONS parameter on a direct share. This parameter also applies to HIPAA-compliant providers that share data with a non-HIPAA consumer.

You must use the ACCOUNTADMIN role, or use a custom role granted the following privileges:

* The OVERRIDE SHARE RESTRICTIONS global privilege.
* OWNERSHIP on the share or the CREATE SHARE global privilege.

Use the [ALTER SHARE](../sql-reference/sql/alter-share) command to set the SHARE\_RESTRICTIONS parameter on a share:

For example, to update the share `my_share` to add a non-Business Critical or non-HIPAA consumer account `consumerorg.consumeraccount`,
run the following:

CopyExpand

```
use role sysadmin;
alter share my_share add accounts = consumerorg.consumeraccount SHARE_RESTRICTIONS=false;
```

Show lessSee more

Scroll to top

See [ALTER SHARE](../sql-reference/sql/alter-share) for more details.

Attention

Snowflake is not responsible for ensuring that HIPAA (and HITRUST) accounts who engage in data sharing have a signed BAA with each other;
this is at the discretion of the accounts that are sharing data. Note that failure to have a signed BAA may impact the HIPAA (and HITRUST)
compliance of both accounts, particularly the provider account.

Also, if you have Business Critical account, to maintain the expected level of data protection provided by Business Critical, we
strongly recommend considering the following before requesting Snowflake to enable Secure Data Sharing with non-Business Critical
accounts:

* Do not share sensitive data with non-Business Critical accounts.
* Consider creating a second, non-Business Critical account where you store less sensitive data and share this data with non-Business
  Critical accounts.
* If you are using [Tri-Secret Secure](security-encryption-tss) with your Business Critical account and you share data with other accounts, Snowflake
  treats the data access from these accounts as if the access occurred from within your own account. Specifically, granting access to
  the consumer account might require Snowflake to access the key management service on the cloud platform that hosts your Snowflake
  account.

These are only recommendations and are not enforced by Snowflake. The decision to share data is always at the discretion of the data
provider and Snowflake does not assume any responsibility for data that is improperly shared.
