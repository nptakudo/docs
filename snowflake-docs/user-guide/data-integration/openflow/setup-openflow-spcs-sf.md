---
title: "Set up Openflow - Snowflake Deployment: Core Snowflake"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/setup-openflow-spcs-sf"
---

# Set up Openflow - Snowflake Deployment: Core Snowflake

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../intro-regions.html#label-na-general-regions).

Openflow - Snowflake Deployment requires the creation of the following Snowflake specific resources:

> 1. [Create the OPENFLOW\_ADMIN role](#create-the-openflow-admin-role)
> 2. [Configure required privileges](#configure-required-privileges)

To complete these tasks, Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in) and open a SQL worksheet.

## Create the OPENFLOW\_ADMIN role

Create the required Openflow administration role.

Note

`<OPENFLOW_USER>` denotes the user that will be used to access Openflow.

CopyExpand

```
USE ROLE ACCOUNTADMIN;

CREATE ROLE IF NOT EXISTS OPENFLOW_ADMIN;

GRANT ROLE OPENFLOW_ADMIN TO USER <OPENFLOW_USER>;
```

Show lessSee more

Scroll to top

Caution

Users with a default role of ACCOUNTADMIN can’t login to Openflow - Snowflake Deployment runtimes and will get an error message when attempting to do so.
Snowflake recommends assigning a different default role to any user that will login to a runtime.
In addition, Snowflake recommends setting default secondary roles to `ALL` for all Openflow users.

To change the default role and enable all secondary roles, execute the following:

For example:

CopyExpand

```
USE ROLE ACCOUNTADMIN;

ALTER USER <openflow_user> SET DEFAULT_ROLE = <openflow_admin>;
ALTER USER <openflow_user> SET DEFAULT_SECONDARY_ROLES = ('ALL');
```

Show lessSee more

Scroll to top

## Configure required privileges

Openflow requires defining specific Snowflake Account level privileges.
These privileges are assigned to the ACCOUNTADMIN role as part of the default set of privileges.
ACCOUNTADMIN will automatically have the following privileges and will be able to grant them
to a role of their choosing for the Openflow admin role, shown as `OPENFLOW_ADMIN` role in the following example:

CopyExpand

```
USE ROLE ACCOUNTADMIN;

GRANT CREATE OPENFLOW DATA PLANE INTEGRATION ON ACCOUNT TO ROLE OPENFLOW_ADMIN;
GRANT CREATE OPENFLOW RUNTIME INTEGRATION ON ACCOUNT TO ROLE OPENFLOW_ADMIN;
GRANT CREATE COMPUTE POOL ON ACCOUNT TO ROLE OPENFLOW_ADMIN;
```

Show lessSee more

Scroll to top

## Next steps

Optionally, [Set up PrivateLink UI access](setup-openflow-spcs-configure-pr-ui) to access the Snowflake Openflow Runtime UI using private connectivity.

[Create deployment](setup-openflow-spcs-deployment)
