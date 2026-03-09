---
title: "Workspace replication"
url: "https://docs.snowflake.com/en/user-guide/ui-snowsight/workspaces-replication"
---

# Workspace replication

[![Snowflake logo in black (no text)](../../_images/logo-snowflake-black.png)](../../_images/logo-snowflake-black.png) [Preview Feature](../../release-notes/preview-features) — Open

Available to all accounts.

Important

* Workspaces owned by users require Business Critical (BC) or higher to support replication.
* Failover and failback require Business Critical Edition or higher. To inquire about upgrading, contact [Snowflake Support](../contacting-support).

Replication helps ensure business continuity by making workspaces and other important objects
available across accounts, even during disasters, outages, or periods of unavailability. Administrators configure replication groups to copy
account objects and databases from a primary account to one or more secondary accounts on a defined schedule.

## How Workspace replication works

Shared workspaces are replicated when they are included in a database that is part of a replication or failover group. Private workspaces are
replicated when their owning users are replicated. In secondary (target) accounts, replicated content is read-only; Workspace files are executable
but cannot be edited. To create and run new queries, use the original Worksheets interface in the secondary account.

Database replication can also be configured as a failover group to support high availability. When a secondary failover group is promoted to
primary, all contained objects, including workspaces, become writable in the new primary account.

For more information, see [Introduction to replication and failover across multiple accounts](../account-replication-intro).

### LOCAL workspaces

LOCAL workspaces do not participate in workspace replication. Workspace files remain within the current deployment and are not copied to or synchronized with other deployments.

When workspace replication is enabled, any existing workspaces and files that already exist in a secondary deployment are automatically designated as LOCAL. This ensures that users retain access to their existing workspace data in the secondary deployment rather than losing it as replication is enabled.

## Set up Workspace replication

To replicate Workspaces, you must complete the following setup tasks in order:

### Step 1: Enable replication for the account

A user with the ORGADMIN role must enable replication for each source and target account in the organization:

CopyExpand

```
USE ROLE ORGADMIN;
SELECT SYSTEM$GLOBAL_ACCOUNT_SET_PARAMETER(
    '<organization_name>.<account_name>',
    'ENABLE_ACCOUNT_DATABASE_REPLICATION',
    'true');
```

Show lessSee more

Scroll to top

For more information, see [Prerequisite: Enable replication for accounts in the organization](../account-replication-config.html#label-enabling-accounts-for-replication).

### Step 2: Create a replication group

A replication group copies objects from a primary account to a secondary account on an optionally defined schedule.

To create a replication group, specify the account that contains the workspace in the replication group:

#### Primary account

CopyExpand

```
USE ROLE ACCOUNTADMIN;

CREATE REPLICATION GROUP my_replication_group
    OBJECT_TYPES = USERS
    ALLOWED_ACCOUNTS = org_name.secondary_account_name
    [ REPLICATION_SCHEDULE = '10 MINUTE' ]
```

Show lessSee more

Scroll to top

In this example:

* `ALLOWED_ACCOUNTS` - The secondary account to replicate to.
* `REPLICATION_SCHEDULE` - How frequently replication occurs (for example, ‘10 MINUTE’ or ‘1 HOUR’).

#### Secondary account

CopyExpand

```
USE ROLE ACCOUNTADMIN;
CREATE REPLICATION GROUP my_replication_group
  AS REPLICA OF org_name.primary_account_name.my_replication_group;
```

Show lessSee more

Scroll to top

### Set up failover for high availability

To enable [failover](../account-replication-intro.html#label-replication-and-failover-groups) (promotion of a secondary account to primary) during an outage, you must use
a failover group instead of a replication group:

#### Primary account

CopyExpand

```
USE ROLE ACCOUNTADMIN;
CREATE FAILOVER GROUP my_failover_group
  OBJECT_TYPES = USERS
  ALLOWED_ACCOUNTS = org_name.secondary_account_name
  [ REPLICATION_SCHEDULE = '10 MINUTE' ]
```

Show lessSee more

Scroll to top

#### Secondary account

CopyExpand

```
USE ROLE ACCOUNTADMIN;
CREATE FAILOVER GROUP my_failover_group
  AS REPLICA OF org_name.primary_account_name.my_failover_group;
```

Show lessSee more

Scroll to top

#### Secondary takes over as primary fails

If you [promote the failover group to primary](../account-replication-failover-failback.html#label-replication-promote-failover-group), the workspace becomes read-write.

#### Secondary account behavior

If you don’t have an available read-write workspace, you can also revert to using Worksheets in Snowsight which support read-write.

## Considerations

* Query results are not replicated - Query results are only stored in the account where the query was originally run.
* The selected role, warehouse, database, and schema context for any files are not replicated - You may replicate those account level objects
  separately, but those contexts will not remain selected on the files in the target account.

## Limitations

* Git integration is not currently supported after failover - If a secondary account with workspaces is promoted to primary, you must
  reconfigure the Git integration manually.
* Workspaces in the secondary account are read-only.

For more detailed information on replication behavior, see [Replication considerations](../account-replication-considerations.html#label-replication-and-tasks).
