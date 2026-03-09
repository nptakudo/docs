---
title: "Reference"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/snowflake-intelligence/reference"
---

# Reference

This page provides reference information about working with Snowflake Intelligence. It covers the following concepts:

* REST API endpoints and SQL commands available for creating, managing, and interacting with Cortex Agents
* The supported AI models and their regional availability
* Legal notices about model usage and data classification

## SQL commands and API reference

Snowflake offers the following programmatic methods to create and interact with Cortex Agents for use with Snowflake Intelligence:

* [Cortex Agent Object REST API](../cortex-agents-rest-api)
* [Agents SQL commands](../../../sql-reference/commands-cortex-agent)

### Cortex Agent Object REST API

The Cortex Agent Object REST API provides a comprehensive set of endpoints for building AI-powered applications that integrate with Snowflake Intelligence. You can use the REST API to manage agent objects and orchestrate interactions with your data.

#### Agent management operations

The Agent Object REST API supports the following operations for managing Cortex Agent objects:

| Operation | Endpoint | Description |
| --- | --- | --- |
| Create | `POST /api/v2/databases/{database}/schemas/{schema}/agents` | Creates a new Cortex Agent with specified attributes and tool configuration. |
| Describe | `GET /api/v2/databases/{database}/schemas/{schema}/agents/{name}` | Retrieves the configuration and metadata for an existing agent. |
| Update | `PUT /api/v2/databases/{database}/schemas/{schema}/agents/{name}` | Modifies an existing agent’s configuration, tools, or instructions. |
| List | `GET /api/v2/databases/{database}/schemas/{schema}/agents` | Lists all agents in a specified database and schema, with filtering options. |
| Delete | `DELETE /api/v2/databases/{database}/schemas/{schema}/agents/{name}` | Removes an agent from your account. |

See moreShow less

Expand

For detailed API specifications including request parameters, response schemas, and examples, see [Cortex Agents REST API](../cortex-agents-rest-api).

### Agents SQL commands

Snowflake provides SQL commands to create and manage [Cortex Agents](../cortex-agents) objects for use with Snowflake Intelligence.

#### Agent management commands

The following SQL commands are available for managing Cortex Agents:

| Command | Description |
| --- | --- |
| [CREATE AGENT](../../../sql-reference/sql/create-agent) | Creates a new Cortex Agent or replaces an existing one with specified attributes, profile settings, and a YAML specification. |
| [DESCRIBE AGENT](../../../sql-reference/sql/desc-agent) | Retrieves the complete configuration, specification, and metadata for an existing agent. |
| [SHOW AGENTS](../../../sql-reference/sql/show-agents) | Lists all Cortex Agents for which you have access privileges, with filtering and pagination options. |
| [DROP AGENT](../../../sql-reference/sql/drop-agent) | Removes an agent from the current or specified schema. |

See moreShow less

Expand

For detailed SQL syntax, parameters, and examples, see [Cortex Agent commands](../../../sql-reference/commands-cortex-agent).

## Supported models and regions

Snowflake Intelligence supports models listed in [Models](../cortex-agents.html#label-cortex-agents-models). You can use these models as long as the account has access to them. For more information, see [Control model access](../aisql.html#label-cortex-llm-access-control).

When creating an agent, we recommend selecting Auto for the model. This lets Snowflake Intelligence automatically select the highest quality model for your account and automatically improves as new models become available.

While the listed models may not be available in [all regions](../aisql.html#label-cortex-llm-availability), you can use Snowflake Intelligence in any cloud or region by using Cortex Cross-region inference. This includes clouds and regions where the models are not available. For more information about configuring Cortex Cross-region inference, see [Cross-region inference](../cross-region-inference).

* **AWS US** - In AWS, Claude 4+ offers the highest quality and best speed performance. We recommend that you set up Cortex Cross-region inference for `aws_us` to use Claude 4 and get the best performance. Without Cortex Cross-region inference, you are restricted to using Claude 3.5 in `aws_us`.
* **Azure US** - If you are using Snowflake Intelligence in East US, you can use GPT 4.1+ without Cortex Cross-region inference. Other region and model combinations require Cortex Cross-region inference setup for `azure_us`.
* **AWS EU** - You can use Claude 4+ in this region as long as you configure Cortex Cross-region inference for `aws_eu`.
* **AWS APJ** - You can use Claude 4+ in this region as long as you configure Cortex Cross-region inference for `aws_apj`.

## Legal notices

Where your configuration of Snowflake Intelligence uses a model provided on the
[Model and Service Flow-down Terms](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/ai-features/open-source-model-flow-down-terms/),
your use of that model is further subject to the terms for that model on that page.

The data classification of inputs and outputs are as set forth in the following table.

| Input data classification | Output data classification | Designation |
| --- | --- | --- |
| Usage Data | Customer Data | Covered AI Features [[1]](#id2) |

See moreShow less

Expand

[[1](#id1)]

Represents the defined term used in the AI Terms and Acceptable Use Policy.

For additional information, refer to [Snowflake AI and ML](../../../guides-overview-ai-features).
