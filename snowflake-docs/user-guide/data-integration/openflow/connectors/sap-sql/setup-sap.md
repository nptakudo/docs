---
title: "SAP® and Snowflake - Setup"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/sap-sql/setup-sap"
---

# SAP® and Snowflake - Setup

Feature — Limited General Availability

SAP® Business Data Cloud Connect for Snowflake is available by request through Snowflake. Please contact your Snowflake account manager to request access.

SAP® Snowflake is available by request through SAP®. Please contact your SAP® account manager to request access.

For more information on these solutions, see [Two Ways to Integrate Snowflake and SAP®](../about-sap-snowflake.html#label-two-ways-to-integrate-snowflake-and-sap).

This topic describes the prerequisite setup tasks for using SAP® Snowflake or SAP® BDC Connect for Snowflake.

* For customers without an existing Snowflake account, see [SAP® Snowflake set up](#label-sap-snowflake-prerequisites).
* For customers with an existing Snowflake account, see [SAP® BDC Connect for Snowflake set up](#label-sap-bdc-prerequisites).

After completing either of these steps, you will need to create a catalog integration in Snowflake to share Data Products from SAP® Business Data Cloud to Snowflake. See [Share Data Products from SAP® Business Data Cloud to Snowflake](share-data-products) for more information.

## SAP® Snowflake set up

This section describes the steps to configure an instance for SAP® Snowflake for SAP customers without an existing Snowflake account.

Note

For Restricted Release, the SAP® Snowflake account provisioned is the Business Critical edition
and must be on AWS in a supported region as described in [regions](../../../../intro-regions).

As an SAP® administrator, perform the following steps:

1. Sign in to [SAP for Me](https://me.sap.com/) with an S-user ID or login name.
2. From the sidebar menu, choose Portfolio & products.
3. In the My Product Packages tab, select the SAP Business Data Cloud product.
4. Select the Applications tab and in the SAP Snowflake card, click Start Provisioning. .
   The Provision SAP® Snowflake wizard dialog displays and guides you through the provisioning process.
5. In the Provision SAP® Snowflake dialog, configure the following parameters and click Next:

   * **Entitlement System**: Displays the ID of the SAP® Business Data Cloud Entitlement set. Cannot be changed.
   * **Name**: Enter an appropriate name for the SAP solution.
   * **Path**: Select or create a resource group under which to group the solution
     components provisioned for SAP® Business Data Cloud.
     Create it in the same location selected for the SAP® Business Data Cloud cockpit system.
   * **Business Type**: Preset to Production.
6. In the Select Application step, SAP Snowflake is pre-selected. .
   The Configure Parameters step displays.
7. In the Configure Parameters step, configure the following parameters and click Next:

   * **Region**: You can provision the SAP Snowflake solution in any region.
     Snowflake recommend choosing the same region as the SAP® Business Data Cloud Cockpit for optimal performance.
   * **Admin email**: Provide the email address of the user to be defined as the administrator of your SAP Snowflake system.
     This user is responsible for adding additional users and for further configuration.
   * **Admin First Name**: The first name of the administrator of your SAP Snowflake system.
   * **Admin Last Name**: The last name of the administrator of your SAP Snowflake system.

   Provisioning begins and SAP® notifies you that a provisioning request was sent to the specified owner’s e-mail address.
8. Click View in Resources to view the tenant within the indicated resource group.
   The Resources tab shows the current solution status, which should be `Processing`.
9. Select the tenant below the new solution and click Details to view the details of the tenant.
10. On top of the details view of the tenant, choose the View Details link.

    A pop-up window opens that provides an activation link to the SAP Snowflake account.
    If you are the SAP Snowflake system owner, select this link and complete the activation flow
    in SAP Snowflake (see [Activating the SAP Snowflake Account](https://accounts.sap.com/saml2/idp/sso/accounts.sap.com)).

    If not, share the activation link with the SAP Snowflake owner and ask them to complete the activation flow.
11. After the account has been activated in SAP for Me, the status for your SAP
    Snowflake solution and tenant changes to `Ready`. In the details view of the SAP
    Snowflake tenant, in the Path field, select the URL to open SAP Snowflake and log in.
    The SAP® DBC admin may provision as many SAP® Snowflake accounts as required with unique account names to
    help distinguish them. Every SAP® Snowflake account will need to be activated per the activation flow.

### Next steps

The SAP® DBC admin may provision as many SAP® Snowflake accounts as they need with unique account names to help distinguish them.
Every SAP® Snowflake account will need to be activated as described in the note below.

After activation, the SAP® Snowflake is ready for you to share Data Products from SAP BDC to SAP® Snowflake as described in Use Cases.
As part of the provisioning process for a new SAP® Snowflake account, a catalog integration called SAP\_BDC\_INTEGRATION is automatically created and enrolled with SAP® Business Data Cloud in the SAP® Snowflake account.
Customers can create additional catalog integrations in the same SAP® Snowflake account
and enroll them with the same or different SAP® Business Data Cloud tenant.
Each catalog integration requires a new Invitation Link that can be obtained from SAP 4 Me.
Each catalog integration requires a new Invitation Link that can be obtained from SAP 4 Me.
Each Invitation Link can be enrolled only once with SAP® Business Data Cloud.

Note

Customers can view the status of provisioning in the Details view.
After provisioning is complete, the customer can click the Snowflake activation link available in the Details view to activate their SAP® Snowflake account, login, change their username and reset their password, setup MFA, and perform other operations.

## SAP® BDC Connect for Snowflake set up

This section describes the steps to set up an SAP® Business Data Cloud connection for use with an existing Snowflake account.

Note

For Restricted Release, the Snowflake account must be Standard, Enterprise, or Business Critical edition and must be on AWS in a supported region as described in [Supported Cloud Regions](../../../../intro-regions).

For more information see, [Provisioning SAP Business Data Cloud Connect](https://help.sap.com/docs/business-data-cloud/administering-sap-business-data-cloud/provision-sap-business-data-cloud-connector-for-supported-external-systems).

As an SAP® administrator, perform the following steps:

1. Obtain your Snowflake account URL and ensure it follows the format below: <https:/>/<orgName>-<accountName>.snowflakecomputing.com.
   Which should be all lower-case and replace \_ (underscore) with - (dash) for RFC compliance.
2. Provision SAP Business Data Cloud Connect as documented here: [Provisioning SAP Business Data Cloud Connect](https://help.sap.com/docs/business-data-cloud/administering-sap-business-data-cloud/provision-sap-business-data-cloud-connector-for-supported-external-systems).
3. Follow steps 1-5 in the wizard
4. In wizard step 6: Configure Parameters:

   * **External System Instance Identifier**: Enter your Snowflake account URL: <https:/>/<orgName>-<accountName>.snowflakecomputing.com
   * **Region**: Use the drop-down menu to choose the region of your Snowflake account. We recommend that your Snowflake account is in the same cloud and region as your SAP Business Data Cloud Core.
5. Complete wizard steps 7 and 8.
6. In step 9: Hover over the View Tenant Notifications button.
   A pop-up window opens with an Invitation Link that can be used to complete the configuration in Snowflake.
7. Copy the Invitation Link
8. Log into your Snowflake account to complete the remainder of the configuration
   to create an SAP BDC connection as described in [Share Data Products from SAP® Business Data Cloud to Snowflake](share-data-products).

### Next steps

In your [SAP for Me](https://me.sap.com/) environment, choose the Customer Landscape tab and, under the Formations tab, choose Include Systems to add the SAP BDC Connect instance to an existing formation.

Customers can create additional catalog integrations in the same Snowflake account and enroll them with the same or
different SAP® Business Data Cloud tenant. Each catalog integration requires a
new Invitation Link that can be obtained from [SAP for Me](https://me.sap.com/).
Each Invitation Link can be enrolled only once with SAP® Business Data Cloud.

Note

To create a new formation, see [Creating SAP Business Data Cloud Formations](https://help.sap.com/docs/business-data-cloud/administering-sap-business-data-cloud/integrate-sap-business-data-cloud-provisioned-systems?locale=en-US&state=PRODUCTION&version=SHIP).
