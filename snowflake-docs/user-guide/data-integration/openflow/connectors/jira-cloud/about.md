---
title: "About Openflow Connector for Jira Cloud"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/jira-cloud/about"
---

# About Openflow Connector for Jira Cloud

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes the basic concepts of Openflow Connector for Jira Cloud, its workflow, and limitations.

The Openflow Connector for Jira Cloud ingests Atlassian Jira issues data to Snowflake. It uses the [Jira Cloud REST API](https://developer.atlassian.com/cloud/jira/platform/rest/v3/intro/#about)
and [Jira Query Language (JQL)](https://support.atlassian.com/jira-service-management-cloud/docs/use-advanced-search-with-jira-query-language-jql/) to retrieve data, which is then stored in a Snowflake table and is accessible via a view.
Data ingestion occurs in two phases:

1. An initial load, where all data is retrieved during the initial API call.
2. Incremental loads, which merge updates and new data into the destination table and use
   timestamps from previous calls to limit the result to the issues that were updated since the last load.

Use this connector if you’re looking to do the following:

* Extract Jira issues and project details for cross‐team visibility and deeper insights

## Workflow

1. A **Jira Cloud administrator** performs the following tasks:

   1. Generates an API token within the Jira instance. This token will be used by the connector for authentication.
      Both tokens with scopes (with `read:jira-work` and `read:jira-user` scopes) and without scopes are supported,
      although tokens with scopes are recommended for better fine-grained access control.
   2. Defines the criteria to search issues, such as project name, created field, and updated field.
2. A **Snowflake account administrator** performs the following tasks:

   1. Installs the connector.
   2. Configures the connector:

      1. Provides the Jira API token.
      2. Specifies the Jira instance URL.
      3. Defines the criteria for the issues being ingested, by providing JQL query or, for simpler cases just the project name.
      4. Sets the database and schema names in the Snowflake account.
   3. Runs the connector flow in the Openflow canvas. Upon execution, the connector performs the following actions:

      1. Creates an API call to fetch issues from the configured Jira instance.
      2. Extracts relevant data, such as issue creation dates, statuses, and assignees.
      3. Creates the configured destination table in the Snowflake database if the API call returned at least one result.
      4. Loads the processed data into the specified Snowflake table.
3. **Snowflake Business users** can then access views, and perform operations on the data downloaded from Jira Cloud to destination tables.

## Limitations

* Each connector instance can be associated with only one JQL search query.
* Timestamps in connector properties reflect the timezone of Jira Cloud, potentially resulting in discrepancies with the user’s local timezone.
  The Jira Cloud timezone is fetched once and kept in the state of the FetchJiraIssues processor. Updating the connector’s timezone requires clearing the state of this processor.
* The connector is unable to reflect deletions in the target Snowflake tables as the Jira Cloud REST API does not return information about data deletion.
* Basic authentication using an email and API token is the only supported authorization method. As a result the connector can only ingest data accessible by the owner of the API token.
* The FetchJiraIssues processor is single threaded, and designed to work on the primary node.

## Next steps

[Set up the Openflow Connector for Jira Cloud](setup)
