---
title: "Personal Databases"
url: "https://docs.snowflake.com/en/user-guide/personal-databases"
---

# Personal Databases

## What is a Personal Database?

A Personal Database (PDB) is a system-owned, user-managed database instance that is automatically provisioned by Snowflake. It serves as a
dedicated, personal storage location where users can create, organize, and manage their own database objects.

Automatic provisioning removes the administrative requirement for users to manually select or request access to a shared database, which
ensures a dedicated development environment. When a user is dropped from the system, their associated PDB and all its objects are
automatically transferred to ACCOUNTADMIN ownership.

### Advantages of a PDB

* **Organize personal projects:** Users can organize their own projects in an isolated environment, reducing clutter and potential naming
  conflicts in shared databases.
* **Easy administrator governance:** All file-related developments are fully governed by RBAC.

## PDB object types

Currently, PDBs support two primary object types that provide a dedicated development environment for the user: workspaces and notebooks.

### Workspaces

The PDB is created when a user first interacts with the [Workspaces UI](ui-snowsight/workspaces).
Workspaces are file-based entities and require storage within a Snowflake database.

### Notebooks

PDBs support managed compute services for [Snowflake notebooks](ui-snowsight/notebooks). To enable code execution, a
Snowflake-managed service object is automatically created within the PDB. This ensures that the notebook’s execution context is bound to all
the roles and permissions the user already possesses. This object connects the workspace to a Snowpark Container Services (SPCS) compute pool, allowing developers
to execute their Snowflake Notebooks code.

Important

The user must have the USAGE privilege on the associated compute pool before they can create a service to execute code. This privilege can be granted by any role with the
MANAGE GRANTS privilege.

## Security

The PDB’s architecture is intentionally streamlined and adheres to the principle of least privilege, which ensures that all operations are strictly
limited to the user’s existing security context:

* **No new data access:** PDBs do not introduce any new or expanded access to data or any additional ability to share data. **Users can’t
  move data from a regular database to a PDB**.
* **Permissions context:** Any SQL queries executed within a workspace are run with the exact same set of roles and permissions that the user
  already possesses. This mirrors the execution environment of a standard Snowflake workspace file.

Note

Personal databases also support personal secrets. [Secret objects](../sql-reference/sql/create-secret) are owned exclusively by the user. This ensures, by default, that the secret
remains private, is accessible only to the user, and is not shared unintentionally.

## PDB management and visibility

Administrators can monitor and control usage of PDBs, which are owned by the system, not by any role. Usage on a PDB is limited
to the user it is assigned to. Objects inside a PDB cannot be shared.

### Administrator visibility

Roles with the MANAGE GRANTS privilege have visibility into all objects within the account, including personal objects owned by individual
users. For example, roles like ACCOUNTADMIN can view all databases, including personal databases, by default. These roles can also access
details about schemas and their objects within personal databases.

* To view details for all personal databases within an account, query the [DATABASES Account Usage view](../sql-reference/account-usage/databases):

  > CopyExpand
  >
  > ```
  > SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.DATABASES
  > WHERE DATABASE_NAME LIKE 'USER$%';
  > ```
  >
  > Show lessSee more
  >
  > Scroll to top
* To view the workspaces that exist in a specific personal database, use the following code:

  > CopyExpand
  >
  > ```
  > SHOW WORKSPACES IN DATABASE "USER$CMEYER";
  > ```
  >
  > Show lessSee more
  >
  > Scroll to top
* To view a specific user’s personal database, use the following code:

  > CopyExpand
  >
  > ```
  > SHOW DATABASES LIKE 'USER$BOBR';
  > ```
  >
  > Show lessSee more
  >
  > Scroll to top
  >
  > For personal databases, the value in the `kind` column is `PERSONAL DATABASE`.
* To view objects in a specific personal database, use the following code:

  > CopyExpand
  >
  > ```
  > SHOW OBJECTS IN DATABASE "USER$<username>";
  > ```
  >
  > Show lessSee more
  >
  > Scroll to top

### Drop a workspace

* To drop a workspace in a personal database, use the following code:

  > CopyExpand
  >
  > ```
  > DROP WORKSPACE "USER$JSMITH_DROP_WS_TEST".PUBLIC."drop_this_ws";
  > ```
  >
  > Show lessSee more
  >
  > Scroll to top

## Cost considerations

* Users cannot store data in tables in their PDBs.
* Storage costs reflect only the size of the workspace files and associated metadata.

## Limitations

Administrators cannot perform the following tasks:

* View filenames or file contents that belong to other users.
* View how much storage is used for PDBs. PDBs do not appear in `DATABASE_STORAGE_USAGE_HISTORY`.
* Limit how much storage is used for each PDB.
* Drop PDBs, or prevent individual users from using them.
* Create new PDBs. New PDBs are created on demand when a user creates a workspace.
