---
title: "Notifications in Snowflake"
url: "https://docs.snowflake.com/en/user-guide/notifications/about-notifications"
---

# Notifications in Snowflake

You can configure Snowflake to send notifications to a queue provided by a Cloud service (Amazon SNS, Google Cloud PubSub, or
Azure Event Grid), an email address, or a webhook. For details, see the following sections:

* [Sending notifications to cloud provider queues (Amazon SNS, Google Cloud PubSub, and Azure Event Grid)](queue-notifications)
* [Sending email notifications](email-notifications)
* [Sending webhook notifications](webhook-notifications)

## Viewing the history of notifications

To view the history of notifications, call the Information Schema [NOTIFICATION\_HISTORY](../../sql-reference/functions/notification_history) table
function.
