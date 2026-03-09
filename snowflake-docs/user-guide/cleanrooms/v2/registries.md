---
title: "Registries"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/v2/registries"
---

# Registries

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) [Preview Feature](../../../release-notes/preview-features) — Open

Available to all accounts.

Required version

To use Collaboration Data Clean Rooms, you must upgrade to Snowflake Data Clean Rooms version 12.9 or later.

Before you can add a template, data offering, or other resource to a collaboration, you must register the resource in a *registry*. A registry is an account-level container that stores resources. After a resource is registered, it can be added to a collaboration by anyone in your account who can access the registry. Registries are not linked to a specific collaboration, but resources in the registry can be linked to zero or more collaborations in that account.

Each Snowflake account supports a default registry. You can create additional custom registries for your account. Custom registries are a good way to group and manage access to your resources. For example, you could create a custom registry for sales data and another for expenditure data, then grant access to these registries to the appropriate users via RBAC roles.

Here are the main rules about registries:

* Each custom registry supports a single resource type (template, data offering, and so on). The data type is specified when you create the registry. The default registry supports all resource types.
* There is no limit to how many custom registries you can create in an account.
* When you register a resource, you can use the optional registry name parameter to specify a custom registry. If you do not specify a custom registry, the resource will be registered in the default registry for the account.
* All users have access to the default registry in an account. Custom registries, however, are initially private to the creator, and additional users must be granted access explicitly by calling `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE`.
* An account can have multiple registries that store the same resource type.
* Registries do not have a maximum number of resources.
* A resource must have a unique name across all registries in that account for resources of that type. For example, you can have a template named `sales` and a data offering named `sales` in the same account, but not two templates named `sales` in either the same or different registries in the same account. The resource name is defined as the highest level `name` value in the spec.
* When you link a resource into a collaboration, the resource is visible to anyone who can access it according to the spec. Access to the containing registry is not required.
* If two different accounts register resources with the same name and type, that is allowed. The collaboration specification will show identically named resources, but the system will know which resource is intended — the resource with that name is used from the account that added the resource to the collaboration.

## Example

This example creates a custom registry, registers a data offering in it, and grants read access to that registry through a new role.

CopyExpand

```
-- Create a custom registry that can hold data offerings.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.CREATE_REGISTRY(
  'SALES',
  'DATA_OFFERING'
);

-- Add a data offering to the custom registry.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.REGISTER_DATA_OFFERING(
  'SALES',
  $$
  api_version: 2.0.0
  spec_type: data_offering
  version: v1
  name: FL_SALES
  datasets:
   - alias: customers_1
     data_object_fqn: SALES_DB.PUBLIC.FL_DATA
     allowed_analyses: template_only
     schema_and_template_policies:
       company_name:
         category: passthrough
       total_sales:
         category: passthrough
  $$
);

-- Create a role and grant it access to the registry.
CREATE ROLE MARKETING_USERS;
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.ADMIN.GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE(
  'READ',
  'REGISTRY',
  $collaboration_name,
  'MARKETING_USERS'
);

-- Grant access to the registry for a user by assigning the role.
GRANT ROLE MARKETING_USERS to USER willy_loman;
```

Show lessSee more

Scroll to top
