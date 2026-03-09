---
title: "Openflow Connector for Salesforce Bulk API: Set up Salesforce"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/salesforce-bulk-api/setup-salesforce"
---

# Openflow Connector for Salesforce Bulk API: Set up Salesforce

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) [Preview Feature](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/preview-terms-of-service/)

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes the steps to set up Salesforce for the Openflow Connector for Salesforce Bulk API.

## Create certificates

You need a private key and public certificate to configure the external client app in Salesforce. You can generate these files using the following commands:

1. Generate the Private Key. You will be asked for a password to secure the private key.

   CopyExpand

   ```
   openssl genpkey -algorithm RSA -out private.key -aes256
   ```

   Show lessSee more

   Scroll to top
2. Create a self-signed certificate from the Private Key.

   CopyExpand

   ```
   openssl req -new -x509 -key private.key -out public.crt -days 365
   ```

   Show lessSee more

   Scroll to top

   You can also generate a Certificate Signing Request (csr) to have a certificate signed by your company CA.

Note

You are responsible for safeguarding and rotating the public key and private key files used for key-pair authentication according to the security policies of your organization.

## Create an external client app in Salesforce

Create an external client app in Salesforce with JWT Bearer Flow.

1. Log in to Salesforce.
2. Go to Setup » Apps » App Manager, and then select New External Client App.
3. Fill in the required fields:

   * External Client App Name: For example, `Openflow connector for Salesforce Bulk API`.
   * Contact Email: For example, `salesforceadmin@mycompany.com`.
4. In the API (Enable OAuth Settings) section, select the Enable OAuth checkbox.
5. Provide a valid Callback URL (for example, `https://www.google.com/`).
6. Provide the desired OAuth Scopes for the application. The following scopes are required for the connector to operate properly:

   * Manage user data via APIs (`api`)
   * Perform requests at any time (`refresh_token`, `offline_access`)
7. In Flow Enablement, select the Enable JWT Bearer Flow checkbox and upload the `public.crt` file created in the previous step.
8. Click Create to complete the application creation process.
9. Go to the Settings tab, expand the OAuth Settings section, and click Consumer Key and Secret to retrieve the credentials of your application.
10. Record the values for the Consumer Key and the Consumer Secret for use when configuring the connector in Snowflake.

## Approve the client app

The client app will be used by the Openflow Connector for Salesforce Bulk API on behalf of a specific configured user. Follow these steps to approve the app for a specific user:

1. Go to the Policies tab of the client application.
2. Click Edit.
3. Expand the OAuth Policies section and change Permitted Users to Admin approved users are pre-authorized.
4. Expand the App Policies section and select the profiles or permission sets you want to use based on what you have assigned to the user you will use with the application in the Snowflake connector.
5. Click Save.

## Next steps

Perform the Snowflake setup tasks:

[Openflow Connector for Salesforce Bulk API: Set up Snowflake](setup-snowflake)
