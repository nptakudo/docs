---
title: "Snowflake Data Clean Rooms: Activation connectors"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/connector-activation"
---

# Snowflake Data Clean Rooms: Activation connectors

Feature — Generally Available

Currently available in [these regions](web-app-introduction.html#label-web-app-hosting).

Not available in government and VPS deployments.

Note

Snowflake Data Clean Rooms do not currently support data subject consent management. Customers are responsible for ensuring they have
obtained all necessary rights and consents to use the data linked in their clean rooms. Customers must also ensure compliance with all
applicable laws and regulations when using Data Clean Rooms, including in connection with third-party connectors.

You can use connectors to integrate your clean room environment with what your ecosystem partners provides. This topic describes how the
clean room admin can configure a connector so that clean room users can push the result of an analysis to an activation partner.

If you are a provider who wants to control which connectors show up as options when a clean room user runs an analysis, see
[Customize available connectors](admin-tasks.html#label-cleanrooms-admin-customize-connectors).

Important

Third-party connectors are not offered by Snowflake and may be subject to additional terms. These integrations are made available for
your convenience, but you are responsible for any content sent to or received from the integrations.

Customers are responsible for obtaining any necessary consents in connection with their use of Snowflake Data Clean Rooms. Please ensure
that you are complying with applicable laws and regulations when using Snowflake Data Clean Rooms, including in connection with
third-party connectors for activation purposes.

## Google Ads connector

Google Ads is an online advertising platform where advertisers bid to display brief advertisements, service offerings, product listings, or
videos to web users.

Configuration guideUser guide

You must have the MANAGE\_DCR\_CONNECTORS role to configure this connector.

To configure the connector so that your clean environment is integrated with your Google Ads account:

1. In the left navigation of the clean rooms UI, select Connectors.
2. Select the Activation tab.
3. Expand Google Ads.
4. Enter your Google credentials.
5. In the Account ID field, enter the ID associated with your Google Ads account.
6. Specify your API preference. If you select My Developer Token, enter your developer token for the Google Ads API.
7. Select Save.

See the user guide tab to learn how to activate results using this connector.

Here is how to activate analysis results with Google Ads. These instructions assume that this connector has been properly installed
and configured.

To push the results of an analysis to Google Ads for activation:

1. Run an analysis that returns data that can be activated. For example, analyses using the Audience Overlap & Segmentation
   template can be activated.

   For more information about running an analysis, see [Run an analysis as a provider](web-app-working.html#label-web-app-provider-run) or
   [Run an analysis as a consumer](web-app-working.html#label-web-app-working-run-analysis-consumer).
2. In the Results section, select Activate.
3. In the Activation Hub dialog, select Google Ads.
4. In the Account ID field, enter the identifier for the account where you want to push the segment.
5. In the Segment Name field, enter a descriptive name for your results.
6. In the Description field, enter a description of the data you are pushing to Google Ads.
7. In the Activation IDs section, select the columns that contain hashed email and/or hashed phone identifiers.
8. Select Push Data.

## Google Display & Video 360-PAIR connector

Google Display & Video 360-PAIR is an online advertising platform where advertisers bid to display. PAIR gives publishers and advertisers
the option to securely and privately reconcile their first-party data for audiences who have visited both an advertiser’s and a publisher’s
site.

Configuration guideUser guide

You must have the MANAGE\_DCR\_CONNECTORS role to configure this connector.

To integrate your clean room environment with Display & Video 360-PAIR, you must configure this connector as follows:

1. Before configuring the connector,
   [link your account ID in Google Display & Video 360](https://support.google.com/displayvideo/answer/15478755) to
   CMU: Snowflake.
2. In the left navigation of the clean room UI, select Connectors.
3. Select the Activation tab.
4. Expand Google DV360 - PAIR.
5. In Account ID, enter the account ID of your Display & Video 360-PAIR account. Contact [Snowflake support](https://docs.snowflake.com/user-guide/contacting-support) for this ID.
6. In Account Type, select Advertiser or Publisher as appropriate. (The advertiser is the consumer, and the
   publisher is the provider.)
7. If you want Snowflake’s data clean room users to be able to activate results to multiple Display & Video 360-PAIR accounts, select
   + Account and enter the new account ID and account type for each additional account. Contact Snowflake support for the correct
   data partner account ID for your clean room.
8. Select Save.

> Here are the steps the provider (publisher) and consumer (advertiser) must take to activate the results of a the consumer’s analysis
> to the provider’s Display & Video 360-PAIR account.
>
> **General guidelines**

* When using Google Display & Video 360-PAIR, the publisher is the provider, and the advertiser is the consumer.
* The only template supported for this connector is Audience Overlap & Segmentation. Both provider and consumer join a
  single table on a PAIR version of a hashed email or phone.
* Both provider and consumer tables must include email or phone data hashed according to [Google’s PAIR requirements](https://support.google.com/admanager/answer/15067908).
* If 4 million or more distinct rows are linked to the clean room, we recommend using the largest warehouse size (4XL).
* Do not exceed 100 million unique rows in a dataset in a PAIR clean room for analysis and activation.
* When you add Google Display & Video 360-PAIR connector to a clean room, the room allows no other activation connectors.
  The only identity connector allowed in the clean room is Google DV360 - PAIR.
* This connector does not support provider-run analysis or activation.

  **Overview**

  Here is a brief overview of how to use this connector:

  1. The provider creates a clean room that uses the PAIR Display & Video 360 Identity activation and identity connectors, and
     links in a table that contains a hashed email or phone column.
  2. The provider uses the identity connector to generate a PAIR ID column based on the hashed email or hashed phone column in their
     table.
  3. The provider specifies the generated PAIR column as the join column.
  4. The provider specifies the Audience Overlap & Segmentation template (the only one allowed for Display & Video 360-PAIR),
     configures the template, shares, and publishes the clean room.
  5. The consumer joins the clean room, specifies tables, selects the PAIR Display & Video 360 Identity connector and generates
     a PAIR column from their hashed email or phone column.
  6. The consumer joins their PAIR column on the provider’s PAIR column, runs the analysis, and activates the results (the PAIR ID
     column) to Google.
  7. The provider downloads a mapping table that correlates each hashed email or phone values with its equivalent PAIR value. The
     provider sends this table to the Ad Server or the Sell-Side Platform (SSP) to match the PAIR values that the consumer activates to
     Display & Video 360.

  For details, read the Provider or Consumer section below.

  ProviderConsumer

  The provider takes the following steps to use Display & Video 360-PAIR in a clean room:

  1. Configure your Display & Video 360 account to link to Snowflake Data Clean Rooms. For instructions, see
     [Google’s documentation](https://support.google.com/displayvideo/answer/9649053).
  2. Install and configure the Google Display & Video 360-PAIR connector as described in the Configuration guide
     tab.
  3. Create a clean room that encrypts identifying columns with Google PAIR, then share the clean room with the consumer
     (*described below*).
  4. Provide a mapping table of corresponding original and PAIR versions of the join column in the bid request sent to your SSP
     (*described below*).

  **Create and share a clean room**

  > 1. [Sign in to the clean room UI](web-app-introduction.html#label-cleanroom-web-app-sign-in) and create a new clean room.
  > 2. In the Add Data step, select the tables to share with the consumer. Your tables must have email and/or phone number
  >    columns hashed according to Google’s requirements.
  > 3. In the Specify Join Policies step, set the following values:
  >
  >    1. Expand Identity Hub and select PAIR Display & Video 360.
  >    2. In the PAIR Join Columns section, select your hashed email or phone column. The connector generates a
  >       PAIR version of this column with `_PAIR` appended to the original column name:
  >
  >       1. Select Generate Preview to see the new column.
  >       2. Select Add Identity to add the new column to your dataset.
  >    3. In the Join Policies section, select the generated `_PAIR` column. Don’t join on any other columns.
  > 4. In the Configure Analysis & Query section, configure the Audience Overlap & Segmentation template.
  >
  >    + Choose the table containing the hashed email or phone.
  >    + Set any Segmentation & Attribute Columns values you want.
  >    + Under Privacy Settings, keep the Threshold Value at or above 1,000, as required by Google.
  > 5. In the Share Clean Room section, select the consumer as a collaborator and then select Finish to publish and share
  >    your clean room.
  > 6. Retrieve your PAIR ID mapping table as described below. This table was generated in your Snowflake account, and you just
  >    need to know the fully qualified name of this translation table to either download it or [bulk export it](../data-unload-overview). Send
  >    this table with your bid request to your SSP or your ad server.

  **Prepare and send a bid request**

  Send your exported translation table of corresponding original and encrypted hashed email or phone PAIR columns to your SSP. Your SSP uses
  this data to find the corresponding hashed value for the encrypted values sent by the consumer. Best practice is to use a
  URL-safe format such as Base64 encoding when providing these IDs in your bid request.

  The fully-scoped table name has this format:

  CopyExpand

  ```
  SAMOOHA_CLEANROOM_<cleanroom ID>.SHARED_SCHEMA.PROVIDER_<source database>__<source schema>__<source table>_PAIR<digit>
  ```

  Show lessSee more

  Scroll to top

  `cleanroom ID`
  :   This is the clean room ID, *not* the clean room name. You can find the clean room ID for a given clean
      room name by making the following call:

      CopyExpand

      ```
      CALL samooha_by_snowflake_local_db.provider.view_cleanrooms();
      ```

      Show lessSee more

      Scroll to top

  `source database`, `source schema`, `source table`
  :   The database, schema, and name of the source table linked into the clean room used in the template. Note the separation by
      single or double underscores as shown, not by dots.

  `PAIRdigit`
  :   A single digit, usually either 0 or 1.

  Here is an example fully-scoped table name:

  CopyExpand

  ```
  SAMOOHA_CLEANROOM_MY_CLEANROOM.SHARED_SCHEMA.PROVIDER_SAMPLE_DATABASE__AUDIENCE_OVERLAP__CUSTOMERS_PAIR0
  ```

  Show lessSee more

  Scroll to top

  Query the table as shown below, or use [bulk export](../data-unload-overview) to download the data as a flat
  text file to a stage or your computer.

  CopyExpand

  ```
  SELECT * FROM SAMOOHA_CLEANROOM_MY_CLEANROOM.SHARED_SCHEMA.PROVIDER_SAMPLE_DATABASE__AUDIENCE_OVERLAP__CUSTOMERS_PAIR0;
  ```

  Show lessSee more

  Scroll to top

  The consumer takes the following steps to activate PAIR overlap data to Google:

  1. Configure your Display & Video 360 account to link to Snowflake Data Clean Rooms. For instructions, see
     [Google’s documentation](https://support.google.com/displayvideo/answer/9649053).
  2. Install and configure the Google Display & Video 360-PAIR connector as described in the Configuration guide
     tab.
  3. Install the clean room so it is PAIR-enabled (*described below*).
  4. Activate the results of your analysis to your Display & Video 360 account (*described below*).

  **Join and configure the clean room**

  1. [Sign in to the clean room UI](https://cleanroom.c1.us-east-1.aws.app.snowflake.com/) and join the appropriate clean room.
  2. In the Add Data section, select the tables that you want to include in the clean room. Your tables must have email and/or
     phone number columns hashed according to Google’s requirements.
  3. In the Specify Join Policies step, set the following values:

     1. In the PAIR Join Columns section, select your hashed email or phone column. The connector generates a PAIR
        version of this column with \_PAIR appended to the original column name:

        1. Select Generate Preview to see the new columns.
        2. Select Add Identity to add these new columns to your schema. If you repeat this step, it will generate additional identical columns.
     2. In the Join Policies section, match your `_PAIR` columns to the corresponding `_PAIR` columns of the provider,
        and then define any additional join policies.
  4. In the Configure Analysis & Query section, configure the Audience Overlap & Segmentation template.

     + Set the Tables values to your table.
     + Set any Segmentation & Attribute Columns values you want.
  5. Select Finish and run your analysis, as described next.

  **Run the analysis and activate results**

  1. Run the clean room in your Joined tab.
  2. Run the Audience Overlap & Segmentation analysis, filling in any
     information you need for the analysis. Join on the \_PAIR column that you generated.
  3. When the query completes successfully, open the results page and select Activate.
  4. Select Google Display & Video 360 - PAIR.
  5. In the Account ID field, select a Display & Video 360 account.
  6. In the Segment Name field, enter a descriptive name for your results.
  7. In the Description field, enter a description of the data you are sending to Display & Video 360.
  8. In the Publisher Name, enter the name of the provider you are collaborating with.
  9. Select the PAIR ID columns, and the type of these identifiers.
  10. Select Push Data to activate results to Google.

## LiveRamp connector

LiveRamp is a leading connectivity platform leveraged by brands and their partners to deliver products and experiences. LiveRamp RampID
connects people, data, and devices across the digital and physical world, powering people-based marketing and allowing consumers to safely
connect with brands and products.

Note

If you choose to configure the LiveRamp connector so data is uploaded using a Snowflake share, LiveRamp must set up share ingestion
before users can activate using LiveRamp.

Configuration guideUser guide

You must have the MANAGE\_DCR\_CONNECTORS role to configure this connector.

To configure the connector so that your clean environment is integrated with your LiveRamp account:

1. In the left navigation of the clean rooms UI, select Connectors.
2. Select the Activation tab.
3. Expand LiveRamp.
4. Use the Select Upload Type drop-down list to do one of the following:

   * If you want to share data with LiveRamp using SFTP:

     1. Select SFTP.
     2. Enter the username and password provided by LiveRamp for the purpose of using their SFTP.
   * If you want to share data with LiveRamp using Snowflake data sharing:

     1. Select Snowflake Share.
     2. Use the Account drop-down to select the LiveRamp Snowflake account.
     3. Select Generate Share.
     4. Send your LiveRamp representative the name of your account and the generated share.
5. Select Authenticate.

See the user guide tab to learn how to activate results using this connector.

Here is how to activate analysis results with the LiveRamp connector. These instructions assume that this connector has been properly
installed and configured.

To push the results of an analysis to LiveRamp for activation:

1. Run an analysis that returns data that can be activated. For example, analyses using the Audience Overlap & Segmentation
   template can be activated.

   For more information about running an analysis, see [Run an analysis as a provider](web-app-working.html#label-web-app-provider-run) or
   [Run an analysis as a consumer](web-app-working.html#label-web-app-working-run-analysis-consumer).
2. In the Results section, select Activate.
3. In the Activation Hub dialog, select LiveRamp.
4. In the Segment Name field, enter a descriptive name for your results. This name must start with a letter, and can contain
   only letters, numbers, and underscores.

   The string `_SNOWDCR` will be appended to this name.
5. In the RampID drop down, select the column from your table that contains your RampID.
6. Select Push Data.

## Meta Ads Manager connector

Meta Ads Manager is an ad platform that lets you build targeted campaigns and optimize ad spend.

Configuration guideUser guide

You must have the MANAGE\_DCR\_CONNECTORS role to configure this connector.

To configure the connector so that your clean environment is integrated with your Meta Ads Manager account:

1. In the left navigation of the clean rooms UI, select Connectors.
2. Select the Activation tab.
3. Expand Meta Ads Manager.
4. Enter your Meta Business Manager credentials.
5. In the Meta Ads Manager Account ID field, enter the ID of your Meta Ads Manager account.
6. Select Save.

See the user guide tab to learn how to activate results using this connector.

These instructions assume that this connector has been properly installed and configured.

To push the results of an analysis to Meta Ads Manager for activation:

1. Run an analysis that returns data that can be activated. For example, analyses using the Audience Overlap & Segmentation
   template can be activated.

   For more information about running an analysis, see [Run an analysis as a provider](web-app-working.html#label-web-app-provider-run) or
   [Run an analysis as a consumer](web-app-working.html#label-web-app-working-run-analysis-consumer).
2. In the Results section, select Activate.
3. In the Activation Hub dialog, select Meta Ads Manager.
4. In the Account ID field, enter the identifier for the account where you want to push the segment.
5. In the Segment Name field, enter a descriptive name for your results.
6. In the Description field, enter a description of the data that you are pushing.
7. In the Activation IDs section, select the columns that contain identifiers, then select the type of those identifiers.
8. Select Push Data.

## The Trade Desk - CRM connector

The Trade Desk CRM integrates customer relationship management (CRM) data to activate and target audience segments within The Trade Desk’s
platform for personalized advertising campaigns.

Configuration guideUser guide

You must have the MANAGE\_DCR\_CONNECTORS role to configure this connector.

To configure the connector so that your clean room environment is integrated with your account with The Trade Desk - CRM:

1. In the left navigation of the clean rooms UI, select Connectors.
2. Select the Activation tab.
3. Expand The Trade Desk - CRM.
4. In the Username field, enter the username associated with your account with The Trade Desk.
5. In the Password field, enter the password your account with The Trade Desk.
6. In the Advertiser ID field, enter the advertiser ID associated with your account with The Trade Desk.
7. In the Region field, select the region of your The Trade Desk account.
8. Select Authenticate.

See the user guide tab to learn how to activate results using this connector.

These instructions assume that this connector has been properly installed and configured.

To push the results of an analysis to The Trade Desk - CRM for activation:

1. Run an analysis that returns data that can be activated. For example, analyses using the Audience Overlap & Segmentation
   template can be activated.

   For more information about running an analysis, see [Run an analysis as a provider](web-app-working.html#label-web-app-provider-run) or
   [Run an analysis as a consumer](web-app-working.html#label-web-app-working-run-analysis-consumer).
2. In the Results section, select Activate.
3. In the Activation Hub dialog, select The Trade Desk - CRM.
4. In the Segment Name field, enter a descriptive name for your results.
5. In the Activation IDs section, select a column that contains identifiers, then select the type of those identifiers.
6. Select Push Data.

## The Trade Desk - UID 2.0 connector

The Trade Desk - UID 2.0 is a demand-side platform (DSP) that provides a technology platform for advertisers to plan, buy, and manage
digital advertising campaigns across various channels.

Configuration guideUser guide

You must have the MANAGE\_DCR\_CONNECTORS role to configure this connector.

To configure the connector so that your clean room environment is integrated with your account with The Trade Desk - UID 2.0:

1. In the left navigation of the clean rooms UI, select Connectors.
2. Select the Activation tab.
3. Expand The Trade Desk - UID 2.0.
4. In the Advertiser ID field, enter the advertiser ID associated with your account with The Trade Desk.
5. In the Secret Key field, enter the secret key associated with your account with The Trade Desk.
6. Use the Data Center drop-down list to select a The Trade Desk data center.
7. Select Authenticate.

See the user guide tab to learn how to activate results using this connector.

These instructions assume that this connector has been properly installed and configured.

To push the results of an analysis to The Trade Desk - CRM for activation:

1. Run an analysis that returns data that can be activated. For example, analyses using the Audience Overlap & Segmentation
   template can be activated.

   For more information about running an analysis, see [Run an analysis as a provider](web-app-working.html#label-web-app-provider-run) or
   [Run an analysis as a consumer](web-app-working.html#label-web-app-working-run-analysis-consumer).
2. In the Results section, select Activate.
3. In the Activation Hub dialog, select The Trade Desk - UID 2.0.
4. In the Segment Name field, enter a descriptive name for your results.
5. In the Activation IDs section, select a column that contains identifiers, then select the type of those identifiers.
6. Select Push Data.

## Yahoo DSP connector

Yahoo DSP is a demand-side platform that allows advertisers to programmatically buy and optimize digital ad inventory across various
channels.

Configuration guideUser guide

You must have the MANAGE\_DCR\_CONNECTORS role to configure this connector.

To configure the connector so that your clean room environment is integrated with Yahoo DSP:

1. In the left navigation of the clean rooms UI, select Connectors.
2. Select the Activation tab.
3. Expand Yahoo DSP.
4. Enter the MDM ID associated with your Yahoo account.
5. Select Authenticate.

See the user guide tab to learn how to activate results using this connector.

These instructions assume that this connector has been properly installed and configured.

To push the results of an analysis to Yahoo DSP for activation:

1. Run an analysis that returns data that can be activated. For example, analyses using the Audience Overlap & Segmentation
   template can be activated.

   For more information about running an analysis, see [Run an analysis as a provider](web-app-working.html#label-web-app-provider-run) or
   [Run an analysis as a consumer](web-app-working.html#label-web-app-working-run-analysis-consumer).
2. In the Results section, select Activate.
3. In the Activation Hub dialog, select Yahoo DSP.
4. In the Segment Name field, enter a descriptive name for your results.
5. In the Description field, enter a description of the data you are pushing to Yahoo DSP.
6. In the Activation IDs section, select columns that contains identifiers, then select the type of those identifiers.
7. Select Push Data.
