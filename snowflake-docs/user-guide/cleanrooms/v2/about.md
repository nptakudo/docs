---
title: "About Snowflake Collaboration Data Clean Rooms"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/v2/about"
---

# About Snowflake Collaboration Data Clean Rooms

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) [Preview Feature](../../../release-notes/preview-features) — Open

Available to all accounts.

Required version

To use Collaboration Data Clean Rooms, you must upgrade to Snowflake Data Clean Rooms version 12.9 or later.

## Overview

Snowflake Data Clean Rooms is previewing a new data clean room architecture called Collaboration Data Clean Rooms. Collaboration Data Clean Rooms allow customers to collaborate in a fully symmetric, multi-party environment. Unlike traditional provider-consumer models, which limit the roles and number of collaborators, the Collaboration API supports flexible roles and fine-grained data access controls for any number of participants.

### Provider and Consumer Data Clean Rooms vs. Collaboration Data Clean Rooms

Provider and consumer clean rooms – the first data clean rooms architecture – were designed primarily for two-party collaborations. Collaboration Data Clean Rooms support multi-party collaboration without additional complexity. The new design will replace the concepts of provider and consumer, provider-run analyses versus consumer-run analyses, and simple collaboration versus multi-party collaboration with a configurable clean room that allows any party to contribute data and templates, and run analyses.

![High-level overview of collaboration with two participants](../../../_images/dcr-v1-vs-v2.png)

Both provider and consumer clean rooms and Collaboration Data Clean Rooms will coexist for some time. Your provider and consumer clean rooms are still usable, and won’t be modified or removed. However, we
encourage you to try out the new Collaboration Data Clean Rooms. Our goal is to migrate all users to Collaboration Data Clean Rooms, which should provide a much simpler and more robust collaboration experience.

### Introduction to Collaboration Data Clean Rooms

In the new data clean rooms architecture, a clean room is called a *collaboration*, and all users with access to the collaboration are called *collaborators*. Each collaborator has one or more *roles* in the collaboration. In this case, the term *role* doesn’t refer to an RBAC role [[\*]](#id2), but to a set of permissions that define what the user can do. The following roles exist in a collaboration:

> * **Owner:** Creates the collaboration and determines who has what roles in a collaboration.
> * **Data provider:** Can import data for use by a designated analysis runner.
> * **Analysis runner:** Can run queries in the collaboration by using data offerings provided by designated data providers.

Each collaborator can have multiple roles, and a collaboration can have multiple data providers and analysis runners, but only one owner.

Collaborations can contain many types of *resources*:

* **Template:** A JinjaSQL template that evaluate to a SQL query. Templates can be added to a collaboration by any collaborator, but templates can be run only by analysis runners that the template provider designates.
* **Data offering:** A package of one or more views shared by a data provider with specific analysis runners in that collaboration.

All resources, as well as the collaboration definition itself, are specified using YAML spec files that are registered by collaborators. Collaborators can add or remove resources after the collaboration is created, but roles cannot be changed, or new members invited, after the owner creates the collaboration.

[[\*](#id1)]

Collaboration Data Clean Rooms *do* [offer an RBAC system to manage access](v2-api-reference.html#label-dcr-collaboration-access-management-api) to the API and individual collaborations by means of RBAC privileges granted by the collaborations administrator.

### Requirements and current limitations

* All accounts must have the latest version of Snowflake Data Clean Rooms installed. [Learn how to update your clean rooms environment.](../admin-tasks.html#label-dcr-updating-cleanrooms-environment)
* Owners and data providers must use Snowflake Enterprise Edition. Analysis runners can use Standard Edition.

## System architecture

This section provides a high-level description of how collaboration works in Snowflake Data Clean Rooms.

The following diagram is a simplified representation of a two-party collaboration:

![High-level overview of collaboration with two participants](../../../_images/collab-hub-architecture.svg)

**Notes about the diagram:**

This diagram shows two collaborators that are using the Data Clean Rooms Collaboration API to create and manage a collaboration.

Collaborator A is the owner and creator, as indicated by the collaboration definition YAML in the diagram. Collaborator A is also a data provider, indicated by the data offering share.

Collaborator B is a data provider, as indicated by the data offering share in the diagram.

Both A and B can act as analysis runners, if the collaboration definition allows it.

The Secure Collaboration Orchestrator (SCO) is an account that manages collaborations. The SCO creates an individual app package per collaboration. This app package is an application that all potential collaborators can install (join). All collaborators interact with the collaboration app using the DCR Collaboration API. Costs associated with the SCO are not charged to users.

Collaborators create data offerings, and the SCO shares that data with the collaborators according to the collaboration definition. The SCO uses the collaboration, data offering, template, and analysis specifications to enforce collaboration policies, such as who can access which data by using which templates; what data can be activated, and to whom, and whether free-form SQL access is provided.
