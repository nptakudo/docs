---
title: "Cortex Code"
url: "https://docs.snowflake.com/en/user-guide/cortex-code/cortex-code"
---

# Cortex Code

## Overview

Cortex Code is an AI-driven intelligent agent integrated into the Snowflake platform, optimized for complex data
engineering, analytics, machine learning, and agent-building tasks. It uses an autonomous agent framework to interact
directly with your Snowflake environment, with deep understanding of Snowflake’s Role-Based Access Control (RBAC),
schemas, and best practices.

Cortex Code supports data analysis, machine learning, and data engineering workflows. It provides a consistent, context-aware interface for users
performing data exploration or developing complex data pipelines.

## Core experiences

Cortex Code is delivered through two interfaces: in Snowsight and as a command line interface (CLI) that runs in a local shell.
This availability ensures access to AI agentic experiences wherever you work.

### Cortex Code in Snowsight

[![Snowflake logo in black (no text)](../../_images/logo-snowflake-black.png)](../../_images/logo-snowflake-black.png) [Preview Feature](../../release-notes/preview-features) — Open

Available to all Commercial (non-Gov, VPS, Sovereign) accounts with [Cross-region inference](../snowflake-cortex/cross-region-inference) enabled.

Cortex Code is the persistent, web-based entry point for AI in Snowflake. It is deeply integrated into Workspaces and
Snowsight Admin pages.

Key capabilities:

* **SQL and Python Notebook authoring:** Generate code from natural language or explain and optimize existing queries.
* **Account administration:** Take actions and answer questions about credit consumption, query performance, governance and user permissions.
* **Within Workspaces:**

  + **Context awareness:** Cortex Code knows which SQL file or notebook you are currently viewing and uses that as background context for its answers.
  + **Change review:** A visual “diff view” allows you to review and accept AI-suggested changes before they are applied.

### Cortex Code CLI

Feature — Generally Available

Available to all Commercial (non-Gov, VPS, Sovereign) accounts with [Cross-region inference](../snowflake-cortex/cross-region-inference) enabled.

For power users and developers, the Cortex Code CLI provides an agentic shell for Snowflake that bridges the gap between your local development environment
(for example, VS Code or Cursor) and your Snowflake account.

For details about the CLI experience, see [Cortex Code CLI](cortex-code-cli).

#### Key features of the CLI

* **Snowflake integration:** The CLI connects directly to your Snowflake account using your existing authentication methods. You can execute SQL commands,
  view tables, validate [Cortex Analyst](../snowflake-cortex/cortex-analyst) semantic models, and manage multiple connections.
* **Local file access:** Unlike the Snowsight UI, the CLI can read and write to your local repositories, making
  it ideal for managing `dbt` projects or Streamlit apps.
* **Tool orchestration:** The CLI can invoke local `bash` commands, run `git` operations, and execute SQL directly against your Snowflake warehouse.
* **Agent customization:** Support for `AGENTS.md` files and Agent Skills allows you to define custom behaviors for the agent within
  specific projects.
* **Security:** Full support for Snowflake role-based access control (RBAC), OS-level sandboxing, a three-tier approval
  system, and automatic risk assessment help ensure secure operation within your environment.
* **Built-in Snowflake skills:** Cortex Code includes built-in skills that support key Snowflake workflows such as agent creation, machine
  learning, data engineering, and data governance.
* **Extensibility:** The CLI can be extended with custom tools, skills, subagents, hooks, and profiles to fit your organization’s workflows.
* **Developer friendly:** Developers, data engineers, and data scientists will find the Cortex Code CLI pleasant to work
  with, thanks to features like session persistence, `git` worktree support, a choice of compact and expanded display
  modes, multiple color themes, and support for `vim`-style keyboard navigation.

## More information

For detailed setup instructions, troubleshooting, and advanced use cases, see the following topics:

* [Cortex Code in Snowsight](cortex-code-snowsight)
* [Cortex Code CLI](cortex-code-cli)

## Cost

Cortex Code CLI supports two billing models depending on how you access the product:

* **Subscription:** Individual developers who sign up at [signup.snowflake.com/cortex-code](https://signup.snowflake.com/cortex-code)
  start with a free trial that includes a fixed amount of Cortex Code CLI usage. The trial is valid for 30 days from
  the date of sign-up. After the trial period ends, the account converts to a paid subscription unless cancelled. The
  subscription includes a fixed monthly amount of Cortex Code CLI usage. If you exceed the included usage, Cortex Code
  CLI is unavailable until the next billing period.
* **Pay-as-you-go:** Companies with an existing Snowflake account (on-demand or capacity customers) are billed based
  on token consumption. Pricing details are provided in the [Snowflake Service Consumption Table](https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf).

Any Snowflake compute or storage consumed separately from Cortex Code CLI usage (for example, virtual warehouse or
storage costs) is billed at standard Snowflake on-demand rates, as described in the [Snowflake Service Consumption Table](https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf).

Cortex Code in Snowsight is currently free of charge. You will be notified before any charges are applied
for this feature.

## Legal notices

Where your configuration of Cortex Code uses a model provided on the
[Model and Service Pass-Through Terms](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/ai-features/model-pass-through-terms/),
your use of that model is further subject to the terms for that model on that page.

The data classification of inputs and outputs are as set forth in the following table.

| Input data classification | Output data classification | Designation |
| --- | --- | --- |
| Usage Data | Customer Data | Cortex Code CLI: Covered AI Features. Cortex Code in Snowsight: Preview AI Features. [[1]](#id2) |

See moreShow less

Expand

[[1](#id1)]

Represents the defined term used in the AI Terms and Acceptable Use Policy.

For additional information, refer to [Snowflake AI and ML](../../guides-overview-ai-features).
