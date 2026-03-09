---
title: "Collaborator roles in Collaboration Data Clean Rooms"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/v2/roles"
---

# Collaborator roles in Collaboration Data Clean Rooms

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) [Preview Feature](../../../release-notes/preview-features) — Open

Available to all accounts.

Required version

To use Collaboration Data Clean Rooms, you must upgrade to Snowflake Data Clean Rooms version 12.9 or later.

## Overview

Collaborators act as one or more of the following *roles* in a clean room collaboration scenario. In this case, a *role* is a set of capabilities, not an RBAC role:

* **Owner:** The owner defines, creates, and owns the collaboration, and defines which collaborators are invited and their roles. An owner isn’t automatically an analysis runner or a data provider, and doesn’t have any elevated run privileges. The owner’s main abilities are to create the clean room, assign roles, determine who can share data with whom, and tear down the clean room. A collaboration can have only one owner.
* **Data provider:** Provides data offerings, such as tables and views, to a collaboration, and specifies which analysis runners can use them. That is, account A is a data provider to accounts B and C, as specified in the collaboration description.
* **Analysis runner:** Runs permitted templates on permitted data offerings, as specified by the collaboration definition.

One collaborator can have multiple roles in a collaboration, and multiple collaborators can have the same role in a collaboration (except for the owner role, which is assigned to only one user). For example, the owner of a collaboration can also be a data provider and an analysis runner.

You can see your available roles for a collaboration in the ROLES column by calling `GET_STATUS`, which lists your roles in the ROLES column. For example, if you are a data provider and want to see whom you can share data with, examine the spec for your collaboration to see your role details. Here is how to see the collaboration spec in a single call after you have joined a collaboration:

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.VIEW_COLLABORATIONS() ->>
  SELECT "COLLABORATION_SPEC" FROM $1
    WHERE "SOURCE_NAME" = $collaboration_name;
```

Show lessSee more

Scroll to top

The owner specifies all collaborators and their roles when they create the collaboration. Collaborators and their roles can’t be changed after a collaboration is created. As a consequence, the following role features are fixed after a collaboration is created:

* The owner can’t be changed.
* Analysis runners can’t be added or removed.
* The list of data providers for each analysis runner can’t be changed. If account A is not defined as a data provider for account B when the collaboration is created, account A can never be a data provider for account B.

However, collaborators *can* add or remove resources after a collaboration is created.

## Example

The following example shows a very basic collaboration definition that defines roles, but doesn’t define any resources. You can create a collaboration with or without resources, and add or remove them later.

CopyExpand

```
api_version: 2.0.0
spec_type: collaboration
name: basic_collaboration
owner: alice
collaborator_identifier_aliases:
  alice: corp1.acct123
  bob: corp2.acctxyz
analysis_runners:
  alice:
    data_providers:
      alice:
        data_offerings: []
      bob:
        data_offerings: []
  bob:
    data_providers:
      alice:
        data_offerings: []
```

Show lessSee more

Scroll to top

The previous collaboration defines the following collaborators and roles:

* `alice` is the collaboration owner, an analysis runner, and a data provider for `bob` and herself. `alice` is the alias defined in the collaboration for account `corp1.acct123`.
* `bob` is an analysis runner, and a data provider for `alice` but *not* for himself. `bob` is the alias defined in the collaboration for account `corp2.acctxyz`.

These roles can’t be modified, and new collaborators can’t be added, after the collaboration is created.

Data providers can add data offerings after a collaboration is created. Any collaborator can add templates after a collaboration is created.
The following example shows how you can use the Collaboration API to add resources to the previous collaboration after it is created:

CopyExpand

```
api_version: 2.0.0
spec_type: collaboration
name: basic_collaboration
owner: alice
collaborator_identifier_aliases:
  alice: corp1.acct123
  bob: corp2.acctxyz
analysis_runners:
  alice:
    data_providers:
      alice:
        data_offerings:
        - id: alice_data_1
        - id: alice_data_2
      bob:
        data_offerings:
        - id: bob_data_1
    templates:
    - id: template1  # Alice can run template1 using alice_data_1, alice_data_2, or bob_data_1.
  bob:
    data_providers:
      alice:
        data_offerings:
        - id: alice_data_1
    templates:
    - id: template2  # Bob can run template2 using data from alice_data_1, provided by alice.
```

Show lessSee more

Scroll to top

The modified collaboration now supports the following resources and capabilities:

* `alice` can run analyses using `template1` with data from `alice_data_1`, `alice_data_2`, and `bob_data_1`.
* `bob` can run `template2` using data from `alice_data_1`.
