---
title: "Use Snowsight to work with cost anomalies"
url: "https://docs.snowflake.com/en/user-guide/cost-anomalies-ui"
---

# Use Snowsight to work with cost anomalies

This topic describes how to use Snowsight to identify and investigate cost anomalies, which occur when daily consumption in an
account or organization is above or below the expected range of consumption for the day. It also describes how to use Snowsight to
configure notifications so specific users are emailed when cost anomalies occur.

For an overview of cost anomalies, see [Introduction to cost anomalies](cost-anomalies).

## Configure notifications with Snowsight

When Snowflake identifies a cost anomaly, it sends a notification to a list of email addresses. When deciding who will receive notifications for cost anomalies, be aware that email notifications might contain details about how much was spent by an account.

Each account can have a notification list for account-level anomalies within the account. You can also define a separate notification list for the organization to
control who is notified when there is an organization-level anomaly.

Each email address must have been [verified by the user](ui-snowsight-profile.html#label-snowsight-verify-email-address).

You can use a group email address, such as a distribution list, for notifications, but this email address must be verified. Before adding a group email address to the notification list, you might need to create a new Snowflake user with the group email address so you can verify it.

Note

Email notifications are processed through Snowflake’s Amazon Web Services (AWS) deployments, using AWS Simple Email Service
(SES). The content of an email message sent using AWS may be retained by Snowflake for up to thirty days to manage the delivery
of the message. After this period, the message content is deleted.

To add email addresses where notifications are sent when there is a cost anomaly, complete the following steps:

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in) as a user with the [required privileges](cost-anomalies-access-control).
2. In the navigation menu, select Admin » Cost management, and then select Anomalies.
3. Select Notifications.
4. To specify who gets notified for an [account-level anomaly](cost-anomalies.html#label-cost-anomaly-level), do the following:

   1. In the Notify for account anomalies field, enter the email address of a Snowflake user you want contacted for anomalies.
   2. Press Enter.
   3. Repeat for additional users.
5. To specify who gets notified for an [organization-level anomaly](cost-anomalies.html#label-cost-anomaly-level), do the following:

   1. In the Notify for organization anomalies field, enter the email addresses of a Snowflake user you want contacted for anomalies.
   2. Press Enter.
   3. Repeat for additional users.
6. Select Save changes.

## Identify and investigate cost anomalies with Snowsight

**Step 1: Identify cost anomalies**

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in) as a user with the [required privileges](cost-anomalies-access-control).
2. In the navigation menu, select Admin » Cost management, and then select Anomalies.
3. Use the filters to select a timeframe and account. If you want to identify
   [organization-level anomalies](cost-anomalies.html#label-cost-anomaly-level), select All accounts.
4. Do one of the following:

   1. Use the chart to visually track actual consumption and the expected range of consumption over time. Cost anomalies where actual
      consumption went above or below the expected range are visually represented in the chart.
   2. Use the table to view a list of all cost anomalies within the timeframe. Sort as desired.

**Step 2: Investigate a cost anomaly**

1. Select a cost anomaly by clicking the indicator in the chart or selecting a row in the table. A side panel opens.
2. If you are investigating an account-level anomaly (you selected a specific account in the filter), you can use the side panel to
   drill down into the following:

   * Use the Hourly consumption section to view hourly consumption within the account.
   * Use the Top warehouses section to identify the warehouses within the account that had the greatest absolute change in consumption.
   * If you are investigating anomalies in the account that you are currently signed in to, use the Top queries section to identify
     the most expensive queries in the warehouse that had the greatest change in consumption.
     This might not show the most expensive query in the account because it focuses on queries in a specific warehouse (the one with the
     greatest change in consumption).
   * Drill down into the most expensive queries by selecting the Open in Worksheet icon that is located near the Query ID. A
     worksheet opens that shows the query that was executed.
3. If you are investigating an organization-level anomaly (you selected All Accounts in the filter), you can use the side
   panel to drill down into the following:

   * Use the Top accounts section to identify the accounts that had the greatest absolute change in consumption.
   * Use the Top warehouses section to drill down into the account with the greatest change in consumption. You can identify the
     warehouses within the account that had the greatest change in consumption.

     This might not show the warehouse with the greatest change within the entire organization because it focuses on warehouses in a
     specific account (the one with the greatest change in consumption). To programmatically retrieve the top warehouses in a different
     account or within the organization, see [Warehouse-level consumption](cost-anomalies-class.html#label-cost-anomaly-investigate-warehouse-class).

Tip

If the Anomalies tab does not provide the consumption data you need to identify the root cause of the cost anomaly, you can
select the Consumption tab for further investigation.
