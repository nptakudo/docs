---
title: "Access control for cost anomalies"
url: "https://docs.snowflake.com/en/user-guide/cost-anomalies-access-control"
---

# Access control for cost anomalies

A cost anomaly occurs when daily consumption is above or below the expected range of consumption for the day. The following sections
describe the access control requirements for viewing and configuring cost anomalies.

## Administrators with system roles

Administrators with the following system roles can perform all tasks related to identifying and investigating cost anomalies, both in
Snowsight and by using the ANOMALY\_INSIGHTS class:

* ACCOUNTADMIN role in an ORGADMIN-enabled account or a regular account.
* GLOBALORGADMIN role in the organization account.

## Granting access to users

You can let users work with cost anomalies by granting application roles to them. The following application roles, which are within the
SNOWFLAKE application, let users work with cost anomalies.

| Application role | Description |
| --- | --- |
| APP\_USAGE\_VIEWER | Allows a user to view cost anomalies. |
| APP\_USAGE\_ADMIN | Allows a user to view cost anomalies and add email addresses where notifications are sent for [account-level cost anomalies](cost-anomalies.html#label-cost-anomaly-level). |
| ORGANIZATION\_BILLING\_VIEWER | When combined with the APP\_USAGE\_ADMIN or APP\_USAGE\_VIEWER role, allows a user in the organization account to see consumption with a currency as the unit of measure. Without this role, users see consumption in credits, not a currency.  Also required to add email addresses where notifications are sent for [organization-level cost anomalies](cost-anomalies.html#label-cost-anomaly-level). |
| APP\_ORGANIZATION\_BILLING\_VIEWER | Provides the same access as ORGANIZATION\_BILLING\_VIEWER but in an ORGADMIN-enabled account instead of the organization account. |

See moreShow less

Expand

The following sections provide more information about how you can use these application roles to provide access to cost anomalies.

### Grant the ability to view cost anomalies in a specific account

If you want users to be able to view account-level cost anomalies in a specific account, but not act as an administrator, grant them the
APP\_USAGE\_VIEWER application role.

For example, if you want user `joe` to be able to view cost anomalies for a specific account, sign in to the account, and then run the
following commands:

CopyExpand

```
USE ROLE ACCOUNTADMIN;

CREATE ROLE anomaly_viewer_role;
GRANT APPLICATION ROLE SNOWFLAKE.APP_USAGE_VIEWER TO ROLE anomaly_viewer_role;
GRANT ROLE anomaly_viewer_role TO USER joe;
```

Show lessSee more

Scroll to top

### Grant the ability to view cost anomalies for all accounts

To allow a user to view account-level cost anomalies for all accounts in the organization and to view organization-level anomalies, grant
the APP\_USAGE\_VIEWER role and one of the following roles:

* If the user signs in to the organization account to view cost anomalies, also grant the ORGANIZATION\_BILLING\_VIEWER application role.
* If the user signs in to an ORGADMIN-enabled account to view cost anomalies, also grant the APP\_ORGANIZATION\_BILLING\_VIEWER application
  role.

A user who is granted these roles can see consumption data with a currency as the unit of measure instead of credits.

For example, if the user `ralph` signs in to the organization account to view cost anomalies that are related to the entire organization,
run the following commands:

CopyExpand

```
USE ROLE ACCOUNTADMIN;

CREATE ROLE anomaly_viewer_role;
GRANT APPLICATION ROLE SNOWFLAKE.APP_USAGE_VIEWER TO ROLE anomaly_viewer_role;
GRANT APPLICATION ROLE SNOWFLAKE.ORGANIZATION_BILLING_VIEWER TO ROLE anomaly_viewer_role;
GRANT ROLE anomaly_viewer_role TO USER ralph;
```

Show lessSee more

Scroll to top

### Grant the ability to configure cost anomalies in a specific account

If you want users to be able to view *and* configure account-level cost anomalies within a specific account, grant them the APP\_USAGE\_ADMIN
application role. A user with this role doesn’t need the APP\_USAGE\_VIEWER role to view the cost anomalies. Configuring cost anomalies
includes adding the email addresses where notifications are sent when there is an anomaly in the account.

For example, if you want user `judy` to be able to view and configure account-level cost anomalies for a specific account, sign in to the
account, and then run the following commands:

CopyExpand

```
USE ROLE ACCOUNTADMIN;

CREATE ROLE anomaly_admin_role;
GRANT APPLICATION ROLE SNOWFLAKE.APP_USAGE_ADMIN TO ROLE anomaly_admin_role;
GRANT ROLE anomaly_admin_role TO USER judy;
```

Show lessSee more

Scroll to top

### Grant the ability to configure organization-level cost anomalies

To allow a user to configure organization-level cost anomalies, grant the APP\_USAGE\_ADMIN role and one of the following roles:

* If the user signs in to the organization account to configure and view cost anomalies, also grant the ORGANIZATION\_BILLING\_VIEWER
  application role.
* If the user signs in to an ORGADMIN-enabled account to configure and view cost anomalies, also grant the APP\_ORGANIZATION\_BILLING\_VIEWER
  application role.

An administrator with one of these role combinations can perform the following tasks:

* Set and view the email addresses where notifications are sent for organization-level anomalies.
* View account-level cost anomalies in all accounts in the organization.
* View organization-level cost anomalies.
* View consumption data that uses a currency as the unit of measure.

For example, if the user `steven` signs in to the organization account to work with cost anomalies related to the entire organization,
run the following commands:

CopyExpand

```
USE ROLE ACCOUNTADMIN;

CREATE ROLE anomaly_admin_role;
GRANT APPLICATION ROLE SNOWFLAKE.APP_USAGE_ADMIN TO ROLE anomaly_admin_role;
GRANT APPLICATION ROLE SNOWFLAKE.ORGANIZATION_BILLING_VIEWER TO ROLE anomaly_admin_role;
GRANT ROLE anomaly_admin_role TO USER steven;
```

Show lessSee more

Scroll to top
