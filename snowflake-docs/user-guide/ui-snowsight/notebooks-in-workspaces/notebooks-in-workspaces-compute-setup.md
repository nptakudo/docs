---
title: "Compute setup for Snowflake Notebooks in Workspaces"
url: "https://docs.snowflake.com/en/user-guide/ui-snowsight/notebooks-in-workspaces/notebooks-in-workspaces-compute-setup"
---

# Compute setup for Snowflake Notebooks in Workspaces

Feature — Generally Available

Available to all AWS and Azure commercial regions. PrivateLink is supported.

## Setting up compute

When a user runs a notebook, the user creates a Snowflake-managed notebook service to host the notebook kernel and execute code.

When creating a notebook service, users can configure the Python version, Snowflake Container Runtime version, compute pool, idle timeout, external
access integrations, and optionally customize the service name.

Each notebook service is scoped to a single user and occupies one node on the selected compute pool. All notebooks connected to the same service
share the compute resources on that node. If a notebook requires dedicated compute resources, create a separate notebook service and avoid attaching
additional notebooks to it.

![../../../_images/notebook-compute-diagram1.png](../../../_images/notebook-compute-diagram1.png)

## Managing a notebook service

### Suspend

You can manually suspend a notebook service by clicking Connected, hovering over the service name, and selecting Suspend (pause icon).

Alternatively, you can wait for the service to reach its idle timeout setting and it will suspend automatically. For details on how idle time is
calculated, see [Idle timeout](#label-nb-in-ws-idle-timeout).

Suspending a service disconnects all notebooks connected to it, clears in-memory states, and removes all packages and variables. Any files created from
code or the terminal in the Workspace file system and the `/tmp` directory are also removed.

Note

Writing files to the Workspace directory from code or the terminal is not supported. For information on persisting files, see
[Working with the file system](../notebooks-work-with-files.html#label-notebooks-work-with-files).

### Resume

To resume a suspended service, connect a notebook to it or run a notebook that has previously been connected to it.

### Drop

Administrators can drop a notebook service.

SQLSnowsight

To drop a notebook service via SQL:

CopyExpand

```
DROP USER$DB_NAME.PUBLIC.[SERVICE_NAME];
```

Show lessSee more

Scroll to top

To drop a notebook service using Snowsight:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. Select the Connected dropdown.
3. Select Manage service to go to the Services & jobs page.
4. Select the ellipsis for the service you want to drop, then select Drop.

## Editing a notebook service

A notebook service can be updated after being created to change:

1. External Access Integrations.
2. Runtime version.
3. Idle timeout.

Changes to (1) or (2) suspend then restart the service. Changing the idle timeout does not restart the service.

## Idle timeout

Each notebook service defines its own idle timeout. The service is suspended when the idle time is reached. Idle time begins as soon as all running
cells across all connected notebooks have finished. If multiple notebooks share the same service, idle time starts only when the last notebook
becomes idle (no cells running).

By default, notebook services have an idle timeout of 24 hours. You can configure the idle timeout when creating or updating a notebook service
to better align with your usage patterns and cost optimization strategies.

Note

To change idle timeout values (including the default value) for notebook services in your account, contact your Snowflake account team or Snowflake Support.

## Credit usage

Notebook execution can incur credits from two sources:

* **Compute pool:** Powers the notebook kernels and Python processes.

  Credits accrue while the notebook service is in the RUNNING state until it is manually suspended, or suspended due to idle timeout. All notebooks
  connected to the same service share the compute pool credits consumed.
* **Query warehouse:** Used for SQL queries or Snowpark pushdown compute triggered by the notebook.

  Credits accrue only when SQL queries or Snowpark pushdown compute operations run on the warehouse. To optimize costs, enable auto-suspend on the
  query warehouse. Notebooks that do not invoke any SQL queries or Snowpark pushdown compute incur no query warehouse credits.

For more information on cost optimization and maximizing value, see [Optimizing cost](../../cost-optimize).

## Governance on notebook services

Notebook services are personal to each user, used exclusively for running notebooks, and located within the user’s Personal Database (PDB).

### Privileges

#### Ownership

The OWNER\_ROLE is NULL because Snowflake manages these services.

#### User privileges

The creating user is granted the following privileges:

* USAGE
* OPERATE
* DROP
* MONITOR

#### Administrator privileges

ACCOUNTADMIN is granted the following privileges:

* USAGE
* OPERATE
* DROP

This allows full management and oversight of all notebook services.

## Administrator control and cost monitoring on compute pools

Administrators manage user access and costs primarily through the compute pools associated with notebook services.

A user’s role must have the USAGE privilege on a compute pool to create a notebook service and run notebooks. In addition, the compute pool must
allow the `NOTEBOOK` workload type through the `ALLOWED_SPCS_WORKLOAD_TYPES` parameter. The default value for this parameter is
`ALL`, which includes `NOTEBOOK`.

To learn more about compute pool workloads, see [Snowpark Container Services: Working with compute pools](../../../developer-guide/snowpark-container-services/working-with-compute-pool).

### Disable notebook execution

Administrators can restrict notebook execution in Workspaces in multiple ways:

#### Remove USAGE on the compute pool

Removing the USAGE privilege from a role on a compute pool prevents that role from using that compute pool, including running notebooks.

#### Restrict workload types on all compute pools

Administrators can restrict notebook execution while still permitting other workloads using two account-level parameters. This will affect
all roles in the account.

* Exclude `NOTEBOOK` from the `ALLOWED_SPCS_WORKLOAD_TYPES` parameter.
* Set `NOTEBOOK` as the `DISALLOWED_SPCS_WORKLOAD_TYPES` parameter.

Any role that has USAGE on the compute pool can still run other allowed types of workloads as specified by the parameters.

### Monitor costs

Administrators can monitor consumption per compute pool. Snowflake recommends provisioning a unique compute pool for each role to view role-level
consumption. To manage spend, administrators can apply budgets on specific compute pools.

### View notebook-managed services

Use the SHOW SERVICES command:

CopyExpand

```
SHOW SERVICES OF TYPE NOTEBOOK;
```

Show lessSee more

Scroll to top

## Service maintenance

Notebook services are a type of Snowpark Container Services and require periodic maintenance to remain secure and up to date. Maintenance typically
takes about five minutes and suspends and restarts the notebook service. See [Managing a notebook service](#label-nb-in-ws-manage-notebook-service)
for details on workload impact.

After a notebook service enters the `RUNNING` state (whether newly created or resumed after being in `SUSPENDED` state), it is guaranteed
not to be disrupted for seven calendar days (168 hours) due to service maintenance. After seven days of creation, the service may be suspended for mandatory
maintenance.
