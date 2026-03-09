---
title: "Glossary of Snowflake Data Clean Room terms"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/dcr-glossary"
---

# Glossary of Snowflake Data Clean Room terms

Feature — Generally Available

Currently available in [these regions](web-app-introduction.html#label-web-app-hosting).

Not available in government and VPS deployments.

Get to know these terms as they are used in Snowflake Data Clean Rooms. Some terms are used differently here than
in the rest of Snowflake.

Activate / activation
:   Exporting the results table of a query out of the clean room, either to a collaborator or to a third party. If allowed by the other party
    and the clean rooms settings, you can export query results to your own account or to an approved third-party partner, such as Google Ads
    or Meta Ads Manager.

Clean rooms UI
:   Or “UI” for short. The browser-based web application you can use to manage the Snowflake Clean Room environment, create new clean rooms, or
    use clean rooms to which you have been invited. This used to be called the “web app,” and you might still see that terminology used in
    some places.

Collaborator
:   Typically used as a synonym for consumer, but can also be used to mean any clean room partner, either the provider or the
    consumer.

Column policy
:   Specified by a collaborator to indicate which of their data columns can be projected by other collaborators. A clean room column policy is determined entirely within a clean room, and is not derived from any Snowflake policies that might be applied to the source table outside of the clean room. [Learn more.](policies.html#label-dcr-column-policies)

Connector
:   A plug-in that provides extra functionality to a clean room. Snowflake clean rooms can access a number of connectors to provide
    additional functionality, including clean room connectors (to allow usage of clean rooms created on other platforms), identity connectors
    (which can help identify similar entities in a dataset so they can be used in joins – for example, multiple email addresses that identify
    the same person), and external data connectors (which enable importing data from non-Snowflake sources). The clean room administrator
    determines which connectors are available to clean rooms in that Snowflake account.

Consumer
:   A person or account invited to use a clean room by the clean room provider. Consumers typically import their own data and run one or more
    queries supported by that clean room. However, a clean room can be configured to allow consumers to propose their own query, subject to
    approval by the provider.

Dataset
:   A table or view that has been linked (imported) into a clean room.

Differential privacy
:   An algorithmic and mathematical system that adds protection to individual rows or entities in a dataset by adding noise to numerical
    results and requiring grouping in queries to prevent exact values from being associated with exact rows or entities in the data.

Join policy
:   A policy specified by a clean room collaborator that specifies which of their columns can be joined on in queries in that clean room. A clean room join policy is entirely independent of Snowflake join policies. [Learn more.](policies.html#label-dcr-join-policies)

LAF
:   *Listings auto-fulfillment*, another name for [Cross-Cloud Auto-Fulfillment](enabling-laf), a technology that lets you share a clean room with a collaborator in another cloud hosting region.

Linking
:   Importing a protected view of data into a clean room. The provider and consumer can both link their own data into a clean room to make it
    available to any queries supported by that clean room. Linking a table or view means creating a copy (a view) of the source data within
    the clean room, dynamically linked to the source table or view outside of the clean room.

Provider
:   A clean room creator. The provider typically provides some data and the list of permitted queries that can be run in that clean room, and
    sets high-level clean room configurations.

Template
:   Each clean room has one or more templates, which are SQL queries written in JinjaSQL to allow them to be configurable by the consumer at
    run time. Each template resolves to a single SQL query. Snowflake provides a set of templates in the web app for common actions, but you
    can create your own custom templates using the API.

Secure view
:   When you link a table or view into the clean room, a secure view is created. This is an encrypted view based on the source table or view
    outside the clean room. The secure view is generally invisible to you, but might sometimes appear in an error message or when you are browsing the
    database objects using various tools, where you will see some name mangling from the original linked dataset. Unless directed otherwise,
    always refer to your data using the dataset name, which is identical to the linked source table or view.

Versioning
:   Each clean room has a version that is incremented whenever Python code is imported into the clean room by the provider. Consumers do not
    need to think about versioning. Providers need to remember to update the default clean room version every time they change any Python
    code within a clean room.
