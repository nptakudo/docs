---
title: "SCIM user API reference"
url: "https://docs.snowflake.com/en/user-guide/scim-user-api-reference"
---

# SCIM user API reference

You can use the SCIM user API to access, create, and modify user data.

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

## User attributes

You can specify user attributes in the body of the API requests as key-value pairs in JSON format. These pairs contain information about the
user, such as the user’s display name or their email address. Identity providers can specify their own key names for each attribute. For
example, identity providers can use the key `lastName`, instead of `familyName`, to represent the user’s last name. Snowflake
does not support multi-value user attributes.

Important

In the table below, the attributes `userName` and `loginName` both refer to the attribute `userName`. Snowflake
supports specifying different values for the `userName` and `loginName` attributes.

Snowflake supports the following attributes for user lifecycle management:

| SCIM User Attribute | Snowflake User Attribute | Type | Description |
| --- | --- | --- | --- |
| `id` | `ID` | string | The immutable, unique identifier (GUID) of the user in Snowflake.  Snowflake does not return this value in the [DESCRIBE USER](../sql-reference/sql/desc-user) or [SHOW USERS](../sql-reference/sql/show-users) output.  For requests on endpoints that require this attribute, such as `PATCH scim/v2/Users/{{id}}`, the `id` attribute can be found using the [REST\_EVENT\_HISTORY](../sql-reference/functions/rest_event_history) function. Check the IdP logs to ensure the values match. |
| `userName` | `NAME`, `LOGIN_NAME` | string | The identifier used to login into Snowflake. For more information about these attributes, see [CREATE USER](../sql-reference/sql/create-user). |
| `name.givenName` | `FIRST_NAME` | string | The first name of the user. |
| `name.familyName` | `LAST_NAME` | string | The last name of the user. |
| `emails` | `EMAIL` | string | The email address of the user. |
| `displayName` | `DISPLAY_NAME` | string | The text shown in the user interface when referring to the user. |
| `externalID` | N/A | string | The unique identifier set by the provisioning client (e.g. Azure, Okta). |
| `password` | `PASSWORD` | string | The password for the user.  This value is not returned in the JSON response.  If the `SYNC_PASSWORD` property in the SCIM security integration is set to `FALSE`, and the SCIM API request specifies the `password` attribute, Snowflake ignores the value for the `password` attribute. All other attributes in the API request are processed normally. |
| `active` | `DISABLED` | boolean | Disables the user when set to `false`. |
| `groups` | N/A | string | A list of groups to which the user belongs. The group `displayName` is required.  The user’s groups are immutable and their membership must be updated using the [SCIM groups API](scim-group-api-reference.html#label-scim-update-a-group). |
| `meta.created` | `CREATED_ON` | string | The time the user was added to Snowflake. |
| `meta.lastModified` | `UPDATED_ON` | string | The time the user was last modified in Snowflake. |
| `meta.resourceType` | N/A | string | The type of resource for the user. You should use `user` as a value for this attribute. |
| `schemas` | N/A | string | A comma-separated array of strings specifying the namespace URIs. Snowflake supports the following values:   * `urn:ietf:params:scim:schemas:core:2.0:User` * `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User` * `urn:ietf:params:scim:schemas:extension:2.0:User` |

See moreShow less

Expand

## Custom attributes

You can set custom attributes that are not defined by [RFC 7643](https://datatracker.ietf.org/doc/html/rfc7643), such as
`defaultRole`.

You can use the following namespaces to set custom attributes when making POST, PUT, and PATCH requests:

`urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`
:   This namespace was part of the original SCIM implementation in Snowflake. You can only use this namespace for setting custom attributes in
    [Okta SCIM security integrations](scim-okta).

    You cannot use this namespace to set custom attributes in [Microsoft Azure SCIM security integrations](scim-azure) or
    [custom SCIM integrations](scim-custom).

`urn:ietf:params:scim:schemas:extension:2.0:User`
:   You can use this namespace to set custom attributes for all SCIM integrations. You must use this namespace for setting custom attributes
    in [Microsoft Azure SCIM security integrations](scim-azure) or
    [Custom SCIM security integrations](scim-custom).

Snowflake supports the following custom attributes:

| SCIM User Custom Attribute | Snowflake User Attribute | Type | Description |
| --- | --- | --- | --- |
| `allowedInterfaces` | `ALLOWED_INTERFACES` | string | Defines which Snowflake interfaces the user can access. Specified as a comma-delimited list of interfaces. For a list of possible interfaces, see [CREATE USER](../sql-reference/sql/create-user.html#label-create-user-allowed-interfaces). If a value other than `ALL` is specified, then users can only access the interface specified and cannot interact with any Snowflake data outside of the interface specified. |
| `defaultWarehouse` | `DEFAULT_WAREHOUSE` | string | The virtual warehouse that is active by default for the user’s session upon login. |
| `defaultRole` | `DEFAULT_ROLE` | string | The primary role that is active by default for the user’s session upon login. |
| `defaultSecondaryRoles` | `DEFAULT_SECONDARY_ROLES` | string | The list of secondary roles that are active for the user’s session upon login. You can set this attribute to `ALL` as an alias for `('ALL')`, or you can set this attribute to `NONE` or `""` as an alias for `()`. |
| `type` | `TYPE` | string | The type of user. Default: `person`. You can set this attribute to `person`, `service`, `legacy_service`, or `null`. For more information about types of users, see [Types of users](admin-user-management.html#label-user-management-types). |

See moreShow less

Expand

### Usage notes for TYPE attribute

Note

The LEGACY\_SERVICE type is being deprecated. Use the SERVICE type for services and applications. For a timeline of the deprecation of
LEGACY\_SERVICE, see [Planning for the deprecation of single-factor password sign-ins](security-mfa-rollout).

This list describes the effects of setting the TYPE attribute in the following SCIM requests:

* `POST` request to create a user, and the `type` attribute is unspecified or `NULL`, the `TYPE` property is set to
  `PERSON`.
* `PATCH` request with a replace operation that specifies the `type` attribute as `NULL`, `TYPE` property doesn’t
  change.
* `PUT` request with a replace operation, and the `type` attribute is unspecified or `NULL`, the `TYPE` property is set to
  `PERSON`.
* `PATCH` request with a remove operation that unsets the `type` attribute, the `TYPE` property doesn’t change.

## Check if a user exists

Method and endpoint:
:   `GET /scim/v2/Users?filter=userName eq "{{user_name}}"`

Description:
:   Returns details about a user associated with the `userName` query parameter.

    Returns the HTTP response status code `200` if the HTTP request successfully completed.

## Get details about a user

Method and endpoint:
:   `GET /scim/v2/Users/{{user_id}}`

Description:
:   Returns details about a user associated with the `user_id` path parameter.

    Returns the HTTP response status code `200` if the HTTP request successfully completed.

## Create a user

Method and endpoint:
:   `POST /scim/v2/Users`

Description:
:   Creates a user in Snowflake.

    Returns the HTTP response status code `201` if the HTTP request successfully completed.

    If the user already exists or the HTTP request failed for a different reason, then Snowflake returns the HTTP response status code
    `409`.

Examples:
:   Create a user with `userName` and `loginName` mapped to the same value:

    CopyExpand

    ```
    {
      "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:2.0:User"
      ],
      "userName": "test_user_1",
      "password": "test",
      "name": {
        "givenName": "test",
        "familyName": "user"
      },
      "emails": [
        {"value": "test.user@snowflake.com"}
      ],
      "displayName": "test user",
      "active": true
    }
    ```

    Show lessSee more

    Scroll to top

    Create a user with `userName` and `loginName` mapped to different values:

    Note

    If you use Okta as your identity provider, follow this [procedure](scim-okta.html#label-okta-separate-attributes).

    CopyExpand

    ```
    {
      "active": true,
      "displayName": "test user",
      "emails": [
        {"value": "test.user@snowflake.com"}
      ],
      "name": {
        "familyName": "test_last_name",
        "givenName": "test_first_name"
      },
      "password": "test_password",
      "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
      ],
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
        "snowflakeUserName": "USER5"
      },
      "userName": "USER5"
    }
    ```

    Show lessSee more

    Scroll to top

## Replace user attributes

Method and endpoint:
:   `PATCH /scim/v2/Users/{{id}}`

Description:
:   Replaces attributes of the user associated with the `id` path parameter.

    You must set `op` to `replace` to perform this HTTP request.

    `active` allows the following values:

    * `false`: deactivates the user.
    * `true`: activates the user.

    Returns the HTTP response status code `200` if the HTTP request was successfully completed.

    If unsuccessful, returns the HTTP response code `204`.

Examples:
:   Deactivate a user and update their `givenName` to `deactivated_user`:

    CopyExpand

    ```
    {
      "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations": [
        {"op": "replace", "value": { "active": false }}
        {"op": "replace", "value": { "givenName": "deactivated_user" }}
      ],
    }
    ```

    Show lessSee more

    Scroll to top

    Update a user with `userName` and `loginName` mapped to the same value:

    CopyExpand

    ```
    {
      "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations": [
        {"op": "replace", "value": { "active": false }}
      ]
    }
    ```

    Show lessSee more

    Scroll to top

    Update a user with `userName` and `loginName` mapped to different values.

    > If Okta is your identity provider, follow this [procedure](scim-okta.html#label-okta-separate-attributes).
    >
    > CopyExpand
    >
    > ```
    > {
    >   "Operations": [
    >     {
    >       "op": "replace",
    >       "path": "userName",
    >       "value": "test_updated_name"
    >     },
    >     {
    >       "op": "replace",
    >       "path": "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User.snowflakeUserName",
    >       "value": "USER5"
    >     }
    >   ],
    >   "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"]
    > }
    > ```
    >
    > Show lessSee more
    >
    > Scroll to top

## Update a user

Method and endpoint:
:   `PUT /scim/v2/Users/{{id}}`

Description:
:   Updates the attributes of the user associated with the `id` path parameter.

    If unsuccessful, returns the HTTP response code `400`. The HTTP request is unsuccessful if the request tries to change immutable
    attributes or if the attributes being changed do not exist in Snowflake.

Examples:
:   Update a user and their `"defaultRole"`, `"defaultSecondaryRoles"`, and `"defaultWarehouse"` attributes.

    To specify the `"defaultRole"`, `"defaultSecondaryRoles"`, and `"defaultWarehouse"` attributes, you must use one of the
    `extension` [schemas](#label-scim-intro-user-attributes). The `defaultSecondaryRoles` attribute only accepts `"ALL"` as
    a value.

    Note

    The PUT method is more expensive than the PATCH method. Use the PATCH operation instead.

    CopyExpand

    ```
    {
      "schemas": [
       "urn:ietf:params:scim:schemas:core:2.0:User",
       "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
      ],
      "userName": "test_user_1",
      "password": "test",
      "name": {
        "givenName": "test",
        "familyName": "user"
      },
      "emails": [{
        "primary": true,
        "value": "test.user@snowflake.com",
        "type": "work"
      }
      ],
      "displayName": "test user",
      "active": true,
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
        "defaultRole" : "test_role",
        "defaultSecondaryRoles" : "ALL",
        "defaultWarehouse" : "test_warehouse"
      }
    }
    ```

    Show lessSee more

    Scroll to top

## Delete a user

Method and endpoint:
:   `DELETE /scim/v2/Users/{{id}}`

Description:
:   Deletes the user associated with the `id` path parameter.
