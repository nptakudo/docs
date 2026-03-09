---
title: "SCIM group API reference"
url: "https://docs.snowflake.com/en/user-guide/scim-group-api-reference"
---

# SCIM group API reference

You can use the SCIM group API to access, create, and modify roles.

Snowflake uses SCIM to import roles from Okta, Azure AD and custom-built applications. The roles in these identity providers map one-to-one
with Snowflake roles.

Roles, sometimes called groups, are a collection of access privileges. To access securable objects in Snowflake, privileges must be assigned
to roles, and roles are assigned to other roles or users.

Access permissions and rights that are granted to the role are automatically inherited by every member, such as a user, of the role. For
more information, see [Overview of Access Control](security-access-control-overview).

A user’s access requirements to Snowflake can change. For example, a user can change from being an individual contributor to a manager in
their organization, which may require their role in Snowflake to change, or they may require access to data sets only available to managers.

As the user’s role changes in the identity provider, their access to Snowflake automatically changes when their organization role maps to the
corresponding Snowflake role.

## HTTP headers

The Snowflake SCIM API uses bearer tokens for HTTP authentication.

Each HTTP request to the Snowflake SCIM API allows the following HTTP headers:

| Header | Value |
| --- | --- |
| `Authorization` (Required) | `Bearer <access_token>` |
| `Content-Type` | `application/scim+json` |
| `Accept-Encoding` | `utf-8` |
| `Accept-Charset` | `utf-8` |

See moreShow less

Expand

## Group attributes

You can specify group (that is, a role) attributes in the body of the API requests as key-value pairs in JSON format. These pairs contain
information about the group, such as the group’s display name. Identity providers can specify their own key names for each attribute.

Snowflake supports the following SCIM attributes for role lifecycle management. Attributes are writable unless otherwise noted.

| SCIM Group Attribute | Snowflake Group Attribute | Type | Description |
| --- | --- | --- | --- |
| `id` | `id` | String | The immutable, unique identifier (GUID) of the role in Snowflake.  Snowflake does not return this value.  You can find this value by calling the Information Schema table function [REST\_EVENT\_HISTORY](../sql-reference/functions/rest_event_history). Check the IdP logs to ensure the values match. |
| `displayName` | `name` | String | The text shown in the user interface when referring to the group. |
| `members.value` | N/A | String | The `id` of the user who is a member of the role. |
| `schemas` | N/A | String | An array of strings to indicate the namespace URIs. For example, `urn:ietf:params:scim:schemas:core:2.0:Group`. |

See moreShow less

Expand

## Get details about a group by displayName

Method and endpoint:
:   `GET /scim/v2/Groups?filter=displayName eq "{{group_name}}"`

Description:
:   Returns details about a group associated with the `displayName` query parameter.

    Returns the HTTP response status code `200` if the HTTP request successfully completed.

## Get details about a group by groupId

Method and endpoint:
:   `GET /scim/v2/Groups/{{group_id}}`

Description:
:   Returns details about a group associated with the `group_id` path parameter.

    Returns the HTTP response status code `200` if the HTTP request successfully completed.

## Create a group

Method and endpoint:
:   `POST /scim/v2/Groups`

Description:
:   Creates a new group in Snowflake.

    Returns the HTTP response status code `201` if the HTTP request successfully completed.

Examples:
:   Create a group with the `displayName` `scim_test_group2`:

    CopyExpand

    ```
    {
      "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
      "displayName":"scim_test_group2"
    }
    ```

    Show lessSee more

    Scroll to top

## Update a group

Method and endpoint:
:   `PATCH /scim/v2/Groups/{{group_id}}`

Description:
:   Updates the display name attribute or group membership of the group associated with the `group_id` path parameter.

    You must set `op` to `add` or `replace` to perform this HTTP request.

    Returns a `200` or `204` HTTP response status code if the HTTP request successfully completed. A `200` status code indicates the
    SCIM client is Okta.

Examples:
:   Update a group `displayName`, remove a member and add a member:

    CopyExpand

    ```
    {
      "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations": [{
        "op": "replace",
        "value": { "displayName": "updated_name" }
      },
      {
        "op" : "remove",
        "path": "members[value eq \"user_id_1\"]"
      },
      {
        "op": "add",
        "value": [{ "value": "user_id_2" }]
      }]
    }
    ```

    Show lessSee more

    Scroll to top

## Delete a group

Method and endpoint:
:   `DELETE /scim/v2/Groups/{{group_id}}`

Description:
:   Deletes the group associated with the `group_id` path parameter.
