---
title: "Using the Trust Center"
url: "https://docs.snowflake.com/en/user-guide/trust-center/using-the-trust-center"
---

# Using the Trust Center

This topic describes how to monitor Trust Center costs, and manage scanners, findings, and security risks by using the Trust Center
Snowsight interface.

## Monitoring cost

The Trust Center incurs [serverless compute cost](../cost-understanding-compute.html#label-serverless-credit-usage) when it scans your Snowflake environment for
security vulnerabilities.

You can use cost-related views in the ACCOUNT\_USAGE and ORGANIZATION\_USAGE schemas to track the costs associated with the Trust Center. When
querying these views, filter on the `service_type` column to find `TRUST_CENTER` values.

| View | Schema | `service_type` | Roles with required privileges |
| --- | --- | --- | --- |
| [METERING\_HISTORY](../../sql-reference/account-usage/metering_history) | ACCOUNT\_USAGE | TRUST\_CENTER | * ACCOUNTADMIN role * USAGE\_VIEWER database role |
| [METERING\_DAILY\_HISTORY](../../sql-reference/account-usage/metering_daily_history) | ACCOUNT\_USAGE | TRUST\_CENTER | * ACCOUNTADMIN role * USAGE\_VIEWER database role |
| [METERING\_DAILY\_HISTORY](../../sql-reference/organization-usage/metering_daily_history) | ORGANIZATION\_USAGE | TRUST\_CENTER | * ORGADMIN role * ORGANIZATION\_USAGE\_VIEWER database role |
| [USAGE\_IN\_CURRENCY\_DAILY](../../sql-reference/organization-usage/usage_in_currency_daily) | ORGANIZATION\_USAGE | TRUST\_CENTER | * ORGADMIN role * ORGANIZATION\_BILLING\_VIEWER database role |

See moreShow less

Expand

**Example:** View the total cost that the Trust Center incurred between December 1, 2024 and December 31, 2024.

CopyExpand

```
SELECT
   SUM(credits_used) AS total_credits
FROM snowflake.account_usage.metering_history
WHERE
   service_type = 'TRUST_CENTER' AND
   start_time >= '2024-12-01' AND
   end_time <= '2024-12-31';
```

Show lessSee more

Scroll to top

**Example:** View the daily cost that the Trust Center incurred after December 1, 2024.

CopyExpand

```
SELECT
   usage_date AS date,
   credits_used AS credits
FROM snowflake.account_usage.metering_daily_history
WHERE
   service_type = 'TRUST_CENTER' AND
   date > '2024-12-01';
```

Show lessSee more

Scroll to top

For information about how many credits are charged per Compute-Hour for the operation of the Trust Center, see Table 5 in the
[Snowflake Service Consumption Table](https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf).

## Use the Trust Center Snowsight interface

This preview introduces several changes to the Trust Center. The Trust Center Snowsight interface now has the following tabs:

* Overview - Displays a high-level summary of Trust Center findings for your account. Select the View option in each section of
  Overview to see more detailed information about a specific aspect of your account’s security posture.
* Violations - This tab was previously named the Findings tab. It shows violations, suggests remediation actions for them, and
  provides detailed information about them. For information about using this tab, go to the Violations tab, and then follow the instructions
  in [Manage the violation findings lifecycle](#label-managing-findings) and [Manage security risks](#label-trust-center-managing-security-risks).
* Detections - This tab shows the detections found by the scanners and provides information about them. For information about using
  this tab, see [View Trust Center detection findings](#label-trust-center-alerts-managing).
* Manage scanners - Now contains the Scanner packages tab. You can use it to view and manage scanner packages and individual
  scanners. The event-driven scanners added in this preview show Event driven in the SCHEDULE column. For information
  about using this tab, go to the Manage scanners tab, and then follow the instructions in [Manage scanner packages](#label-trust-center-managing-scanners)
  and [Managing scanners](#label-trust-center-managing-individual-scanners).
* Manage scanners - Now contains the Extensions tab. You can create Trust Center extensions by using the Snowflake Native App
  Framework. For more information, see, [Using Trust Center extensions](trust-center-extensions).

## Manage scanner packages

You can complete the following tasks to manage scanner packages in the Trust Center:

* [View the list of scanners in a package](#label-trust-center-view-scanner-list)
* [Enable scanner packages](#label-trust-center-enable-scanner-packages).
* [View available scanner packages](#label-trust-center-view-scanner-packages).
* [Change the schedule for a scanner package](#label-trust-center-change-scanner-package-schedule).
* [Run a scanner package on demand](#label-trust-center-start-scanner-package-on-demand).

### View the list of scanners in a package

To view the list of scanners provided in a scanner package, follow these steps:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. Switch to a role with the `SNOWFLAKE.TRUST_CENTER_ADMIN` application role granted to it.

   For more information about granting this role, see [Required roles](overview.html#label-trust-center-requirements).
3. In the navigation menu, select Governance & security » Trust Center.
4. Select the Manage scanners tab.
5. From the list, select a scanner package.

### Enable scanner packages

To enable a scanner package, follow these steps:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. Switch to a role with the `SNOWFLAKE.TRUST_CENTER_ADMIN` application role granted to it.

   For more information about granting this role, see [Required roles](overview.html#label-trust-center-requirements).
3. In the navigation menu, select Governance & security » Trust Center.
4. Select the Manage scanners tab.
5. Select a scanner package from the list.
6. Select Enable Package.

After you enable a scanner package, you can
[enable or disable individual scanners in the scanner package](#label-trust-center-enable-disable-scanner).

### View available scanner packages

To view available scanner packages, follow these steps:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. Switch to a role with the `SNOWFLAKE.TRUST_CENTER_ADMIN` application role granted to it.

   For more information about granting this role, see [Required roles](overview.html#label-trust-center-requirements).
3. In the navigation menu, select Governance & security » Trust Center.
4. Select the Manage scanners tab.
5. Optionally, select Provider, Status, or Search to filter the list of scanner packages available.

### Change the schedule for a scanner package

You can change the schedule for all scanner packages, except the [Security Essentials scanner package](overview.html#label-security-essentials-scanner-package).

Tip

After a scanner package is enabled, you can
[change the schedule for individual scanners in the scanner package](#label-trust-center-change-scanner-schedule).

To change the schedule for a scanner package, follow these steps:

1. Ensure you’ve enabled the [CIS Benchmarks scanner package](overview.html#label-cis-benchmarks-scanner-package).
2. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
3. Switch to a role with the `SNOWFLAKE.TRUST_CENTER_ADMIN` application role granted to it.

   For more information about granting this role, see [Required roles](overview.html#label-trust-center-requirements).
4. In the navigation menu, select Governance & security » Trust Center.
5. Select the Manage scanners tab.
6. Select a scanner package from the list.
7. Select the Settings tab.
8. Under Scanner Package Schedule, select [![trust-center-edit-image](../../_images/ui-trust-center-edit.png)](../../_images/ui-trust-center-edit.png) Edit.
9. Set your desired Frequency.
10. Select Continue.

### Run a scanner package on demand

To run a scanner package on demand, follow these steps:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. Switch to a role with the `SNOWFLAKE.TRUST_CENTER_ADMIN` application role granted to it.

   For more information about granting this role, see [Required roles](overview.html#label-trust-center-requirements).
3. In the navigation menu, select Governance & security » Trust Center.
4. Select the Manage scanners tab.
5. Select a scanner package from the list.
6. Next to Search, select [![trust-center-start-image](../../_images/ui-trust-center-start.png)](../../_images/ui-trust-center-start.png) Run Package.

## Managing scanners

You can complete the following tasks to manage scanners in the Trust Center:

* [View details for a scanner](#label-trust-center-view-scanner-detail-descriptions).
* [Enable or disable a scanner in a scanner package](#label-trust-center-enable-disable-scanner).
* [Change the schedule for a scanner](#label-trust-center-change-scanner-schedule).
* [Reset the schedule for a scanner to the scanner package schedule](#label-trust-center-reset-scanner-schedule).
* [Run a scanner on demand](#label-trust-center-start-scanner-on-demand).

### View details for a scanner

To view details that describe what each scanner does, follow these steps:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. Switch to a role with the `SNOWFLAKE.TRUST_CENTER_ADMIN` application role granted to it.

   For more information about granting this role, see [Required roles](overview.html#label-trust-center-requirements).
3. In the navigation menu, select Governance & security » Trust Center.
4. Select the Manage scanners tab.
5. Select a scanner package from the list.
6. Select a scanner from the list of scanner names.

### Enable or disable a scanner in a scanner package

Attention

Scanners provide valuable information about possible security risks at a minimal cost.
Before disabling a scanner, we recommend evaluating the value of the information provided
by the scanner in relation to the cost associated with running it. For more information about
evaluating the cost associated with a scanner, see [Monitoring cost](#label-trust-center-monitoring-cost).

If a scanner package is disabled, all of the scanners in the package are disabled, including
scanners that were enabled individually.

To enable or disable a scanner in a scanner package, follow these steps:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. Switch to a role with the `SNOWFLAKE.TRUST_CENTER_ADMIN` application role granted to it.

   For more information about granting this role, see [Required roles](overview.html#label-trust-center-requirements).
3. In the navigation menu, select Governance & security » Trust Center.
4. Select the Manage scanners tab.
5. Select a scanner package from the list.
6. In the scanner STATE, enable or disable the scanner.
7. In the confirmation box, select Confirm.

### Change the schedule for a scanner

You can change the schedule for schedule-based scanners. You can’t change the schedule for event-based scanners. You can only
[enable or disable](#label-trust-center-enable-disable-scanner) an event-driven scanner.

Note

When a custom schedule is set for an individual scanner, that setting is used instead of its scanner package schedule,
even if the scanner package schedule is changed.

To change the schedule for a scanner, follow these steps:

1. Ensure that you [enabled the scanner](#label-trust-center-enable-disable-scanner).
2. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
3. Switch to a role with the `SNOWFLAKE.TRUST_CENTER_ADMIN` application role granted to it.

   For more information about granting this role, see [Required roles](overview.html#label-trust-center-requirements).
4. In the navigation menu, select Governance & security » Trust Center.
5. Select the Manage scanners tab.
6. Select a scanner package from the list.
7. Select [![trust-center-vertical-more-image](../../_images/ui-trust-center-vertical-more.png)](../../_images/ui-trust-center-vertical-more.png) More for the scanner, and then select Edit schedule.
8. Set your desired Frequency.
9. Select Save.

### Reset the schedule for a scanner to the scanner package schedule

To change the schedule for a scanner to match its scanner package schedule, follow these steps:

1. Ensure that you [enabled the scanner](#label-trust-center-enable-disable-scanner).
2. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
3. Switch to a role with the `SNOWFLAKE.TRUST_CENTER_ADMIN` application role granted to it.

   For more information about granting this role, see [Required roles](overview.html#label-trust-center-requirements).
4. In the navigation menu, select Governance & security » Trust Center.
5. Select the Manage scanners tab.
6. Select a scanner package from the list.
7. Select [![trust-center-vertical-more-image](../../_images/ui-trust-center-vertical-more.png)](../../_images/ui-trust-center-vertical-more.png) More for the scanner, and then select Edit schedule.
8. Select Reset, and then select Reset to scanner package schedule.
9. Select Save.

### Run a scanner on demand

To run a scanner on demand, follow these steps:

1. Ensure that you [enabled the scanner](#label-trust-center-enable-disable-scanner).
2. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
3. Switch to a role with the `SNOWFLAKE.TRUST_CENTER_ADMIN` application role granted to it.

   For more information about granting this role, see [Required roles](overview.html#label-trust-center-requirements).
4. In the navigation menu, select Governance & security » Trust Center.
5. Select the Manage scanners tab.
6. Select a scanner package from the list.
7. Select [![trust-center-vertical-more-image](../../_images/ui-trust-center-vertical-more.png)](../../_images/ui-trust-center-vertical-more.png) More for the scanner, and then select Run scanner.

## Manage the violation findings lifecycle

Specific application roles allow you to view and manage violation findings by using the Violations tab. For more
information, see [Required roles](overview.html#label-trust-center-requirements).

### View violations

To view and filter your violations data to see your current progress, follow these steps:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. Switch to a role with the `SNOWFLAKE.TRUST_CENTER_VIEWER` application role granted to it.

   For more information about granting this role, see [Required roles](overview.html#label-trust-center-requirements).
3. In the navigation menu, select Governance & security » Trust Center.
4. Select the Violations tab.
5. To view the list of open, resolved, or all violations, select an option from the Status drop-down menu.
6. To see a detailed pane with the violation’s summary, recommendations, and activity, select any violation.
7. In the violation bar, select Activity to see the comments history and the responsible users.
8. To see the scanner’s last run and when the violation was generated, select Scanned.
9. To see when the violation status was last changed, select Updated.

### Change the status of a violation finding

Attention

Marking a violation as `Resolved` is a way to triage the open violation so you can focus on the ones most important for your environment.
Resolving a violation also ceases the periodic email notifications for that violation. Scanners still run as scheduled irrespective of the
violation status: `Open` or `Resolved`. The scanner continues to run and detect violations if the configuration remains unchanged.

All new security violations are raised with an `Open` status. You can resolve a violation for multiple reasons, such as not being applicable
to your account, being deferred for a future date, being in progress already, or another reason.

You can change the status of a violation for any reason, such as not being applicable to your account, deferred for a future date, being in
progress already, or another reason. To change the status of a violation, follow these steps:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. Switch to a role with the `SNOWFLAKE.TRUST_CENTER_ADMIN` application role granted to it.

   For more information about granting this role, see [Required roles](overview.html#label-trust-center-requirements).
3. In the navigation menu, select Governance & security » Trust Center.
4. Select the Violations tab.
5. Select a violation that opens its detailed pane. By default, only violations with the Open status are shown.
6. Select the Resolve button.
7. (Optional) To justify the resolution, add a comment.
8. Select Submit.

You can reopen a resolved violation by selecting the Resolve button.

Note

Manually resolving a violation finding isn’t mandatory for customers. The Trust Center automatically removes violation findings from the
Violations tab when a scanner run determines that any misconfiguration was corrected or remediation steps were
followed correctly.

## View Trust Center detection findings

[![Snowflake logo in black (no text)](../../_images/logo-snowflake-black.png)](../../_images/logo-snowflake-black.png) [Preview Feature](../../release-notes/preview-features) — Open

Available to all accounts.

The Detections tab displays information about the detection findings reported by the Trust Center and lets
you examine them:

Note

Currently, you can’t manage the lifecycle — that is, resolve or reopen — a detection finding. Detection findings aren’t currently aggregated
into the Organization account.

### View detections

To view detections, follow these steps:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. Switch to a role that has either the `SNOWFLAKE.TRUST_CENTER_ADMIN` application role or the `SNOWFLAKE.TRUST_CENTER_VIEWER`
   application role granted to it.

   For more information about granting these roles, see [Required roles](overview.html#label-trust-center-requirements).
3. In the navigation menu, select Governance & security » Trust Center.
4. Select the Detections tab.

   A chart displays information about detections in the specified time period. You can adjust filters
   to modify the detections displayed on the tab. See the next step for information about modifying filters.

   The detections bar displays information about each detection, such as the detection type, entity type,
   entity name, and additional information.
5. To analyze the detections displayed on the tab, adjust the filters:

   * Detection Type - Clear the filter to show detections of any type, or select a type to show only detections of that type; for
     example, Abnormal Account Activities, Insecure Login, or Privilege Escalation.
   * Severity - Clear the filter to show detections of any severity, or select a severity to show only detections of that severity;
     for example, Critical, High, Medium, or Low.
   * Entity Type - Clear the filter to show detections for any entity type, or select an entity type to show only detections for that
     entity type; for example, QUERY, ROLE, or USER.
   * Reported By - Clear the filter to show detections reported by all scanners in the **Security Essentials** and **Threat Intelligence**
     scanner packages, or select a scanner package to only show detections reported by scanners in that scanner package.
   * Time Range - Clear the filter to show all detections that were reported at any time or select a time range to view detections
     reported in the selected time range.
6. To see a detailed pane with the detection’s summary, remediation recommendations, and activity, select any detection.

   To open a worksheet with queries that you can run to get more information on the scanner output,
   on the Remediation tab, select Open a Worksheet.

## Manage security risks

You can complete the following tasks to manage security risks in the Trust Center:

* [View security risks](#label-trust-center-view-security-risks).
* [Remediate security risks](#label-trust-center-resolve-violations).

### View security risks

To view security risks, follow these steps:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. Switch to a role with the `SNOWFLAKE.TRUST_CENTER_VIEWER` or `SNOWFLAKE.TRUST_CENTER_ADMIN`
   application role granted to it.

   For more information about granting these roles, see [Required roles](overview.html#label-trust-center-requirements).
3. In the navigation menu, select Governance & security » Trust Center.
4. Select the Violations tab.
5. Select a recommendation from the list of violations to view details about the violation associated with the recommendation.
6. Optionally, select Severity, Violations, or Search to filter the list of recommendations shown.

### Remediate security risks

When [viewing individual security risks](#label-trust-center-view-security-risks), you can learn how to remediate the risks associated
with the recommendations that display, allowing you to harden the security of your account.

To remediate security risks, follow these steps:

1. Sign in to [Snowsight](../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. Switch to a role that has the `SNOWFLAKE.TRUST_CENTER_ADMIN` application role granted to it.

   For more information about granting these roles, see [Required roles](overview.html#label-trust-center-requirements).
3. In the navigation menu, select Governance & security » Trust Center.
4. Select the Violations tab.
5. From the list of violations, select a recommendation.
6. In the Remediation tab, follow the steps that are shown.
