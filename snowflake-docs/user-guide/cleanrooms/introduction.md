---
title: "About Snowflake Data Clean Rooms"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/introduction"
---

# About Snowflake Data Clean Rooms

Feature — Generally Available

Currently available in [these regions](web-app-introduction.html#label-web-app-hosting).

Not available in government and VPS deployments.

A Snowflake Data Clean Room is a native solution to build, connect, and use data clean rooms easily in Snowflake.

Data clean rooms offer a secure way to gain valuable insights while protecting sensitive information. They allow you to combine
and analyze data from different parties with privacy-preserving configurations that help protect the underlying data.

Benefits of data clean rooms include:

* **Enhanced privacy** — Protects sensitive data while enabling collaboration.
* **Deeper insights** — Combines data from multiple sources for richer analysis.
* **Increased security** — Reduces the risk of unauthorized access.

## How Snowflake Data Clean Rooms work

With Snowflake Data Clean Rooms, all analyses are conducted within the secure environment of the clean room. Collaborators are able to
return aggregated results and insights, but cannot directly query the raw data in the clean room. The collaborator who is sharing their data
can define what analyses are available to the other collaborators, allowing them to tightly control how their data is used.

A Snowflake Data Clean Room also uses privacy-enhancing techniques on its data such as differential privacy.

## Clean room collaborators

Snowflake Data Clean Rooms use the concept of a provider and consumer, similar to other Snowflake features like Secure Data Sharing. The
data owner is a *provider* who uses a clean room to safely share data with a *consumer*. The consumer installs the clean room in their own
account and analyzes data in the clean room, including joining their own data with the provider data.

Tasks associated with clean room collaborators include:

Provider:
:   * Create a clean room.
    * Add data to a clean room.
    * Configure a clean room to control how a consumer can interact with data.
    * Share a clean room with a consumer.

Consumer:
:   * Install a clean room.
    * Add datasets to the clean room.
    * Analyze data in the clean room, including joining consumer data with the provider’s data.

Within the clean room environment installed in a Snowflake account, a collaborator can be the provider of one clean room while acting as
the consumer of another.

If you want to collaborate with someone who is not currently a Snowflake customer, see [Clean room managed accounts](managed-accounts).

For information about adding a collaborator to your clean room environment, see [Manage clean room collaborators](manage-dcr-users.html#label-cleanrooms-get-started-add-collaborators).

## Working with Snowflake Data Clean Rooms

There are two ways to interact with clean rooms:

* **Clean rooms UI** — An easy-to-use interface that makes privacy-enhanced data collaboration accessible to a wide base of users,
  including non-technical business users. Collaborators can use pre-defined analysis templates. For an overview, see [Clean rooms UI overview](web-app-introduction).
* **Developer APIs** — A complete set of APIs that allow a technical audience to work with clean rooms programmatically, including the
  ability to build custom applications and to customize analysis templates and ML models. For an overview, see
  [Snowflake Data Clean Rooms developer’s guide](developer-introduction).

New!

Snowflake Data Clean Room has a new multi-collaborator experience in public preview. [Read about and try out our Collaboration clean rooms experience.](v2/about)

## Next Steps

Your next steps depend on how you got here and what you want to do:

**If you got an invitation to join a clean room:**

1. Open the clean room in your browser:

   1. Select Log in in your invitation email to sign in to your clean rooms account.
   2. Sign in with the email address that got the email.
   3. If you get an account chooser, refer to the email to see which account this clean room is in.
   4. When the clean rooms UI opens, navigate to Clean Rooms > Invited.
   5. Choose a clean room and select Join to install the clean room.
   6. To learn how to configure and run a clean room in your browser, see [Install (join) a clean room](manage-clean-rooms.html#label-cleanrooms-web-app-install).
2. Read the [getting started page](getting-started) for background. Remember that the clean rooms
   environment is already installed for your account.
3. If you plan to use only the clean rooms UI, [read about installing and using clean rooms](manage-clean-rooms.html#label-cleanrooms-web-app-install).
   You must install new clean rooms in your account in order to use them; the clean room environment itself is already installed.

**If you are a Snowflake administrator and are interested in installing the clean room environment in your Snowflake account:**

1. Read the [getting started page](getting-started) for background and installation prerequisites.
2. Understand the [costs associated with clean rooms](cleanroom-cost).
3. [Install the clean room environment in your account.](installing-dcr)

**If you are a developer:**

1. If clean rooms are already installed in your Snowflake account, read [getting started page](getting-started) for background.
2. Read the [developer API overview](developer-introduction) and [try out the API tutorial](tutorials/cleanroom-api-tutorial-basic).
