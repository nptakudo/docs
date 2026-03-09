---
title: "Snowflake Data Clean Rooms Collaboration API"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/v2/v2-api-reference"
---

# Snowflake Data Clean Rooms Collaboration API

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) [Preview Feature](../../../release-notes/preview-features) — Open

Available to all accounts.

Required version

To use Collaboration Data Clean Rooms, you must upgrade to Snowflake Data Clean Rooms version 12.9 or later.

## Introduction

This is the reference page for the Snowflake Data Clean Rooms Collaboration API. This API uses the `COLLABORATION` and `REGISTRY`
namespaces, not the `PROVIDER` or `CONSUMER` namespaces used by the Provider and Consumer APIs.

Note

You should disable secondary roles in your environment when using the Collaboration API:

CopyExpand

```
USE SECONDARY ROLES NONE;
```

Show lessSee more

Scroll to top

### Manage access to the DCR Collaboration API

Collaborators can use the high-level SAMOOHA\_APP\_ROLE role to call all DCR Collaboration API procedures.

However, we recommend that administrators grant more granular access to specific collaborations or specific roles, based on their needs. You can find the more granular privileges documented in [Access management procedures](#label-dcr-collaboration-access-management-api).

To grant granular API privileges to a user, take the following steps:

1. Create a role.
2. Grant usage on the warehouse being used to the role.
3. Call [GRANT\_PRIVILEGE\_ON\_OBJECT\_TO\_ROLE](#label-grant-privilege-on-object-to-role) if needed to grant appropriate privileges on a specific collaboration to a role.
4. Call [GRANT\_PRIVILEGE\_ON\_ACCOUNT\_TO\_ROLE](#label-dcr-grant-privilege-on-account-to-role) if needed to grant appropriate high-level privileges on all collaborations in the account to the role.
5. Grant the role to the user, who can now call collaboration procedures to participate in the collaboration.

#### Example

Here is an example of creating two Collaboration API roles: one role that can create and join collaborations, and another role that can view resources in a collaboration and run analyses in a collaboration (but not join or modify a collaboration).

CopyExpand

```
-- Create a role that can create and join collaborations.
USE ROLE ACCOUNTADMIN;
CREATE ROLE COLLABORATION_CREATOR_ROLE;
GRANT USAGE ON WAREHOUSE APP_WH TO COLLABORATION_CREATOR_ROLE;
GRANT APPLY ROW ACCESS POLICY ON ACCOUNT TO ROLE COLLABORATION_CREATOR_ROLE;
GRANT CREATE APPLICATION ON ACCOUNT TO ROLE COLLABORATION_CREATOR_ROLE;
GRANT CREATE DATABASE ON ACCOUNT TO ROLE COLLABORATION_CREATOR_ROLE;
GRANT CREATE LISTING ON ACCOUNT TO ROLE COLLABORATION_CREATOR_ROLE;
GRANT CREATE SHARE ON ACCOUNT TO ROLE COLLABORATION_CREATOR_ROLE;
GRANT IMPORT SHARE ON ACCOUNT TO ROLE COLLABORATION_CREATOR_ROLE;
GRANT MANAGE SHARE TARGET ON ACCOUNT TO ROLE COLLABORATION_CREATOR_ROLE;
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.ADMIN.GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'COLLABORATION_CREATOR_ROLE');
GRANT ROLE COLLABORATION_CREATOR_ROLE TO USER george_washington;

-- Create a role that can run analyses on a specific collaboration.
USE ROLE COLLABORATION_CREATOR_ROLE;
CREATE ROLE ANALYST_ROLE;

CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.ADMIN.GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE(
  'RUN',
  'COLLABORATION',
  $collaboration_name,
  'ANALYST_ROLE'
);

-- Also grant READ, which gives permission to read the resources available to use in a collaboration.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.ADMIN.GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE(
  'READ',
  'COLLABORATION',
  $collaboration_name,
  'ANALYST_ROLE'
);

GRANT ROLE ANALYST_ROLE to USER alexander_hamilton;
```

Show lessSee more

Scroll to top

### Metadata cheat sheet

Here is how to find some commonly sought information about a collaboration:

| To learn this… | Call this |
| --- | --- |
| What collaborations can I join? | `VIEW_COLLABORATIONS`. Look for collaborations where the COLLABORATION\_NAME column is NULL. |
| Which collaborations have I joined? | `VIEW_COLLABORATIONS`. Look for collaborations where the COLLABORATION\_NAME column is not NULL, which can mean either that you have created or joined the collaboration. |
| Which collaborations do I own? | `VIEW_COLLABORATIONS`. Look in the OWNER\_ACCOUNT column. |
| What is the status of all collaborators in a collaboration? | `GET_STATUS` |
| What is my join or creation status in a collaboration? | `GET_STATUS` or `VIEW_COLLABORATIONS` |
| Who owns a given collaboration? | `GET_STATUS`. Look for OWNER in the ROLES column. |
| What is my role in a given collaboration? | `GET_STATUS`. Look in the ROLES column. |
| What roles are assigned in a given collaboration? | `GET_STATUS`. Look in the ROLES column. |
| What is the spec in a given collaboration? | `VIEW_COLLABORATIONS`. Look in the COLLABORATION\_SPEC column. |
| Is the spec up to date? | There is no way to tell if a given spec has changes in progress, but you can call `VIEW_COLLABORATIONS` to see when the latest updates were applied. |
| What pending update requests do I have? | `VIEW_UPDATE_REQUESTS`. Look for rows where STATUS = PENDING. |
| Show me the spec for a given collaboration | `REVIEW` returns the collaboration spec. If you have already called `REVIEW` or joined the collaboration, call the following SQL command with your collaboration name as indicated:  CopyExpand  ``` CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.VIEW_COLLABORATIONS() ->>   SELECT "COLLABORATION_SPEC" FROM $1     WHERE "SOURCE_NAME" = <collaboration name>; ```  Show lessSee more  Scroll to top |

See moreShow less

Expand

## Template procedures

### REGISTER\_TEMPLATE

Schema:
:   REGISTRY

Registers a template to enable it to be used in a collaboration. Every template registered must have a unique
name + version for all templates in all registries in your account.

#### Syntax

CopyExpand

```
REGISTER_TEMPLATE( ['<registry_name>' ,] <template_spec> )
```

Show lessSee more

Scroll to top

#### Arguments

`registry_name` *(Optional)*
:   Name of a [custom registry](registries) in which to register this template. If not specified, registers the template in the default account registry.

`template_spec`
:   [Template definition](spec-reference.html#label-dcr-collaboration-template-yaml) in YAML format, as a string.

#### Returns

A template ID to use in the collaboration specification.

#### Examples

Register a template in the default registry:

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.REGISTER_TEMPLATE(
  $$
  api_version: 2.0.0
  spec_type: template
  name: my_test_template
  version: 2026_01_12_V1
  type: sql_analysis
  description: A test template
  template:
    SELECT * FROM IDENTIFIER({{ source_table[0] }}) LIMIT 10;
$$);
```

Show lessSee more

Scroll to top

Register a template in a custom registry:

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.REGISTER_TEMPLATE(
  'my_custom_registry',
  $$
  api_version: 2.0.0
  spec_type: template
  name: my_test_template
  version: 2026_01_12_V1
  type: sql_analysis
  description: A test template
  template:
    SELECT * FROM IDENTIFIER({{ source_table[0] }}) LIMIT 10;
$$);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling the following procedures.

To register objects in the default registry:

* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('REGISTER TEMPLATE', 'role name')`

To register items in a custom registry:

* You have read and write privileges on any custom registry that you created yourself.
* To access a custom registry created by another user, you need `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('REGISTER', 'REGISTRY', 'MY_REGISTRY', 'role name')`.

---

### VIEW\_REGISTERED\_TEMPLATES

Schema:
:   REGISTRY

Lists all templates that you have registered. To register a template, call `REGISTRY.REGISTER_TEMPLATE`.

#### Syntax

CopyExpand

```
VIEW_REGISTERED_TEMPLATES( [ '<registry_name>' ] )
```

Show lessSee more

Scroll to top

#### Arguments

`registry_name` *(Optional)*
:   Name of a [custom registry](registries) to list templates from. If not specified, lists templates from the default account registry.

#### Returns

A table that lists the details of all templates that you have registered in this account.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.VIEW_REGISTERED_TEMPLATES();
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures.

To see items in the default registry:

* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('VIEW REGISTERED TEMPLATES', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

To see items in a custom registry:

* You have read and write privileges on any custom registry that you created yourself.
* To access a custom registry created by another user, you need `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('READ', 'REGISTRY', 'MY_REGISTRY', 'role name')`.

---

### ADD\_TEMPLATE\_REQUEST

Schema:
:   COLLABORATION

Sends a request to add a template to an existing collaboration. If the sender is affected by the request, the sender automatically approves the request; all other affected collaborators must approve the request for the change to be applied. All collaborators need to call this procedure to add a template to an existing collaboration, even the collaboration owner.

To add additional template sharers, you can call this procedure again with their aliases. Each call adds the users listed in `share_with`

To see the status of the request, call `VIEW_UPDATE_REQUESTS`.

[See the add template flow.](using.html#label-dcr-collaboration-add-template-post-creation)

#### Syntax

CopyExpand

```
ADD_TEMPLATE_REQUEST( <collaboration_name>, <template_id>, <share_with> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration to add the template to.

`template_id`
:   ID of the template to add to the collaboration. Register the template to get this value.

`share_with`
:   Array of *aliases* of analysis runners to share this template with. Collaborators listed here will be added in addition to any other collaborators associated with this template. All collaborators listed here must be analysis runners or the procedure will fail without sharing this template with anyone.

#### Returns

A string success message.

#### Example

CopyExpand

```
-- Ask to add the template only for Collaborator3 in this collaboration.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.ADD_TEMPLATE_REQUEST(
  $collaboration_name,
  $template_alias,
  ['Collaborator3']
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('UPDATE', 'COLLABORATION', 'collaboration name', 'role name')`
* Either of the following privileges:

  + `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`, plus all additional account-level privileges that must be manually granted to the role.
  + `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`, plus all additional account-level privileges that must be manually granted to the role.
* If the template is in a custom registry, you must also have `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE( 'READ', 'registry name', 'role name')`

---

### REMOVE\_TEMPLATE

Schema:
:   COLLABORATION

Asynchronous request to remove a template from a given collaboration for specified collaborators. Only the collaborator that registered the template can remove a template. No approval is needed from anyone else to remove a template that you have registered. When a template is removed for a collaborator, that collaborator cannot see or view the template.

#### Syntax

CopyExpand

```
REMOVE_TEMPLATE( <collaboration_name>, <template_id>, <remove_for> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration to remove the template from.

`template_id`
:   ID of the template to remove from the collaboration.

`remove_for`
:   Array of one or more *aliases* of analysis runners in this collaboration that should no longer be able to see or use this template.

#### Returns

A string success message. To see if a template has been removed for a collaborator, view the collaboration specification.

#### Example

CopyExpand

```
-- Prevent collaborator_1234 from using the specified template
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.REMOVE_TEMPLATE(
  $collaboration_name,
  $template_id,
  ['collaborator_1234']
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('UPDATE', 'COLLABORATION', 'collaboration name', 'role name')`
* Either of the following privileges:

  + `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`, plus all additional account-level privileges that must be manually granted to the role.
  + `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`, plus all additional account-level privileges that must be manually granted to the role.
* If the template is in a custom registry, or references a code spec in a custom registry, you must also have `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE( 'READ', 'registry name', 'role name')`

---

### VIEW\_TEMPLATES

Schema:
:   COLLABORATION

Shows all templates that you can run, or that you have submitted, to the specified collaboration.

#### Syntax

CopyExpand

```
VIEW_TEMPLATES( <collaboration_name> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration. You must review or join this collaboration before you can list its templates.

#### Returns

A table that lists information about templates that you can run in this collaboration, including templates that you have registered. The information includes the template ID (used to run the template), template body, parameters, who created it, and who can run it.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.VIEW_TEMPLATES(
  $collaboration_name
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('VIEW TEMPLATES', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('READ', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

---

### ENABLE\_TEMPLATE\_AUTO\_APPROVAL

Schema:
:   COLLABORATION

Causes all template update requests sent by other collaborators to be approved automatically. Requests will still appear in the request log.
This affects only requests sent after auto-approval was enabled.

#### Syntax

CopyExpand

```
ENABLE_TEMPLATE_AUTO_APPROVAL( <collaboration_name> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration.

#### Returns

A string success message.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.ENABLE_TEMPLATE_AUTO_APPROVAL(
  $collaboration_name
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('MANAGE TEMPLATE AUTO APPROVAL', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('UPDATE', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

---

### DISABLE\_TEMPLATE\_AUTO\_APPROVAL

Schema:
:   COLLABORATION

Disables automatic approval for template requests raised by other collaborators. All future requests must be approved manually by calling `APPROVE_UPDATE_REQUEST`.

#### Syntax

CopyExpand

```
DISABLE_TEMPLATE_AUTO_APPROVAL( <collaboration_name> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration.

#### Returns

A string success message.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.DISABLE_TEMPLATE_AUTO_APPROVAL(
  $collaboration_name
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('MANAGE TEMPLATE AUTO APPROVAL', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('UPDATE', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

## Data offering procedures

### REGISTER\_DATA\_OFFERING

Schema:
:   REGISTRY

Registers a data offering so that it can be added to a collaboration definition. You cannot unregister a registered data offering. You can’t overwrite an existing data offering, but you can register a new one with the same name and a new version. Creating a new version of a data offering doesn’t remove any earlier versions.

Every data offering must have a unique name + version for all data offerings in all registries in your account.

If you want to share this table with others in the collaboration, add the table to the collaboration specification before the collaboration is created.

You must have the REFERENCE\_USAGE privilege on any data that you share in a collaboration. If you do not, you will get a “missing reference usage grant” error when you try to join the collaboration or register the object. [Learn how to handle this issue.](troubleshooting.html#label-dcr-database-missing-reference-usage-error)

#### Syntax

CopyExpand

```
REGISTER_DATA_OFFERING( ['<registry_name>' ,] <data_offering_spec> )
```

Show lessSee more

Scroll to top

#### Arguments

`registry_name` *(Optional)*
:   Name of a [custom registry](registries) in which to register this data offering. If not specified, registers the data offering in the default account registry.

`data_offering_spec`
:   A [data offering definition](spec-reference.html#label-dcr-collaboration-data-yaml) in YAML format that describes this data offering.

#### Returns

The data offering ID to use in a collaboration’s `data_offerings.id` field.

#### Examples

Register a data offering in the default registry:

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.REGISTER_DATA_OFFERING(
    $$
    api_version: 2.0.0
    spec_type: data_offering
    version: v1
    name: customers
    datasets:
     - alias: customers_1
       data_object_fqn: SAMOOHA_SAMPLE_DATABASE.DEMO.CUSTOMERS
       allowed_analyses: template_only
       schema_and_template_policies:
         hashed_email:
           category: join_custom
         status:
           category: passthrough
    $$
  );
```

Show lessSee more

Scroll to top

Register a data offering in a custom registry:

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.REGISTER_DATA_OFFERING(
    'my_custom_registry',
    $$
    api_version: 2.0.0
    spec_type: data_offering
    version: v1
    name: customers
    datasets:
     - alias: customers_1
       data_object_fqn: SAMOOHA_SAMPLE_DATABASE.DEMO.CUSTOMERS
       allowed_analyses: template_only
       schema_and_template_policies:
         hashed_email:
           category: join_custom
         status:
           category: passthrough
    $$
  );
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling the following procedures.

To register a data offering in the default registry:

* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('REGISTER DATA OFFERING', 'role name')`

To register items in a custom registry:

* You have read and write privileges on any custom registry that you created yourself.
* To access a custom registry created by another user, you need `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('REGISTER', 'REGISTRY', 'MY_REGISTRY', 'role name')`.

---

### LINK\_DATA\_OFFERING

Schema:
:   COLLABORATION

A data provider runs this procedure to update an existing collaboration by making the specified data offering available to the specified analysis runners. This is an asynchronous procedure; analysis runners should call `VIEW_DATA_OFFERINGS` to see when the data offering is available to be used.

This procedure is *additive*, meaning that the collaborators you specify are added to the existing list of data offering sharers.

If you want to use this table but not make it visible to other collaborators, call `LINK_LOCAL_DATA_OFFERING` instead of `LINK_DATA_OFFERING`.

Important

LINK\_DATA\_OFFERING can currently only be called by the role that created or joined the collaboration.

You cannot have an active secondary role when you run this procedure. Run the following SQL code to disable any secondary roles:

CopyExpand

```
USE SECONDARY ROLES NONE;
```

Show lessSee more

Scroll to top

This method is atomic: all of the following conditions must be met for this procedure to succeed. If the link attempt fails for any one collaborator, it fails for all of them.

* All of the specified collaborators must be analysis runners.
* This data offering must not already be shared with any of the specified analysis runners.
* This procedure can be run only by a user with the data provider role who has joined the collaboration.

You must have the REFERENCE\_USAGE privilege on any data that you wish to share. If you do not, you will get a “missing reference usage grant” error when you try to join the collaboration. [Learn how to handle this issue.](troubleshooting.html#label-dcr-database-missing-reference-usage-error)

#### Syntax

CopyExpand

```
LINK_DATA_OFFERING( <collaboration_name>, <data_offering_id>, <share_with> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration.

`data_offering_id`
:   ID of the dataset to share, generated when it was registered. The data offering must be visible to you when you call `VIEW_DATA_OFFERINGS` or `VIEW_REGISTERED_DATA_OFFERINGS` to be able to link it.

`share_with`
:   Array of string aliases of analysis runners to share this dataset with. Collaborators listed here will be added in addition to any other collaborators associated with this data offering. All collaborators listed here must be analysis runners that you are a data provider for, or the procedure will fail without sharing data with anyone.

#### Returns

A string success message.

#### Example

This example allows collaborator `alice` to use the specified data offering in the specified collaboration.

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.LINK_DATA_OFFERING(
  $collaboration_name,
  $my_data_id,
  ['alice']
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('UPDATE', 'COLLABORATION', 'collaboration name', 'role name')`
* Either of the following privileges:

  + `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`, plus all additional account-level privileges that must be manually granted to the role.
  + `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`, plus all additional account-level privileges that must be manually granted to the role.
* If the data offering is in a custom registry, you must also have `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE( 'READ', 'registry name', 'role name')`

---

### UNLINK\_DATA\_OFFERING

Schema:
:   COLLABORATION

A data provider runs this procedure to remove access to a data offering from specified analysis runners in an existing collaboration. This is an asynchronous procedure; analysis runners should call `VIEW_COLLABORATIONS` to confirm the data offering has been removed.

#### Syntax

CopyExpand

```
UNLINK_DATA_OFFERING( <collaboration_name>, <data_offering_id>, <remove_for> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration.

`data_offering_id`
:   ID of the dataset to unlink, generated when it was registered.

`remove_for`
:   Array of string aliases of one or more analysis runners to remove access for. All collaborators listed here must be analysis runners that currently have access to this data offering.

#### Returns

A string success message.

#### Example

CopyExpand

```
-- Remove data offering access for specific analysis runners in this collaboration.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.UNLINK_DATA_OFFERING(
  $collaboration_name,
  $data_offering_id,
  ['AnalysisRunner_1', 'AnalysisRunner_2']
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('UPDATE', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

---

### LINK\_LOCAL\_DATA\_OFFERING

Schema:
:   COLLABORATION

Use this procedure to link your own data into a collaboration if you are using Snowflake Standard edition. You must first register your data offerings by calling `REGISTER_DATA_OFFERING`. These offerings will not be visible to any other collaborator, and template policies will not be enforced. Tables submitted here propagate the `my_table` array in the template.

For more information, see [Run an analysis with your own data when you use Standard Edition](using.html#label-dcr-using-local-data).

#### Syntax

CopyExpand

```
LINK_LOCAL_DATA_OFFERING( <collaboration_name>, <data_offering_id> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration.

`data_offering_id`
:   ID of the dataset, generated when you registered it. Also visible in `VIEW_REGISTERED_DATA_OFFERINGS` and `VIEW_DATA_OFFERINGS` (to you only).

#### Returns

A string success message.

#### Example

This example links a registered data offering for use only by the current account, without exposing it to the rest of the collaborators.

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.LINK_LOCAL_DATA_OFFERING(
  $collaboration_name,
  $my_private_data_offering_id
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('UPDATE', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

---

### UNLINK\_LOCAL\_DATA\_OFFERING

Schema:
:   COLLABORATION

Use this procedure to unlink your own local data from a collaboration. After unlinking, the data offering will no longer be available to use in analyses within this collaboration. For more information about local data offerings, see [Run an analysis with your own data when you use Standard Edition](using.html#label-dcr-using-local-data).

#### Syntax

CopyExpand

```
UNLINK_LOCAL_DATA_OFFERING( <collaboration_name>, <data_offering_id> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration.

`data_offering_id`
:   ID of the dataset to unlink, generated when you registered it. Also visible in `VIEW_REGISTERED_DATA_OFFERINGS` and `VIEW_DATA_OFFERINGS` (to you only).

#### Returns

A string success message.

#### Example

CopyExpand

```
-- Unlink a local data offering from a collaboration.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.UNLINK_LOCAL_DATA_OFFERING(
  $collaboration_name,
  $my_private_data_offering_id
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('UPDATE', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

---

### VIEW\_REGISTERED\_DATA\_OFFERINGS

Schema:
:   REGISTRY

Lists all data offerings that you have registered. To view data offerings in a collaboration added by others, call `COLLABORATION.VIEW_DATA_OFFERINGS`.

#### Syntax

CopyExpand

```
VIEW_REGISTERED_DATA_OFFERINGS( [ '<registry_name>' ] )
```

Show lessSee more

Scroll to top

#### Arguments

`registry_name` *(Optional)*
:   Name of a [custom registry](registries) to list data offerings from. If not specified, lists data offerings from the default account registry.

#### Returns

A table that lists all data offerings that you have registered using this account.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.VIEW_REGISTERED_DATA_OFFERINGS();
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures.

To see items in the default registry:

* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('VIEW REGISTERED DATA OFFERINGS', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

To see items in a custom registry:

* You have read and write privileges on any custom registry that you created yourself.
* To access a custom registry created by another user, you need `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('READ', 'REGISTRY', 'MY_REGISTRY', 'role name')`.

---

### VIEW\_DATA\_OFFERINGS

Schema:
:   COLLABORATION

Lists all data offerings present in a specified collaboration that you can access as an analysis runner, or that you have added yourself. To see only data offerings that you registered, call `REGISTRY.VIEW_REGISTERED_DATA_OFFERINGS`.

You can see data offerings from collaborator X only after X has joined the collaboration.

#### Syntax

CopyExpand

```
VIEW_DATA_OFFERINGS( <collaboration_name> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration to explore.

#### Returns

Information about all data offerings in the specified collaboration. The table includes the
following columns:

* FREEFORM\_SQL\_COLUMN\_POLICIES: A JSON representation of all [free-form column policies](spec-reference.html#label-dcr-freeform-sql-policies-field) in
  this collaboration, keyed by policy type.
* FREEFORM\_SQL\_VIEW\_NAME: The fully qualified table name used in free-form SQL queries, when the dataset supports free-form SQL queries.
  This cell is empty if the dataset doesn’t offer free-form SQL queries.
* SHARE\_WITH: Who can use the data in an analysis. If this value is `LOCAL`, this is a local dataset that isn’t shared with any
  collaborators except for the party that hosts the data.
* TEMPLATE\_VIEW\_NAME: The fully qualified table name used in a template when calling `COLLABORATION.RUN`.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.VIEW_DATA_OFFERINGS(
  $collaboration_name
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('VIEW DATA OFFERINGS', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('READ', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

## Update request procedures

These procedures are used to manage collaboration update requests that require approval, such as
the [add template flow.](using.html#label-dcr-collaboration-add-template-post-creation)

### VIEW\_UPDATE\_REQUESTS

Schema:
:   COLLABORATION

See all update requests that you have created or that you can approve or deny, in the specified collaboration.
It can take a few seconds for an update request to be registered, so you might not see a request that you just sent a moment ago.

[See the add template flow.](using.html#label-dcr-collaboration-add-template-post-creation)

#### Syntax

CopyExpand

```
VIEW_UPDATE_REQUESTS( <collaboration_name> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration.

#### Returns

A table of update requests sent in this collaboration. Information includes

* `ID`: ID of the request. Use this to approve or deny a request.
* `TYPE`: Type of request. The following values are supported:

  + ‘Add Template’
* `STATUS`: Current status of the request. The following statuses can be reported:

  + REJECTED: Someone in the collaboration rejected this request.
  + PENDING: The request is awaiting your approval or rejection.
  + AWAITING\_PARTNER\_APPROVAL: You have approved the request, but the request still needs to be approved by one or more other collaborators.
  + APPROVED: All required approvers have approved the request, and the change has been applied to the collaboration.
* `APPROVAL_LOG`: Log of all approvals and rejections of the request. If the request is rejected, the reason given by the rejecting party is also provided here.
* `DETAILS`: Details specific to the request type, such as the template name, description, and whom it is shared with for an ‘Add Template’ request.
* `SPEC`: The details of the resource being updated, such as template specification for an ‘Add Template’ request.
* `UPDATED_ON`: The timestamp when the last action was taken on this request (for example, an approval or rejection).

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.VIEW_UPDATE_REQUESTS(
  $collaboration_name
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('UPDATE', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('READ', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

---

### APPROVE\_UPDATE\_REQUEST

Schema:
:   COLLABORATION

Approves a collaboration update request. See your list of pending requests by calling `VIEW_UPDATE_REQUESTS`. Once you approve a request, you cannot reject it later.

All affected collaborators must approve a request before the change is actually applied to the collaboration.

[See the add template flow.](using.html#label-dcr-collaboration-add-template-post-creation)

#### Syntax

CopyExpand

```
APPROVE_UPDATE_REQUEST( <collaboration_name>, <request_id> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration.

`request_id`
:   ID of the request.

#### Returns

A string success message.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.APPROVE_UPDATE_REQUEST(
  $collaboration_name,
  $request_id
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('MANAGE UPDATE REQUEST', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('UPDATE', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

---

### REJECT\_UPDATE\_REQUEST

Schema:
:   COLLABORATION

Rejects a collaboration update request. A single rejection prevents the change from being applied to the collaboration. You cannot approve a request after rejecting it.

#### Syntax

CopyExpand

```
REJECT_UPDATE_REQUEST( <collaboration_name>, <request_id>, <reason> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration.

`request_id`
:   ID of the request.

`reason`
:   A human-readable description of why the request was rejected. The argument is required, but you can submit an empty string.

#### Returns

A string success message.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.REJECT_UPDATE_REQUEST(
  $collaboration_name,
  'request_1324f934457',
  'Needs more cowbell'
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('MANAGE UPDATE REQUEST', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('UPDATE', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

## Collaboration management procedures

### INITIALIZE

Schema:
:   COLLABORATION

The owner calls this to create a collaboration and, optionally, join the collaboration.

Submitting a collaboration definition with the same `name` value as an existing collaboration throws an error.

It takes some time to create and join a collaboration, so you must call GET\_STATUS to learn when the collaboration has been joined.

After the owner has joined, all listed collaborators can see the collaboration when they call VIEW\_COLLABORATIONS, and can review and join it.

#### Syntax

CopyExpand

```
INITIALIZE( <collaboration_spec> [, '<auto_join_warehouse>'] )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_spec`
:   [Collaboration definition](spec-reference.html#label-dcr-collaboration-invitation-yaml) in YAML format, as a string.

`auto_join_warehouse` *(Optional)*
:   String that specifies a warehouse name as a valid Snowflake identifier. If specified, the collaboration will be created and joined using
    this warehouse. If not specified, the current warehouse will be used to create the collaboration, and you must call JOIN to join the
    collaboration. An XS warehouse is recommended.

#### Returns

A table with the following columns:

* COLLABORATION\_NAME: The name of the collaboration. Use this in any procedures that require you to specify a collaboration.
* MESSAGE: Information about the initialize request.

#### Examples

The following example creates a collaboration where Alice is the owner and can run an analysis using data provided
by Bob.

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.INITIALIZE(
  $$
  api_version: 2.0.0
  spec_type: collaboration
  name: basic_collaboration
  owner: alice
  collaborator_identifier_aliases:
    alice: corp_id.account_id
    bob: corp2_id.account2_id
  analysis_runners:
    alice:
      data_providers:
        bob:
          data_offerings:
          - id: bob_data_v1
      templates:
      - id: alice_test_template_2026_01_12_V1
  $$,
  'APP_WH'
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling the following procedure:

* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`

  If providing the `auto-join-warehouse` parameter and using a role other than SAMOOHA\_APP\_ROLE, the role must also be granted the
  EXECUTE TASK account-level privilege.

See [GRANT\_PRIVILEGE\_ON\_ACCOUNT\_TO\_ROLE](#label-dcr-grant-privilege-on-account-to-role) for additional required role permissions.

---

### TEARDOWN

Schema:
:   COLLABORATION

Called by the owner to delete a collaboration for all parties.

**You must call this procedure twice.** Call it once, then call `GET_STATUS` until it returns `LOCAL_DROP_PENDING`, then call this procedure again.

Note

This procedure can be called only on a collaboration that you have created and joined. If you have created but not yet joined the
collaboration, you must join it before you can tear it down.

#### Syntax

CopyExpand

```
TEARDOWN( <collaboration_name> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration to delete.

#### Returns

A string success message.

#### Example

CopyExpand

```
-- Start the process.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.TEARDOWN($collaboration_name);

-- Call until it returns LOCAL_DROP_PENDING.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.GET_STATUS($collaboration_name);

-- Final call.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.TEARDOWN($collaboration_name);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

See [GRANT\_PRIVILEGE\_ON\_ACCOUNT\_TO\_ROLE](#label-dcr-grant-privilege-on-account-to-role) for additional required role permissions.

---

### GET\_STATUS

Schema:
:   COLLABORATION

Shows information about all collaborators in a given collaboration.

When running an asynchronous operation such as creating or joining a collaboration, you must check the status to know when the last operation was complete before you can perform additional actions on that collaboration, such as running analyses. This procedure can be called by any collaborator invited to a collaboration.

Collaboration owners can see the following status pathway:

* CREATING » CREATED » INSTALLING » IN\_REVIEW (or INSTALLATION\_FAILED) » JOINING » JOINED (or JOIN\_FAILED)

Non-owners will see the following status pathway:

* INSTALLING » IN\_REVIEW (or INSTALLATION\_FAILED) » JOINING » JOINED (or JOIN\_FAILED)

#### Syntax

CopyExpand

```
GET_STATUS( <collaboration_name> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration to see the status of. You can see a list of your collaborations by calling
    `COLLABORATION.VIEW_COLLABORATIONS`. You must be invited to, or have joined, a collaboration before you can call `GET_STATUS` on it.

#### Returns

A table that shows the details about the latest join attempt for all collaborators in the specified collaboration. The table includes the following columns:

* UPDATED\_ON: Timestamp when the status was reported by the system.
* COLLABORATOR\_ACCOUNT: Data sharing account ID of this collaborator.
* COLLABORATOR\_NAME: The collaborator’s alias, as declared in the collaboration specification.
* COLLABORATOR\_ROLES: The actual and potential roles for this collaborator. Values include `owner`, `data_provider`, `analysis_runner`.
* STATUS: Status at the updated time. The following values are supported, and show the status of the named collaborator in the specified collaboration.

  + CREATING: Collaboration creation has started.
  + CREATE\_FAILED: Collaboration creation failed.
  + CREATED: Collaboration has been created and is ready to operate on.
  + INSTALLING: Installing the application package and preparing the collaboration details for review.
  + IN\_REVIEW: The collaboration is in review.
  + INSTALLATION\_FAILED: Installation failed; application package not installed, and can’t be reviewed.
  + INVITED: Participant has been invited.
  + REVIEWING: Currently reviewing the collaboration.
  + JOINING: Join process has started.
  + JOIN\_FAILED: Join process failed.
  + JOINED: Successfully joined the collaboration. You can start to use the collaboration.
  + INVITE\_STARTED: Invitation process started.
  + INVITE\_ACCEPTED: Invitation has been accepted.
  + LEAVING: Leave process has started.
  + LEAVE\_FAILED: Leave process failed.
  + LEFT: Successfully left the collaboration.
  + LOCAL\_DROP\_PENDING: You have made a successful request to drop or leave the collaboration. Complete the process by calling `TEARDOWN` or `LEAVE` again.
  + DROPPING: Drop process has started.
  + DROPPED: Successfully dropped.
  + DROP\_FAILED: Drop process failed.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.GET_STATUS(
  $collaboration_name
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('READ', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

---

### ENABLE\_EXTERNAL\_TABLE\_ANALYSIS\_FOR\_COLLABORATION

Schema:
:   ADMIN

Enables external and Apache Iceberg™ tables to be used to run an analysis in your account. An analysis runner must call this before running any analysis that includes external or Iceberg tables. This procedure is called once per collaboration, not once per analysis.

#### Syntax

CopyExpand

```
ENABLE_EXTERNAL_TABLE_ANALYSIS_FOR_COLLABORATION( <collaboration_name> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration.

#### Returns

A string success message.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.ADMIN.ENABLE_EXTERNAL_TABLE_ANALYSIS_FOR_COLLABORATION(
  $collaboration_name
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted the MANAGE FIREWALL CONFIGURATION privilege to call this
procedure.

---

### VIEW\_COLLABORATIONS

Schema:
:   COLLABORATION

View information about collaborations that you have created, can review, or have joined.

#### Syntax

CopyExpand

```
VIEW_COLLABORATIONS()
```

Show lessSee more

Scroll to top

#### Arguments

*None*

#### Returns

A table that lists details of all collaborations that you can access. The table includes the following columns:

* SOURCE\_NAME: The name of the collaboration, as specified by the `name` value in the collaboration specification.
* COLLABORATION\_NAME: The name of the installed collaboration. This is NULL until the collaboration is installed by calling `JOIN` (owners) or `REVIEW` (non-owners).
* OWNER\_ACCOUNT: Data sharing ID of the account that created the collaboration.
* UPDATED\_ON: When the collaboration was last updated.
* COLLABORATION\_SPEC: The specification for this collaboration in YAML format. This shows the latest version of the collaboration, including any resources added or removed after the collaboration was created. However, there might be update requests that are in progress that will be added soon, such as new or removed templates or data offerings.

#### Examples

View all collaborations:

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.VIEW_COLLABORATIONS();
```

Show lessSee more

Scroll to top

View the specification for a given collaboration by name:

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.VIEW_COLLABORATIONS() ->>
SELECT "COLLABORATION_SPEC" FROM $1 WHERE "SOURCE_NAME" = $collaboration_name;
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('READ', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('RUN', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('VIEW COLLABORATIONS', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

---

### REVIEW

Schema:
:   COLLABORATION

Provides details about a collaboration to which you have been invited. Call `COLLABORATION.VIEW_COLLABORATIONS` to see which
collaborations you have been invited to and not yet joined. All collaborators except the owner must call this
procedure before calling `JOIN`. You cannot call this procedure on a collaboration that you have joined. You must use the same role to call `REVIEW` and `JOIN`. If your account is on a different cloud hosting region than the owner, you might need to call this procedure several times until it returns a successful response.

This procedure installs the underlying application in your account.

**Important notes:**

* Owners cannot call `REVIEW` on their own collaborations.
* Everyone except the owner must call `REVIEW` before calling `JOIN`.
* After you have joined a collaboration, you cannot call `REVIEW` again.

#### Syntax

CopyExpand

```
REVIEW( <source_name>, <owner_account> )
```

Show lessSee more

Scroll to top

#### Arguments

`source_name`
:   Name of the collaboration you have been invited to join. You can see a list of your collaborations by calling
    `COLLABORATION.VIEW_COLLABORATIONS`.

`owner_account`
:   [Data Sharing Account Identifier](../../admin-account-identifier.html#label-account-name-data-sharing) of the owner. This can be found in the response to `COLLABORATION.VIEW_COLLABORATIONS`.

#### Returns

Table of information about the collaboration, including the collaboration ID, owner, and the collaboration specification.

If your account is on a [different cloud hosting region](using.html#label-dcr-collab-enabling-laf) than the collaboration owner’s, `REVIEW` might return a message saying that additional setup steps are still being performed. If you get this message, continue calling `REVIEW` until it returns the information table about the collaboration.

#### Example

CopyExpand

```
-- View the collaboration for your own usage.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.REVIEW(
  $collaboration_name,
  'org1.account1234'
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

See [GRANT\_PRIVILEGE\_ON\_ACCOUNT\_TO\_ROLE](#label-dcr-grant-privilege-on-account-to-role) for additional required role permissions.

---

### JOIN

Schema:
:   COLLABORATION

Asynchronous method to join a specified collaboration. Note that you can access only the resources listed in the collaboration at the time that you join. This procedure takes some time to run.

You need the REGISTER DATA OFFERING account privilege to join any collaboration in which you can activate data (that is, you are an analysis runner and the collaboration specification includes an `activation_destinations` field). See the [access management API reference guide](#label-dcr-collaboration-access-management-api).

You cannot have an active secondary role when you run this procedure. Run the following SQL code to disable any secondary roles:

CopyExpand

```
USE SECONDARY ROLES NONE;
```

Show lessSee more

Scroll to top

Everyone except the collaboration creator must call `COLLABORATION.REVIEW` before calling this procedure.

This procedure is asynchronous; call `GET_STATUS` to determine when you have successfully joined the collaboration.

Anyone who submits a resource to the collaboration or wants to run a template in the collaboration must join the collaboration first. The
collaboration creator is automatically joined when calling `INITIALIZE` (unless `auto_join_warehouse` is provided).

#### Syntax

CopyExpand

```
JOIN( <collaboration_name> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration to join. You can see a list of your collaborations by calling
    `COLLABORATION.VIEW_COLLABORATIONS`. If you have been invited to join multiple collaborations with the same name, this defaults to
    the last one that you called `COLLABORATION.REVIEW` on.

#### Returns

A string success message. If you get an error about a missing reference usage grant, see the [Troubleshooting guide](troubleshooting.html#label-dcr-database-missing-reference-usage-error).

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.JOIN(
  $collaboration_name
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

See [GRANT\_PRIVILEGE\_ON\_ACCOUNT\_TO\_ROLE](#label-dcr-grant-privilege-on-account-to-role) for additional required role permissions.

---

### LEAVE

Schema:
:   COLLABORATION

Leaves a collaboration that you have joined. You cannot rejoin a collaboration after you have left it.

**You must call this procedure twice.** Call it once, then call `GET_STATUS` until it returns `LOCAL_DROP_PENDING`, then call this procedure again.

#### Syntax

CopyExpand

```
LEAVE( <collaboration_name> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration to leave.

#### Returns

A string success message.

#### Example

CopyExpand

```
-- Start the process.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.LEAVE($collaboration_name);

-- Call until it returns LOCAL_DROP_PENDING.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.GET_STATUS($collaboration_name);

-- Final call.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.LEAVE($collaboration_name);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

See [GRANT\_PRIVILEGE\_ON\_ACCOUNT\_TO\_ROLE](#label-dcr-grant-privilege-on-account-to-role) for additional required role permissions.

## Running analyses and activations

### RUN

Schema:
:   COLLABORATION

Runs an analysis in the data clean room. You can pass in run details either as individual parameters, or by passing in an [analysis YAML specification string](spec-reference.html#label-dcr-collaboration-analysis-yaml).

Read the [consumer.run\_analysis](../consumer.html#dcr-consumer-run-analysis) reference for background about running a template in a data clean room.

There are two versions of this procedure: one that takes the run arguments as a single YAML-formatted string, and one that takes the arguments as individual parameters.

#### YAML argument syntax

CopyExpand

```
RUN( <collaboration_name>, <analysis_spec> )
```

Show lessSee more

Scroll to top

##### Arguments

`collaboration_name`
:   Name of the collaboration in which to run this analysis.

`analysis_spec`
:   [Analysis definition](spec-reference.html#label-dcr-collaboration-analysis-yaml) in YAML format as a string, describing the template, tables, and template values to use in this analysis.

#### Explicit parameters syntax

CopyExpand

```
RUN( <collaboration_name>, <template_id>, <template_view_names>, <local_template_view_names>, <arguments> )
```

Show lessSee more

Scroll to top

##### Arguments

`collaboration_name`
:   Name of the collaboration in which to run this analysis.

`template_id`
:   ID of the template to run.

`template_view_names`
:   Array of string names of source tables to use in the analysis. Use table names returned by `VIEW_DATA_OFFERINGS` in the TEMPLATE\_VIEW\_NAME column. The format for each entry is `user_alias.data_offering_id.dataset_alias`

`local_template_view_names`
:   Array of string IDs of your own tables to use in the analysis. You must link these tables first by calling `LINK_LOCAL_DATA_OFFERING`.

`arguments`
:   JSON object that contains named arguments used by the template, where each key is a template argument name, and the value is the value of that argument.

#### Returns

Analysis results in table format.

#### Examples

Pass by parameter example:

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.RUN(
  $collaboration_name,
  $template_name,
  ['Provider.data_offering_1_2026_01_12_v0.test_dataset'], -- Tables to pass to source_tables variable.
  [],
  {} -- Template takes no parameters.
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('RUN', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

---

### VIEW\_ACTIVATIONS

Schema:
:   COLLABORATION

Shows the activation status of any analysis run that either you triggered to send to a collaborator, or activations that a collaborator triggered to send to you. Activation requests to send data to yourself are not listed.

For more information about activation, see [Activate query results](using.html#label-dcr-collaboration-activating-results).

#### Syntax

CopyExpand

```
VIEW_ACTIVATIONS( <collaboration_name> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration.

#### Returns

A table containing details for each activation. The table includes the following columns:

* `UPDATED_ON`: Time when the status was last updated.
* `SEGMENT_NAME`: An arbitrary string assigned by the analysis runner to identify this activation. For more information, see [Activating query results](../activation).
* `ACTIVATION_ID`: ID of this activation request. For more information, see [Viewing provider and consumer activation results](../activation.html#label-cleanrooms-provider-consumer-view-results).
* `TEMPLATE_ID`: Template used to produce this activation data.
* `SHARED_BY`: The collaborator that ran the analysis.
* `SHARED_WITH`: The collaborator that should receive the analysis data.
* `STATUS`: Status of the activation. The following values are supported:

  > + `PENDING`: Activation was requested, but is waiting to be processed.
  > + `SHARED`: Activation data is ready to be processed. Call `PROCESS_ACTIVATION` to send the results to your account.
  > + `FAILED`: Activation processing failed. See information in the `DETAILS` column.
  > + `PROCESSED`: Activation results have been sent to the account specified in the activation request.
* `DETAILS`: Failure details, if the activation failed.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.VIEW_ACTIVATIONS(
  $collaboration_name
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('VIEW ACTIVATIONS', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('RUN', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

---

### PROCESS\_ACTIVATION

Schema:
:   COLLABORATION

If the analysis runner is sending data to another collaborator’s account, that collaborator should call `PROCESS_ACTIVATION` to import the activation data into their account. The collaborator should call
`VIEW_ACTIVATIONS` and wait until the output shows that the activation status for a given segment is `SHARED` before calling `PROCESS_ACTIVATION`.

For more information, see [Activate query results](using.html#label-dcr-collaboration-activating-results).

#### Syntax

CopyExpand

```
PROCESS_ACTIVATION( <collaboration_name> [, <array_of_batch_ids> ] )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   Name of the collaboration.

`batch_ids` *(Optional)*
:   String array of batch IDs of activations to process. This value is returned by VIEW\_ACTIVATIONS. If not included, the request will process all pending
    activations in the designated collaboration for the caller.

#### Returns

The table name where the user can retrieve the results, and the segment name specified for the results. See [Activate query results](using.html#label-dcr-collaboration-activating-results) to learn how to read results.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.PROCESS_ACTIVATION(
  $collaboration_name
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('PROCESS ACTIVATION', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`

## Registry management procedures

### CREATE\_REGISTRY

Schema:
:   REGISTRY

Creates a custom registry to organize resources such as templates and data offerings. A custom registry can store resources of a single type, designated when you create the registry.

Use custom registries to group related resources separately from the default local registry. Add resources to this registry using the optional registry name parameter.

#### Syntax

CopyExpand

```
CREATE_REGISTRY( '<registry_name>', <registry_type> )
```

Show lessSee more

Scroll to top

#### Arguments

`registry_name`
:   Name of the registry to create. Must be a unique name across all registries in the account.

`registry_type`
:   The type of resources this registry will contain. Supported values: `TEMPLATE`, `DATA OFFERING`.

#### Returns

A string success message.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.CREATE_REGISTRY(
  'my_custom_registry',
  'TEMPLATE'
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling the following procedure:

* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE REGISTRY', 'role name')`

---

### DROP\_REGISTRY

Schema:
:   REGISTRY

Drops a custom registry and unregisters all the objects that it contains. You can drop only registries that you created.

#### Syntax

CopyExpand

```
DROP_REGISTRY( '<registry_name>' )
```

Show lessSee more

Scroll to top

#### Arguments

`registry_name`
:   Name of the registry to drop.

#### Returns

A string success message.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.DROP_REGISTRY(
  'my_custom_registry'
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling the following procedure:

* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE REGISTRY', 'role name')`

---

### VIEW\_REGISTRIES

Schema:
:   REGISTRY

Lists all registries that you have access to, including the default local registry and any custom registries.

#### Syntax

CopyExpand

```
VIEW_REGISTRIES()
```

Show lessSee more

Scroll to top

#### Arguments

None.

#### Returns

A table with a row for each registry that you can access.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.VIEW_REGISTRIES();
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted privileges by calling one of the following procedures:

* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('JOIN COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE REGISTRY', 'role name')`

For a custom registry to be visible to VIEW\_REGISTRIES, you must also have READ or REGISTER privileges, granted by one of the
following procedure calls:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('READ', 'REGISTRY', 'registry name', 'role name')`
* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('REGISTER', 'REGISTRY', 'registry name', 'role name')`

## Access management procedures

The SAMOOHA\_APP\_ROLE role grants access to all DCR Collaboration API procedures. However, if an administrator wants to grant more granular privileges to specific roles, you can create a role and grant it specific privileges with the procedures described in this section.

Learn more about granting access at [Manage access to the DCR Collaboration API](#label-dcr-collaboration-access-management-overview).

The following procedures are used to manage fine-grained access to the Snowflake Data Clean Room Collaboration API:

### GRANT\_PRIVILEGE\_ON\_OBJECT\_TO\_ROLE

Schema:
:   ADMIN

Grants the rights to call specific procedures on a specific object to a specific role. The type of object is defined using the OBJECT\_TYPE argument.

You can call this procedure multiple times to grant multiple
permissions to the same role. Run this procedure using the role that owns the object.

#### Syntax

CopyExpand

```
GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE(
  <privilege>,
  <object_type>,
  <object_name>,
  <account_role_name> )
```

Show lessSee more

Scroll to top

#### Arguments

`privilege`
:   What permission this role should be granted. See the table below to learn which privileges are available for which objects.

`object_type`
:   The type of object that this role is being granted permissions on. Supported values:

    * `COLLABORATION`
    * `REGISTRY`

`object_name`
:   The ID of the object, as specified in the object’s specification.

`account_role_name`
:   The role being granted.

The following privilege and object type combinations are supported:

| Privilege | Object type | Procedures enabled |
| --- | --- | --- |
| `READ` | `COLLABORATION` | `VIEW_COLLABORATIONS`, `GET_STATUS`, `VIEW_DATA_OFFERINGS`, `VIEW_UPDATE_REQUESTS`, `VIEW_TEMPLATES` |
| `RUN` | `COLLABORATION` | `RUN`, `VIEW_ACTIVATIONS`, `VIEW_COLLABORATIONS` |
| `UPDATE` | `COLLABORATION` | `LINK_DATA_OFFERING`, `UNLINK_DATA_OFFERING`, `LINK_LOCAL_DATA_OFFERING`, `UNLINK_LOCAL_DATA_OFFERING`, `ADD_TEMPLATE_REQUEST`, `REMOVE_TEMPLATE`, `APPROVE_UPDATE_REQUEST`, `REJECT_UPDATE_REQUEST`, `ENABLE_TEMPLATE_AUTO_APPROVAL`, `DISABLE_TEMPLATE_AUTO_APPROVAL`, `VIEW_UPDATE_REQUESTS` |
| `READ` | `REGISTRY` | View resources registered in a [custom registry](registries). |
| `REGISTER` | `REGISTRY` | Register resources such as templates and data offerings in a [custom registry](registries). |

See moreShow less

Expand

#### Returns

A string success message.

#### Example

This example creates a role for analysts to use to run analyses in a collaboration named `my_collaboration` and assigns it to a user.

CopyExpand

```
USE ROLE role_that_created_this_collaboration;

CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.ADMIN.GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE(
  'RUN',
  'COLLABORATION',
  $collaboration_name,
  'collaborator_analyst_role'
);
GRANT ROLE collaborator_analyst_role to USER alexander_hamilton;
```

Show lessSee more

Scroll to top

#### Access requirements

You must use the same role that created the object to call `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE` on that object.

---

### GRANT\_PRIVILEGE\_ON\_ACCOUNT\_TO\_ROLE

Schema:
:   ADMIN

Grants account-level privileges to a role. This procedure enables anyone using that role to call the procedures listed for that privilege.

#### Syntax

CopyExpand

```
GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE( <privilege>, <account_role_name> )
```

Show lessSee more

Scroll to top

#### Arguments

`privilege`
:   The privilege to grant this role. The following string values are supported:

    * `JOIN COLLABORATION`: Grants permission to run `COLLABORATION.JOIN` as well as the following procedures on the joined collaboration:

      + `ADMIN.GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE`
      + `COLLABORATION.ADD_TEMPLATE_REQUEST`
      + `COLLABORATION.APPROVE_UPDATE_REQUEST`
      + `COLLABORATION.ENABLE_TEMPLATE_AUTO_APPROVAL`
      + `COLLABORATION.DISABLE_TEMPLATE_AUTO_APPROVAL`
      + `COLLABORATION.REMOVE_TEMPLATE`
      + `COLLABORATION.GET_STATUS`
      + `COLLABORATION.LEAVE`
      + `COLLABORATION.LINK_DATA_OFFERING`
      + `COLLABORATION.LINK_LOCAL_DATA_OFFERING`
      + `COLLABORATION.PROCESS_ACTIVATION`
      + `COLLABORATION.REJECT_UPDATE_REQUEST`
      + `COLLABORATION.REVIEW`
      + `COLLABORATION.RUN`
      + `COLLABORATION.TEARDOWN`
      + `COLLABORATION.UNLINK_DATA_OFFERING`
      + `COLLABORATION.UNLINK_LOCAL_DATA_OFFERING`
      + `COLLABORATION.VIEW_ACTIVATIONS`
      + `COLLABORATION.VIEW_COLLABORATIONS`
      + `COLLABORATION.VIEW_DATA_OFFERINGS`
      + `COLLABORATION.VIEW_TEMPLATES`
      + `COLLABORATION.VIEW_UPDATE_REQUESTS`
      + `REGISTRY.VIEW_REGISTERED_DATA_OFFERINGS`
      + `REGISTRY.VIEW_REGISTERED_TEMPLATES`

      This privilege requires the following account-level privileges to be granted to the role manually:

      + APPLY ROW ACCESS POLICY
      + CREATE APPLICATION
      + CREATE DATABASE
      + CREATE LISTING
      + CREATE SHARE
      + IMPORT SHARE
      + MANAGE SHARE TARGET
    * `CREATE COLLABORATION`: Grants permission to run `COLLABORATION.INITIALIZE`, plus all procedures allowed by `JOIN COLLABORATION`
      for the joined collaboration. Requires the following account-level privileges to be granted manually to the role:

      + APPLY ROW ACCESS POLICY
      + CREATE APPLICATION
      + CREATE DATABASE
      + CREATE LISTING
      + CREATE SHARE
      + IMPORT SHARE
      + MANAGE SHARE TARGET
      + EXECUTE TASK (if using auto-join in the INITIALIZE procedure)
    * `VIEW COLLABORATIONS`: Grants permission to run `COLLABORATION.VIEW_COLLABORATIONS`. Requires the following privileges to be granted manually to the role:

      + IMPORT SHARE
    * `REGISTER DATA OFFERING`: Grants permission to run `REGISTRY.REGISTER_DATA_OFFERING`. This permission is required for any analysis runner to join a collaboration that implements activation.
    * `VIEW REGISTERED DATA OFFERINGS`: Grants permission to run `REGISTRY.VIEW_REGISTERED_DATA_OFFERINGS`.
    * `REGISTER TEMPLATE`: Grants permission to run `REGISTRY.REGISTER_TEMPLATE`.
    * `VIEW REGISTERED TEMPLATES`: Grants permission to run `REGISTRY.VIEW_REGISTERED_TEMPLATES`.
    * `REGISTER CODE SPEC`: Grants permission to run `REGISTRY.REGISTER_CODE_SPEC`.
    * `VIEW REGISTERED CODE SPECS`: Grants permission to run `REGISTRY.VIEW_REGISTERED_CODE_SPECS`.
    * `CREATE REGISTRY`: Grants permission to run `REGISTRY.CREATE_REGISTRY` and `REGISTRY.DROP_REGISTRY`, and also the ability to read from custom registries.
    * `MANAGE LISTING AUTO FULFILLMENT`: Grants permission for a collaborator to join a collaboration in another cloud hosting region. For more information, see [Enable Cross-Cloud Auto-Fulfillment](using.html#label-dcr-collab-enabling-laf).

`account_role_name`
:   The name of an account-level role.

#### Returns

A string success message.

#### Example

CopyExpand

```
USE ROLE ACCOUNTADMIN;
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.ADMIN.GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE(
  'REGISTER DATA OFFERING',
  'COLLABORATOR_ANALYST_ROLE'
);
```

Show lessSee more

Scroll to top

#### Access requirements

You need the ACCOUNTADMIN role to run this procedure.
