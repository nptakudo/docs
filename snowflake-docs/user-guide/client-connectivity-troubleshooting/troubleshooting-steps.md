---
title: "Troubleshooting steps"
url: "https://docs.snowflake.com/en/user-guide/client-connectivity-troubleshooting/troubleshooting-steps"
---

# Troubleshooting steps

This topic provides additional steps you can take to troubleshoot connectivity issues when the [common issues resolutions](common-issues) are not successful. If these resolutions don’t work, you can try the following steps, *in order*, until the issue is resolved:

1. [Use Snowflake troubleshooting tools](snowflake-tools).
2. [Follow alternative troubleshooting steps](alternate-steps).
3. [Follow-up actions](followup-actions).

## Prerequisites

* Ensure that all tests are performed directly on the system experiencing connectivity issues. For example, if the issue is occurring in the Tableau server, perform the following troubleshooting steps in that system and not on your own workstation.
* Admin access might be required on systems with connectivity issues, such as a Tableau server.
* Verification of client connectivity before any scheduled production release or network change is recommended to prevent unexpected downtime due to a client’s inability to access one or more of the required endpoints.
