---
title: "Update billing contact information"
url: "https://docs.snowflake.com/en/user-guide/billing-contacts"
---

# Update billing contact information

[Converting to a paid account](admin-trial-account.html#label-trial-account-convert) allows you to see the billing contact information for your account in Snowsight.
On-demand, self-service (ODSS) [organization administrators](organization-administrators) can edit billing information
shown on Billing communication. Non-ODSS customers see their billing information with READ-ONLY access.

Customers with Marketplace accounts (Marketplace customers) have limited ability to edit their billing information on the Contacts tab. Only
ODSS Marketplace customers (who have the ORGADMIN role) can edit the information shown in Snowflake Marketplace billing communication
cards. More specifically, ODSS Marketplace **providers** cannot update their billing name and address fields using Snowsight. ODSS
Marketplace **consumers** can only update the Country field, by selecting one from the list of [Supported consumer locations](../collaboration/consumer-listings-paying.html#label-monetization-consumer-region-support). Non-ODSS Marketplace
customers see their billing information with READ-ONLY access.

Customers with at least one active Snowflake order form (Capacity customers) can see the information on Snowflake Marketplace billing
communication with READ-ONLY access.

## Edit billing contact information

To edit your Snowflake billing contact information:

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select Admin » Billing.
3. On the Billing and Payments page, select the Contacts tab.
4. Find the field that you want to edit, and select [![Pencil icon](../_images/pencil-icon.svg)](../_images/pencil-icon.svg).
5. Type your updated information.

   If you are updating email addresses, you can update multiple email addresses at the same time. The list can be comma-separated or
   space-separated. For example, `updated-usage@snowflake.com, existing-usage@snowflake.com`.

   Tip

   Snowsight might suggest information that matches existing addresses when you update address information. You can accept (select)
   or ignore such suggestions.
6. Update other fields, as necessary.
7. Select Save to save all updates.

### Receiving notification of changes

Snowflake notifies customers who update billing information about the change by sending an email message to the current email addresses
shown in the Usage emails and Invoices emails fields on the Billing communication card. If a customer changes the email address
in a specific email address field, Snowflake sends an email message to both the previous and current email addresses entered in that field.

Snowflake sends notification emails to customers about successful and unsuccessful update attempts. Unsuccessful update attempts also
generate a banner in Snowsight, which remains visible for up to 35 days or until the next update attempt.

## Access control requirements

You must have been granted the ORGADMIN role to edit information on the Snowflake billing communication card.

## Usage notes

* Updates to billing contact information might take several minutes to process.
* Pending updates appear as In-progress in Snowsight.
