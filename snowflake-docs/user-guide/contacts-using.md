---
title: "Using Contacts"
url: "https://docs.snowflake.com/en/user-guide/contacts-using"
---

# Using Contacts

Contacts are schema-level objects that contain details about how to communicate with a user or group of users. For example, one contact
named `data_stewards` might include an email distribution list while another named `support_department` might include the URL of the
department’s website.

Contacts can be associated with other objects such as databases and tables so users can reach the right person for assistance with those
objects. For example, there might be a contact on a table that describes who to reach out to in order to gain access to
the table. The purpose of the contact is not a property of the contact, but rather the association between a contact and a specific object.
For example, the same contact might provide general support for one table while providing access approval for another.

An object can have more than one contact as long as the purpose of each contact is unique for the object. For example, a table might have
one contact that grants access to the table and another contact that provides general support for the table. When a user views the contacts
associated with an object, they see the purpose of each contact along with a communication method, so they know who to communicate with for
a specific reason and how to reach them.

Data users see these contacts when they are using the Database Explorer in Snowsight to navigate their databases, schemas, and
table-like objects.

## Inheriting and overriding contacts

Contacts are inherited by descendant objects. If you associate a contact with an object that is the parent of another object, the child,
grandchild, and so on inherit the contact. For example, if you associate a contact with a schema, all of the tables in the schema inherit
the contact by default.

Contact inheritance is overridden if a child object has a contact with the same purpose. For example, suppose someone associates the
following two contacts with the `ac_sch` schema, which contains the table `t1`:

| Contact | Purpose |
| --- | --- |
| `data_stewards` | Steward |
| `business_unit1` | Approver |

See moreShow less

Expand

Now suppose someone associates the contact `finance_dept` with `t1` for the purpose of access approval. The contacts associated with
`t1` are now the following:

| Contact | Purpose |
| --- | --- |
| `data_stewards` | Steward |
| `finance_dept` | Approver |

See moreShow less

Expand

The contact responsible for access approval on the `ac_schema` has been replaced with a contact directly associated with `t1`, but
`t1` continues to inherit the `data_stewards` contact from the schema.

## Supported objects

You can associate a contact with the following objects:

|  |  |  |
| --- | --- | --- |
| * Database * Schema * Table * Apache Iceberg™ table | * External table * Dynamic table * Event table | * View * Materialized view * Task (SQL only) |

## Create a contact

When you create a contact, you specify the name of the contact and how someone should communicate with the contact. Communication methods
include the following:

* The URL of a website.
* An email address, which can be a distribution list.
* A list of Snowflake users.

You can create a contact using Snowsight or SQL. It can be helpful to create all contacts in a dedicated schema.

Snowsight:
:   1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
    2. In the navigation menu, select Catalog » Database Explorer.
    3. Navigate to the schema where you want to create the contact.
    4. Select Create » Contact.
    5. Specify the name of the contact.
    6. Choose a communication method, then specify the email address, users, or URL that data users will contact for assistance with an object.
    7. Select the roles that will have the ability to associate the contact with objects. These roles are granted the APPLY privilege on the contact.
    8. Select Create.

SQL:
:   You can execute the [CREATE CONTACT](../sql-reference/sql/create-contact) command to create a new contact.

    **Examples**

    Create a contact for the support team that is reached through their website.

    CopyExpand

    ```
    CREATE CONTACT support_dept URL = 'http://internalsupport.example.com';
    ```

    Show lessSee more

    Scroll to top

    Create a contact for the finance team that is reached via an email address, which acts as a distribution list.

    CopyExpand

    ```
    CREATE CONTACT finance_dept EMAIL_DISTRIBUTION_LIST = 'fd_dl@example.com';
    ```

    Show lessSee more

    Scroll to top

    Create a contact for database administrators, as identified by the name of their Snowflake user objects.

    CopyExpand

    ```
    CREATE CONTACT db_admins USERS = ('ex_admin1', 'ex_admin2');
    ```

    Show lessSee more

    Scroll to top

## Associate a contact with an object

When you associate a contact with an object, you specify the name of the contact along with the purpose of the association between the
contact and the object. When users view all of the contacts associated with an object, they’ll be able decide who to communicate with based on the purpose of each
contact.

The purpose of having a contact associated with an object can be one of the following:

| Purpose | Description | SQL value |
| --- | --- | --- |
| Steward | Provides information about the accuracy, consistency, and reliability of data. | `STEWARD` |
| Support | Provides technical support related to a dataset. | `SUPPORT` |
| Approver | Approves or rejects requests to access data. | `ACCESS_APPROVAL` |

See moreShow less

Expand

You can associate a contact and define its purpose when modifying an existing object or creating a new object.

### Associate a contact with an existing object

Snowsight:
:   1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
    2. In the navigation menu, select Catalog » Database Explorer.
    3. Navigate to one of the [supported objects](#label-contacts-supported-objects).
    4. Select the Details tab.
    5. Find the Assigned Contacts section and select the Edit icon.
    6. Select a contact for one or more of the purposes. For example, if you select a contact from the Approver drop-down list, data
       users will reach out to that contact when they need access to the object.
    7. Select Save.

SQL:
:   The ALTER … SET CONTACT command for an existing object lets you associate a contact and specify the purpose of the contact for that
    object. The syntax to associate the contact is the same for all objects that can be associated with a contact:

    CopyExpand

    ```
    ALTER <object_type> <object_name>
      SET CONTACT <purpose> = <contact_name> [ , <purpose> = <contact_name> ... ]
    ```

    Show lessSee more

    Scroll to top

    The `purpose` must be one of the [predefined purposes](#label-contacts-associate-purposes), which describe the contact’s
    relationship to the object. When users view the contacts of an object, they use the purpose to determine which of the contacts to
    communicate with.

    **Examples**

    Associate the `finance_dept` contact with a table so users know who to communicate with when they need access to the table:

    CopyExpand

    ```
    ALTER TABLE t1 SET CONTACT ACCESS_APPROVAL = finance_dept;
    ```

    Show lessSee more

    Scroll to top

    Associate the `internal_support` contact with a database so users know who to communicate with when they need general support for a table
    in the database:

    CopyExpand

    ```
    ALTER DATABASE my_db SET CONTACT SUPPORT = internal_support;
    ```

    Show lessSee more

    Scroll to top

    Associate the `data_stewards` contact with a schema so users know who to communicate with regarding object tagging of tables in the schema:

    CopyExpand

    ```
    ALTER SCHEMA sch1 SET CONTACT STEWARD = data_stewards;
    ```

    Show lessSee more

    Scroll to top

    Note

    If you want to set a contact on an existing Iceberg table, external table, or dynamic table, you must use the ALTER TABLE
    command.

### Associate a contact when creating a new object

The CREATE … WITH CONTACT command lets you associate a contact with a new object. The syntax for the WITH CONTACT clause is the same for all
objects that can be associated with a contact:

CopyExpand

```
WITH CONTACT ( <purpose> = <contact_name> [ , <purpose> = <contact_name> ... ] )
```

Show lessSee more

Scroll to top

The `purpose` must be one of the [predefined purposes](#label-contacts-associate-purposes), which describe the contact’s
relationship to the object. When users view the contacts of an object, they use the purpose to determine which of the contacts to
communicate with.

For tables and table-like objects, the WITH CONTACT clause is specified after the column definitions.

#### Examples

Associate the `finance_dept` contact with a new table so users know who to communicate with when they need access to the table:

CopyExpand

```
CREATE TABLE t1 (col1 VARCHAR, col2 INT) WITH CONTACT (ACCESS_APPROVAL = finance_dept);
```

Show lessSee more

Scroll to top

Associate the `internal_support` contact with a new database so users know who to communicate with when they need general support for a
table in the database:

CopyExpand

```
CREATE DATABASE my_db WITH CONTACT (SUPPORT = internal_support);
```

Show lessSee more

Scroll to top

Associate the `data_stewards` contact with a new schema so users know who to communicate with regarding object tagging of tables in the
schema and the `finance_dept` contact so users know who to communicate with when they need access:

CopyExpand

```
CREATE SCHEMA sch1 WITH CONTACT (STEWARD = data_stewards, ACCESS_APPROVAL = finance_dept);
```

Show lessSee more

Scroll to top

## Detach a contact from an object

Snowsight:
:   1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
    2. In the navigation menu, select Catalog » Database Explorer.
    3. Navigate to the object.
    4. Select the Details tab.
    5. Find the Assigned Contacts section and select the Edit icon.
    6. Find the purpose (for example, Approver) that has the contact that you want to detach, and use the drop-down list to select None.
    7. Select Save.

SQL:
:   The ALTER … UNSET CONTACT commands lets you detach a contact from an object. The syntax to detach the contact is the same for all
    objects that can be associated with a contact:

    CopyExpand

    ```
    ALTER <object_type> <object_name>
      UNSET CONTACT <purpose>
    ```

    Show lessSee more

    Scroll to top

    You identify the contact to detach by specifying the purpose of the association between the contact and the object, not by the contact
    name. For a list of possible purposes that can be specified to detach a contact, see
    [predefined purposes](#label-contacts-associate-purposes).

    For example, to detach the contact that was added as the STEWARD of a table, execute:

    > CopyExpand
    >
    > ```
    > ALTER TABLE t1 UNSET CONTACT STEWARD;
    > ```
    >
    > Show lessSee more
    >
    > Scroll to top

    Note

    If you want to unset a contact on an existing Iceberg table, external table, or dynamic table, you must use the ALTER TABLE
    command.

## View contacts for an object

Snowsight:
:   When you navigate to an object in Snowsight, the contacts that are associated with the object appear on the Details tab.

SQL:
:   Users with at least one privilege on an object can use the [GET\_CONTACTS](../sql-reference/functions/get_contacts) table function to view the
    contacts associated with that object. The function returns a row for each contact associated with the object.

    For example, to list the contacts on the table `t1`, a user with at least one privilege can execute the following:

    CopyExpand

    ```
    SELECT *
      FROM TABLE(SNOWFLAKE.CORE.GET_CONTACTS('t1', 'TABLE'));
    ```

    Show lessSee more

    Scroll to top

    For each contact, the function lists the following:

    * Purpose of the contact.
    * Method of communication for the contact.
    * Whether the contact was associated with the object directly or inherited from a parent object.

## Governing contacts and their associations

Snowsight:
:   To list the contacts that have been created in a schema and drill down into the details for a specific contact:

    1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
    2. In the navigation menu, select Catalog » Database Explorer, and then select the schema.
    3. Select the Contacts tab.
    4. Select a contact if you want to view details about it, including the objects with which it is associated.

SQL:
:   The ACCOUNT\_USAGE schema provides the following views to help manage contacts:

    * [CONTACTS view](../sql-reference/account-usage/contacts) - Lists all contacts in the account.
    * [CONTACT\_REFERENCES view](../sql-reference/account-usage/contact_references) - Lists the objects with which a contact has been associated.

## Access control privileges

The following summarizes the privileges a user needs to work with contacts.

| Task | Required privileges |
| --- | --- |
| [Create a contact](#label-contacts-create) | Both of the following:   * CREATE CONTACT on the schema * USAGE on the schema and database |
| [Associate a contact with an object](#label-contacts-associate) | Either of the following:   * APPLY CONTACT on the account * APPLY privilege on the contact and OWNERSHIP on the object |
| [List the contacts for an object](#label-contacts-view-for-object) | Any privilege on the object. |
| [Detach a contact from an object](#label-contacts-associate) | Either of the following:   * APPLY CONTACT on the account * APPLY privilege on the contact and OWNERSHIP on the object |
| [Modify an existing contact](../sql-reference/sql/alter-contact) | Either of the following:   * OWNERSHIP on the contact * MODIFY on the contact |
| [Drop a contact](../sql-reference/sql/drop-contact) | OWNERSHIP on the contact |

See moreShow less

Expand
