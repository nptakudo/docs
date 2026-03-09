---
title: "Installing the Snowflake Data Clean Rooms environment"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/installing-dcr"
---

# Installing the Snowflake Data Clean Rooms environment

Feature — Generally Available

Currently available in [these regions](web-app-introduction.html#label-web-app-hosting).

Not available in government and VPS deployments.

## Before you begin

* If the Snowflake Data Clean Room environment is not installed for your account, follow the installation instructions on this page.
* If you have received an email invitation to join a clean room, follow the link to open the clean rooms UI, where you can join and
  use a clean room. Note that you must provide the email address where you received the invitation.
* If the clean rooms environment is installed for your account, and you want access to it, ask a clean rooms administrator for
  access to the API, the UI, or both.

## Overview

Snowflake Data Clean Rooms can be used in two different environments:

* **The clean rooms UI:** A graphical, no-code, browser-based environment that makes it easy to create and run analyses.
* **The clean rooms API:** A set of stored procedures you can use to create and manage clean rooms and run analyses.

These environments provide similar, but [not exactly equivalent](getting-started.html#label-dcr-web-app-api-comparison), capabilities. A clean rooms administrator installs one or both components in a Snowflake account, and can then grant users access to each environment individually.

## Requirements to install Snowflake Data Clean Rooms

### Account, installer, and user requirements

When you install the clean rooms environment, you install it for all potential users in the Snowflake account. However, access to the clean
rooms environment must be granted to users explicitly by a clean rooms administrator.

Here are the requirements to install Snowflake Data Clean Rooms in your Snowflake account:

* **The account must be the required** [Snowflake Edition](../intro-editions):

  + **To create clean rooms**, you must have Enterprise Edition or higher.
  + **To join and use a clean room** created in another account, you must have Standard Edition or higher.
* **The installer must fulfill** [these role requirements](#label-cleanroom-web-app-prereq-installer).
* **Reader accounts are not supported,** because reader accounts do not allow the data sharing required to install and run the clean rooms
  application.
* **The account must allow** [key-pair authentication](admin-tasks.html#label-cleanrooms-get-started-key-pair), which is used by the service account
  for authentication.
* **You must accept data sharing terms.** If you have not accepted the
  [Snowflake Customer-Controlled Data Sharing Functionality Terms](//www.snowflake.com/legal/data-sharing-terms/), please contact
  [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support). Snowflake Data Clean Rooms leverage [listings](../../collaboration/collaboration-listings-about), which are part
  of the Snowflake Service and subject to your Service terms with Snowflake, including the Snowflake Customer-Controlled Data Sharing
  Functionality Terms and
  [Snowflake Acceptable Use Policy](//www.snowflake.com/legal/acceptable-use-policy/).
* **You must unset any unsupported account-level parameters.** See the [list of unsupported account-level settings](#label-dcr-unsupported-parameters).
* (*Clean rooms UI only*) **The Snowflake account must be a capacity account:** this is an account that has an up-front
  capacity commitment. Snowflake On-Demand accounts cannot access the clean rooms UI.
* (*Clean rooms UI only*) **You must use multi-factor authentication** (MFA) with a
  [supported authenticator app](web-app-introduction.html#label-cleanroom-web-app-mfa).

If you do not meet all these requirements and need to upgrade, contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

### Unsupported account-level parameters

Snowflake Data Clean Rooms does not support certain account-level parameter values. The following table shows the required values for these parameters:

| Parameter name | Required value | Notes |
| --- | --- | --- |
| DEFAULT\_DDL\_COLLATION | *No values supported, must be null* | [Account-level collation](../../sql-reference/collation) is not supported. |
| QUOTED\_IDENTIFIERS\_IGNORE\_CASE | `false` |  |

See moreShow less

Expand

To check a parameter in your account, run the following SQL command, substituting the parameter name for `<parameter_name>`:

CopyExpand

```
SHOW PARAMETERS LIKE '<parameter_name>' IN ACCOUNT;
```

Show lessSee more

Scroll to top

For example:

CopyExpand

```
SHOW PARAMETERS LIKE 'DEFAULT_DDL_COLLATION' IN ACCOUNT;
```

Show lessSee more

Scroll to top

### Role requirements

Here are the role requirements for the person installing the clean rooms environment:

* You must have an ACCOUNTADMIN role in a Snowflake account in order to install the clean rooms environment in that account.
* The user with the ACCOUNTADMIN role must have a valid first name, last name, and email defined for their user object. To check,
  run [DESCRIBE USER](../../sql-reference/sql/desc-user).

## Install the Snowflake Data Clean Rooms environment

Follow these steps to install the clean rooms environment in your Snowflake account.

You must always install the native app (step 1), but after that you can enable the clean rooms API
for code usage (step 2), the clean rooms UI for browser usage (step 3), or both. We recommend installing both the UI and the API to support
both coders and non-coders in your organization.

### 1. Install the native application

Install the native application from the marketplace:

> 1. Set your current role to ACCOUNTADMIN
> 2. Install the [Snowflake Data Clean Rooms application](https://app.snowflake.com/marketplace/listing/GZSTZTP0KKO/snowflake-snowflake-data-clean-rooms)
>    from the Snowflake Marketplace
> 3. Select Get and accept the default options.

Installation takes several minutes. When done, proceed to step 2.

### 2. Install the clean rooms API

The clean rooms API is required to use clean rooms either through the UI or the API.

Here are the steps to install the clean rooms API in your Snowflake account:

1. After installing the native application, launch it in Snowflake. In the navigation menu, select Catalog » Apps »
   Snowflake Data Clean Rooms » Launch app. This opens a worksheet with SQL commands.
2. Run the SQL commands to install the clean rooms API, with the following notes:

   * If you renamed the native application during installation you will need to modify the script as indicated in the script comments.
   * If you want to review the full installation script before running it, uncomment the `DRY_RUN=TRUE` script line and run all commands
     up to and including that line to see the script contents. Note that you should **not run the installation script** exposed by that
     command manually, as it might result in an incomplete installation.
   * Note that installation takes several minutes.
3. Confirm that you can access the API:

   CopyExpand

   ```
   USE ROLE SAMOOHA_APP_ROLE;
   USE WAREHOUSE app_wh;
   CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.LIBRARY.CHECK_MOUNT_STATUS();
   ```

   Show lessSee more

   Scroll to top

   If this returns FALSE, see the troubleshooting section below.
4. Grant API access to other users in your Snowflake account:

   * To add users with **full abilities** to create and manage clean rooms, run `GRANT ROLE SAMOOHA_APP_ROLE to USER USER`
   * To add users with **consumer-run permissions only**, create one or more Snowflake roles and grant users access to this role. Then
     grant run access to specific clean rooms by calling `consumer.grant_run_on_cleanrooms_to_role`.
5. (*Optional*) Install the clean rooms UI to enable no-code access to your clean rooms or other features such as scheduled queries.

### 3. Enable the clean rooms UI

The clean rooms UI provides an easy no-code environment to manage your clean rooms account and create clean rooms and run analyses. It also
provides some [additional functionality](getting-started.html#label-dcr-web-app-api-comparison) not available in the clean rooms API, such as scheduled
queries, third-party activation, and useful predefined templates.

Here is how to enable the clean rooms UI in your Snowflake account:

1. **Configure your network policies** to
   [allow the clean rooms UI to access your Snowflake account](admin-tasks.html#label-cleanrooms-configure-network-policy).
   (*Required only if your Snowflake account uses a network policy to control network traffic.*)
2. **Complete the UI setup.** This step configures a service user [[\*]](#id2) that the clean rooms UI uses to communicate with Snowflake.

   1. [Sign in to the clean rooms UI](https://cleanroom.c1.us-east-1.aws.app.snowflake.com/) with your Snowflake credentials.
   2. Open Admin » Snowflake Admin » Connect to Snowflake account.
   3. Under Enable the Data Clean Rooms UI choose Quick Setup or Manual Setup:

      * Quick Setup - This creates a service user for you. Specify a unique service user name for this account.
      * Manual Setup - If you want to create the service user yourself, or reuse an existing service user, select this option. Note
        that clean rooms will take control of the service user and modify it, so make sure that the service user isn’t used for
        anything else. [Learn how to create a service user](#label-dcr-create-service-user-manually).
   4. Enter your unique service user name and select Finish.
3. **Configure your clean rooms environment** as described in the next section.

[[\*](#id1)]

The service user is configured as follows: Clean rooms sets the type as SERVICE, creates and applies the required network policy
(named SAMOOHA\_SERVICE\_ACCOUNT\_USER\_ACCESS) for the service user, sets the authentication as key-pair, and grants SAMOOHA\_APP\_ROLE to
the service user.

## Configure the clean rooms environment

After you have installed the native application, API, and UI, you should configure the environment and add users:

* [Add clean rooms UI administrators and users.](manage-dcr-users.html#label-dcr-manage-ui-users) Administrators manage the environment on a day-to-day
  basis and can perform many actions, such as managing collaborators and configuring third-party connectors. Users can create or join
  clean rooms and run analyses.
* [Add developers.](manage-dcr-users.html#label-cleanrooms-add-developers) Grant API access to developers in your Snowflake account so they can create
  or consume clean rooms in your account.
* [Enable Cross-Cloud Auto-Fulfillment.](enabling-laf) By default, clean rooms can be shared only with
  consumers in the same underlying cloud region as the clean room creator. If you want to let providers share clean rooms with
  consumers in a different cloud region, you must enable Cross-Cloud Auto-Fulfillment for your account.
* [Register datasets available in the UI.](register-data) A collaborator using the clean rooms UI can
  import only data that has been pre-registered by a clean rooms UI administrator into their clean room.
* [Enable automatic clean room version updates.](admin-tasks.html#label-dcr-updating-cleanrooms-environment) Enable the clean rooms API environment to
  be updated automatically whenever Snowflake releases a new version. You can also install updates manually, but we recommend enabling
  automatic updates.

## Troubleshooting installation

Use this section to troubleshoot problems you might have after completing the steps in this topic.

Symptom: Insufficient privileges
:   **Solution:**
    Ensure that the IP addresses associated with the clean rooms UI are allowed by your network policies. For a list of these IP addresses,
    see [Clean rooms UI hosting locations and IP addresses](web-app-introduction.html#label-web-app-hosting).

Symptom: Installation is successful, but the clean rooms UI is not functioning properly.
:   **Solution #1:**
    Use the [DESCRIBE USER](../../sql-reference/sql/desc-user) command to double-check that the Snowflake user that you used to configure Snowflake has a
    valid first name, last name, and email. If the user is missing any of these, execute the [ALTER USER](../../sql-reference/sql/alter-user) command to
    specify them.

    **Solution #2:**
    Try uninstalling the Snowflake Native App for Snowflake Data Clean Rooms, and then re-installing it.

    * To uninstall the app, see
      [Uninstall a Snowflake Native App](https://other-docs.snowflake.com/en/native-apps/consumer-managing-applications#uninstall-a-native-app).
      If you installed the application with its default name, it is called SAMOOHA\_BY\_SNOWFLAKE.
    * To re-install the app:

      1. [Sign in to the clean rooms UI](web-app-introduction.html#label-cleanroom-web-app-sign-in).
      2. In the left navigation pane, select Snowflake Admin.
      3. Select Login to Snowflake, and authenticate as a Snowflake user with the ACCOUNTADMIN role.
      4. Use the [DESCRIBE USER](../../sql-reference/sql/desc-user) command to confirm that the Snowflake user with the ACCOUNTADMIN role that you just
         used to authenticate has a valid first name, last name, and email. If the user is missing any of these, execute the
         [ALTER USER](../../sql-reference/sql/alter-user) command to specify them.
      5. To install the Snowflake Native App, select Install.
      6. Accept the default name of the application during the installation process.

## Creating a UI service user manually

When installing the clean rooms UI, you can either let the installation create the service user for you, or you can provide a service user
that you create. Here is how to create a service user in Snowsight:

Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in) with your Snowflake administrator credentials and create a user as shown in the following SQL example:

> CopyExpand
>
> ```
> -- Create the user.
> -- Clean rooms will set the type to SERVICE for you.
>
> USE ROLE USERADMIN;
> CREATE USER <SERVICE-USER-USERNAME>;
> ```
>
> Show lessSee more
>
> Scroll to top

Important

Clean rooms alters the authentication controls, network policies, and other attributes of the service user. You will not be able to use
this user yourself after you give it to the clean rooms environment.
