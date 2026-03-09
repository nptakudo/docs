---
title: "Snowflake-managed MCP server"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-agents-mcp"
---

# Snowflake-managed MCP server

Feature — Generally Available

Not supported in government regions.

## Overview

Note

Snowflake supports Model Context Protocol revision `2025-06-18`.

Model Context Protocol (MCP), is an [open-source standard](https://modelcontextprotocol.io/docs/getting-started/intro) that lets AI agents securely interact with business applications and external data systems, such as databases and content repositories. MCP lets enterprise businesses reduce integration challenges and quickly deliver outcomes from models. Since its launch, MCP has become foundational for agentic applications, providing a consistent and secure mechanism for invoking tools and retrieving data.

The Snowflake-managed MCP server lets AI agents securely retrieve data from Snowflake accounts without needing to deploy separate infrastructure. You can configure the MCP server to serve Cortex Analyst, Cortex Search, and Cortex Agents as tools, along with custom tools and SQL executions on the standards-based interface. MCP clients discover and invoke these tools, and retrieve data required for the application. With managed MCP servers on Snowflake, you can build scalable enterprise-grade applications while maintaining access and privacy controls. The MCP server on Snowflake provides:

* **Standardized integration:** Unified interface for tool discovery and invocation, in compliance with the rapidly evolving standards.
* **Comprehensive authentication:** Snowflake’s built-in OAuth service to enable OAuth-based authentication for MCP integrations.
* **Robust governance:** Role based access control (RBAC) for the MCP server and tools to manage tool discovery and invocation.

For information about the MCP lifecycle, see [Lifecycle](https://modelcontextprotocol.io/specification/2025-06-18/basic/lifecycle). For an example of an MCP implementation, see the [Getting Started with Managed Snowflake MCP Server](https://quickstarts.snowflake.com/guide/getting-started-with-snowflake-mcp-server/index.html) Quickstart.

## MCP server security recommendations

Important

When you configure hostnames for MCP server connections, use hyphens (`-`) instead of underscores (`_`). MCP servers have connection issues with hostnames containing underscores.

Using multiple MCP servers without verifying tools and descriptions could lead to vulnerabilities such as tool poisoning or tool shadowing. Snowflake recommends verifying third-party MCP servers before using them. This includes any MCP server from another Snowflake user or account. Verify all tools offered by third-party MCP servers.

We recommend using OAuth as the authentication method. Using hardcoded tokens can lead to token leakage.

When using a Programmatic Access Token (PAT), set it to use the least-privileged role allowed to work with MCP. This will help prevent leaking a secret with access to a highly-privileged role.

Configure proper permissions for the MCP server and tools following the least-privilege principle. Access to the MCP Server does not give access to the tools. Permission needs to be granted for each tool.

## Create an MCP Server object

Create an object, specifying the tools and other metadata. MCP clients that connect with the server, after requisite authentication, are able to discover and invoke these tools.

1. Navigate to the desired database and schema to create the MCP server in.
2. Create the MCP server:

   CopyExpand

   ```
   CREATE [ OR REPLACE ] MCP SERVER [ IF NOT EXISTS ] <server_name>
     FROM SPECIFICATION $$
       tools:
         - name: "product-search"
           type: "CORTEX_SEARCH_SERVICE_QUERY"
           identifier: "database1.schema1.Cortex_Search_Service1"
           description: "cortex search service for all products"
           title: "Product Search"

         - name: "revenue-semantic-view"
           type: "CORTEX_ANALYST_MESSAGE"
           identifier: "database1.schema1.Semantic_View_1"
           description: "Semantic view for all revenue tables"
           title: "Semantic view for revenue"
     $$
   ```

   Show lessSee more

   Scroll to top

> Snowflake currently supports the following tool types:
>
> * **CORTEX\_SEARCH\_SERVICE\_QUERY:** Cortex Search Service tool
> * **CORTEX\_ANALYST\_MESSAGE:** Cortex Analyst tool
> * **SYSTEM\_EXECUTE\_SQL:** SQL execution
> * **CORTEX\_AGENT\_RUN:** Cortex Agent tool
> * **GENERIC:** tool for UDFs and stored procedures
>
> The following examples show how to configure different tool types:
>
> Analyst toolSearch toolSQL execution toolAgent toolCustom tool
>
> Using the Analyst tool, your client can generate SQL from natural language text. Use the following code to specify the tool configuration.
>
> Note
>
> The Snowflake-managed MCP server only supports using semantic views with Cortex Analyst. It does not support semantic models.
>
> CopyExpand
>
> ```
> tools:
>   - name: "revenue-semantic-view"
>     type: "CORTEX_ANALYST_MESSAGE"
>     identifier: "database1.schema1.Semantic_View_1"
>     description: "Semantic view for all revenue tables"
>     title: "Semantic view for revenue"
> ```
>
> Show lessSee more
>
> Scroll to top
>
> Using the Search tool requests, your client can perform unstructured search on their data.
>
> CopyExpand
>
> ```
> tools:
>   - name: "product-search"
>     type: "CORTEX_SEARCH_SERVICE_QUERY"
>     identifier: "database1.schema1.Cortex_Search_Service1"
>     description: "cortex search service for all products"
>     title: "Product Search"
> ```
>
> Show lessSee more
>
> Scroll to top
>
> For the SQL execution tool, your client can execute SQL queries on Snowflake. Use the following code to specify the tool configuration.
>
> CopyExpand
>
> ```
> tools:
>   - title: "SQL Execution Tool"
>     name: "sql_exec_tool"
>     type: "SYSTEM_EXECUTE_SQL"
>     description: "A tool to execute SQL queries against the connected Snowflake database."
> ```
>
> Show lessSee more
>
> Scroll to top
>
> For the Agent tool, your client passes a message to the agent. The agent processes the request and returns a response. Use the following code to specify the tool configuration.
>
> CopyExpand
>
> ```
> tools:
>   - title: "Agent V2"
>     name: "agent_1"
>     type: "CORTEX_AGENT_RUN"
>     identifier: "db.schema.agent"
>     description: "agent that gives the ability to..."
> ```
>
> Show lessSee more
>
> Scroll to top
>
> For your custom tools, you must provide the user-defined function (UDF) or stored procedure signature in the tool configuration. The custom tool enables you to invoke UDFs and stored procedures as tools through the MCP server.
>
> You need to specify the following in the tool configuration:
>
> * `type`: `function` for UDF, `procedure` for stored procedure
> * `Warehouse`. If you don’t specify a warehouse, the default warehouse is used.
> * `Input schema`: corresponds to the function signature

Use the following examples to create and configure custom tools using UDFs and stored procedures:

UDF examplesStored procedure examplesTool configuration examples

The following examples demonstrate creating UDFs that can be used as custom tools:

CopyExpand

```
-- create a simple udf
CREATE OR REPLACE FUNCTION MULTIPLY_BY_TEN(x FLOAT)
RETURNS FLOAT
LANGUAGE PYTHON
RUNTIME_VERSION = '3.8'
HANDLER = 'multiply_by_ten'
AS
$$
def multiply_by_ten(x: float) -> float:
  return x * 10
$$;

SHOW FUNCTIONS LIKE 'MULTIPLY_BY_TEN';

-- test return json/variant
CREATE OR REPLACE FUNCTION CALCULATE_PRODUCT_AND_SUM(x FLOAT, y FLOAT)
RETURNS VARIANT
LANGUAGE PYTHON
RUNTIME_VERSION = '3.8'
HANDLER = 'calculate_values'
AS
$$
import json

def calculate_values(x: float, y: float) -> dict:
  """
  Calculates the product and sum of two numbers and returns them in a dictionary.
  The dictionary is converted to a VARIANT (JSON) in the SQL return.
  """
  product = x * y
  sum_val = x + y

  return {
      "product": product,
      "sum": sum_val
  }
$$;

-- test return list/array
CREATE OR REPLACE FUNCTION GET_NUMBERS_IN_RANGE(x FLOAT, y FLOAT)
RETURNS ARRAY -- Use ARRAY to explicitly state a list is being returned
LANGUAGE PYTHON
RUNTIME_VERSION = '3.8'
HANDLER = 'get_numbers'
AS
$$
def get_numbers(x: float, y: float) -> list:
  """
  Returns a list of integers between x (exclusive) and y (inclusive).
  Assumes x < y.
  """
  # Ensure x and y are treated as integers for range generation
  start = int(x) + 1
  end = int(y) + 1 # range() is exclusive on the stop value

  # Use a list comprehension to generate the numbers
  # The Python list will be converted to a Snowflake ARRAY.
  return list(range(start, end))
$$;
```

Show lessSee more

Scroll to top

The following examples demonstrate creating stored procedures that can be used as custom tools:

CopyExpand

```
-- create a simple stored procedure
CREATE OR REPLACE PROCEDURE MULTIPLY_BY_TEN_SP(x FLOAT)
RETURNS FLOAT
LANGUAGE PYTHON
RUNTIME_VERSION = '3.8'
PACKAGES = ('snowflake-snowpark-python')
HANDLER = 'multiply_by_ten'
AS
$$
# The handler logic is identical to the UDF for a scalar return
def multiply_by_ten(x: float) -> float:
      return x * 10
$$;

-- test return json/variant
CREATE OR REPLACE PROCEDURE CALCULATE_VALUES_SP(x FLOAT, y FLOAT)
RETURNS VARIANT
LANGUAGE PYTHON
RUNTIME_VERSION = '3.8'
PACKAGES = ('snowflake-snowpark-python')
HANDLER = 'calculate_values'
AS
$$
# The handler logic is identical to the UDF for a VARIANT return
def calculate_values(x: float, y: float) -> dict:
      """
      Calculates the product and sum of two numbers and returns them in a dictionary.
      The dictionary is converted to a VARIANT (JSON) in the SQL return.
      """
      product = x * y
      sum_val = x + y

      return {
          "product": product,
          "sum": sum_val
      }
$$;

-- test return list/array
CREATE OR REPLACE PROCEDURE GET_NUMBERS_SP(x FLOAT, y FLOAT)
RETURNS ARRAY
LANGUAGE PYTHON
RUNTIME_VERSION = '3.8'
PACKAGES = ('snowflake-snowpark-python')
HANDLER = 'get_numbers'
AS
$$
def get_numbers(x: float, y: float) -> list:
      """
      Returns a list of integers between x (exclusive) and y (inclusive).
      The Python list will be converted to a Snowflake ARRAY.
      """
      # Ensure x and y are treated as integers for range generation
      start = int(x) + 1
      end = int(y) + 1 # range() is exclusive on the stop value

      # Use a list comprehension to generate the numbers
      return list(range(start, end))
$$;
```

Show lessSee more

Scroll to top

The following examples demonstrate configuring custom tools for UDFs and stored procedures:

CopyExpand

```
CREATE MCP SERVER my_mcp_server
  FROM SPECIFICATION $$
    tools:
      - title: "Custom Tool 1"
        identifier: "EXAMPLE_DATABASE.AGENTS.MULTIPLY_BY_TEN"
        name: "multiply_by_ten"
        type: "GENERIC"
        description: "Multiplied input value by ten and returns the result."
        config:
          type: "function"
          warehouse: "COMPUTE_SERVICE_WAREHOUSE"
          input_schema:
            type: "object"
            properties:
              x:
                description: "A number to be multiplied by ten"
                type: "number"
      - title: "Custom Tool 2"
        identifier: "EXAMPLE_DATABASE.AGENTS.CALCULATE_PRODUCT_AND_SUM"
        name: "calculate_product_and_sum"
        type: "GENERIC"
        description: "Calculates the product and sum of two numbers and returns them in a JSON object."
        config:
          type: "function"
          warehouse: "COMPUTE_SERVICE_WAREHOUSE"
          input_schema:
            type: "object"
            properties:
              x:
                description: "First number"
                type: "number"
              y:
                description: "Second number"
                type: "number"
      - title: "Custom Tool 3"
        identifier: "EXAMPLE_DATABASE.AGENTS.GET_NUMBERS_IN_RANGE"
        name: "get_numbers_in_range"
        type: "GENERIC"
        description: "Returns a list of integers between two numbers."
        config:
          type: "function"
          warehouse: "COMPUTE_SERVICE_WAREHOUSE"
          input_schema:
            type: "object"
            properties:
              x:
                description: "Start number (exclusive)"
                type: "number"
              y:
                description: "End number (inclusive)"
                type: "number"
      - title: "Custom Tool 4"
        identifier: "EXAMPLE_DATABASE.AGENTS.MULTIPLY_BY_TEN_SP"
        name: "multiply_by_ten_sp"
        type: "GENERIC"
        description: "Multiplied input value by ten and returns the result."
        config:
          type: "procedure"
          warehouse: "COMPUTE_SERVICE_WAREHOUSE"
          input_schema:
            type: "object"
            properties:
              x:
                description: "A number to be multiplied by ten"
                type: "number"
      - title: "Custom Tool 5"
        identifier: "EXAMPLE_DATABASE.AGENTS.CALCULATE_PRODUCT_AND_SUM_SP"
        name: "calculate_product_and_sum_sp"
        type: "GENERIC"
        description: "Calculates the product and sum of two numbers and returns them in a JSON object."
        config:
          type: "procedure"
          warehouse: "COMPUTE_SERVICE_WAREHOUSE"
          input_schema:
            type: "object"
            properties:
              x:
                description: "First number"
                type: "number"
              y:
                description: "Second number"
                type: "number"
      - title: "Custom Tool 6"
        identifier: "EXAMPLE_DATABASE.AGENTS.GET_NUMBERS_IN_RANGE_SP"
        name: "get_numbers_in_range_sp"
        type: "GENERIC"
        description: "Returns a list of integers between two numbers."
        config:
          type: "procedure"
          warehouse: "COMPUTE_SERVICE_WAREHOUSE"
          input_schema:
            type: "object"
            properties:
              x:
                description: "Start number (exclusive)"
                type: "number"
              y:
                description: "End number (inclusive)"
                type: "number"
  $$;
```

Show lessSee more

Scroll to top

1. To show MCP servers, use the following commands:

   CopyExpand

   ```
   SHOW MCP SERVERS IN DATABASE <database_name>;
   SHOW MCP SERVERS IN SCHEMA <schema_name>;
   SHOW MCP SERVERS IN ACCOUNT;
   ```

   Show lessSee more

   Scroll to top

   The following shows the output of the command:

   Expand

   ```
   |               created_on               |       name        | database_name | schema_name |    owner     |           comment            |
   ------------------------------------------+-------------------+---------------+-------------+--------------+------------------------------
   | Fri, 23 Jun 1967 07:00:00.123000 +0000 | TEST_MCP_SERVER   | TEST_DATABASE | TEST_SCHEMA | ACCOUNTADMIN | [NULL]                       |
   | Fri, 23 Jun 1967 07:00:00.123000 +0000 | TEST_MCP_SERVER_2 | TEST_DATABASE | TEST_SCHEMA | ACCOUNTADMIN | Test MCP server with comment |
   ```

   Show lessSee more

   Scroll to top
2. To describe an MCP server, use the following command:

   CopyExpand

   ```
   DESCRIBE MCP SERVER <server_name>;
   ```

   Show lessSee more

   Scroll to top

   The following shows the output of the command:

   Expand

   ```
   |      name       | database_name | schema_name |    owner     | comment |     server_spec        |               created_on               |
   ------------------------------------------------------------------------------------------------------+-------------------------------------
   | TEST_MCP_SERVER | TEST_DATABASE | TEST_SCHEMA | ACCOUNTADMIN | [NULL]  | {"version":1,"tools":[{"name":"product-search","identifier":"db.schema.search_service","type":"CORTEX_SEARCH_SERVICE_QUERY"}]} | Fri, 23 Jun 1967 07:00:00.123000 +0000 |
   ```

   Show lessSee more

   Scroll to top
3. To drop an MCP server, use the following command:

   CopyExpand

   ```
   DROP MCP SERVER <server_name>;
   ```

   Show lessSee more

   Scroll to top

## Access control

You can use the following privileges to manage access to the MCP server and the underlying tools.

| Privilege | Object | Description |
| --- | --- | --- |
| CREATE | MCP SERVER | Required to create the MCP server |
| OWNERSHIP | MCP SERVER | Required to update the object configuration |
| MODIFY | MCP SERVER | Provides update, drop, describe, show, and use (`tools/list` and `tools/call`) on the object configuration |
| USAGE | MCP SERVER | Required to connect with the MCP server and discover tools |
| USAGE | Cortex Search Service | Required to invoke the Cortex Search tool in the MCP server |
| SELECT | Semantic View | Required to invoke the Cortex Analyst tool in the MCP server |
| USAGE | Cortex Agent | Required to invoke the Cortex Agent as a tool in the MCP server |
| USAGE | User-defined function (UDF) or stored procedure | Required to invoke the UDF or stored procedure as a tool in the MCP server |

See moreShow less

Expand

## Set up OAuth authentication

Configure authentication on the MCP client. The Snowflake-managed MCP server supports [OAuth 2.0](../oauth-snowflake-overview) aligned with the [authorization](https://modelcontextprotocol.io/specification/2025-06-18/basic/authorization) recommendation in the MCP protocol. The Snowflake-managed MCP server doesn’t support dynamic client registration.

1. First, create the security integration. For information about this command, see [CREATE SECURITY INTEGRATION (Snowflake OAuth)](../../sql-reference/sql/create-security-integration-oauth-snowflake).

   CopyExpand

   ```
   CREATE [ OR REPLACE ] SECURITY INTEGRATION [IF NOT EXISTS] <integration_name>
     TYPE = OAUTH
     OAUTH_CLIENT = CUSTOM
     ENABLED = TRUE
     OAUTH_CLIENT_TYPE = 'CONFIDENTIAL'
     OAUTH_REDIRECT_URI = '<redirect_URI>'
   ```

   Show lessSee more

   Scroll to top
2. Then, call the system function to retrieve your client id and keys for client configuration. The integration name is case sensitive and must be in uppercase.

   CopyExpand

   ```
   SELECT SYSTEM$SHOW_OAUTH_CLIENT_SECRETS('<integration_name>');
   ```

   Show lessSee more

   Scroll to top

## Interact with the MCP server using a custom MCP client

If you are building a custom MCP client, you must use the URL endpoint with the following format:

CopyExpand

```
https://<account_URl>/api/v2/databases/{database}/schemas/{schema}/mcp-servers/{name}
```

Show lessSee more

Scroll to top

For information about formatting your account URL, see [Account identifiers](../admin-account-identifier).

For information about interacting with the MCP server, see [Build an MCP client](https://modelcontextprotocol.io/docs/develop/build-client).

Note

The Snowflake MCP server currently only supports tool capabilities.

### Discover and invoke tools

The MCP clients can discover and invoke tools with `tools/list` and `tools/call` requests.

To discover or invoke tools, issue a POST call as shown in the [tools/list request](https://modelcontextprotocol.io/specification/2025-06-18/server/tools#calling-tools):

For the Analyst tool, your client passes messages in the request. The SQL statement is listed in the output. You must pass the name of the tool that you’re invoking in the request in the `name` parameter.

CopyExpand

```
POST /api/v2/databases/<database>/schemas/<schema>/mcp-servers/<name>
    {
        "jsonrpc": "2.0",
        "id": 1,
        "method": "tools/call",
        "params": {
            "name": "test-analyst",
            "arguments": {
                "message": "text"
            }
        }
    }
```

Show lessSee more

Scroll to top

The following example shows the response:

CopyExpand

```
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "content": [
            {
                "type": "text",
                "text": "string"
            }
        ]
    }
}
```

Show lessSee more

Scroll to top

For Search tool requests, your client can pass the query and the following optional arguments:

* columns
* limit

The search results and request ID are returned in the output. You must pass the name of the tool that you’re invoking in the request as the `name` parameter.

CopyExpand

```
POST /api/v2/databases/{database}/schemas/{schema}/mcp-servers/{name}
    {
        "jsonrpc": "2.0",
        "id": 1,
        "method": "tools/call",
        "params": {
            "name": "product-search",
            "arguments": {
                "query": "Hotels in NYC",
                "columns": array of strings,
                "limit": int
            }
        }
  }
```

Show lessSee more

Scroll to top

The following example shows the response:

CopyExpand

```
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "results": {}
    }
}
```

Show lessSee more

Scroll to top

## Limitations

Snowflake managed MCP server does not support the following constructs in the MCP protocol: resources, prompts, roots, notifications, version negotiations, life cycle phases, and sampling.

Only non-streaming responses are supported.
