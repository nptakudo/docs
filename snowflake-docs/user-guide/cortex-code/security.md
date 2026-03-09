---
title: "Security best practices for Cortex Code CLI"
url: "https://docs.snowflake.com/en/user-guide/cortex-code/security"
---

# Security best practices for Cortex Code CLI

Essential security practices for Cortex Code CLI include using secure authentication methods, protecting configuration files, managing roles and access appropriately, handling conversation history securely, ensuring MCP server integrity, and following production safety protocols.

Important

In managed environments, your organization may deploy a system-level managed settings file that enforces policy (for example, restricting tool access, limiting allowed accounts, or disabling bypass capabilities). For details, see [Managed settings (organization policy)](settings.html#label-cortex-code-managed-settings).

## Credentials

Use browser-based authentication when possible.
:   The default authentication method for Cortex Code CLI is browser-based authentication. Use `authenticator = "externalbrowser"` in your `connections.toml` file to set this option manually.

Use programmatic access tokens (PATs), when trying to scope access to a specific role.
:   Generate dedicated PATs in Snowsight (see [Using programmatic access tokens for authentication](../programmatic-access-tokens)). Set expiration ≤ 90 days, use descriptive names, and rotate regularly.

Protect configuration files
:   Use mode `600` for configuration files and `700` for directories to restrict access to only your user.

    CopyExpand

    ```
    chmod 600 ~/.snowflake/connections.toml
    chmod 700 ~/.snowflake/cortex
    ```

    Show lessSee more

    Scroll to top

Never commit credentials
:   Add sensitive configuration files to `.gitignore`.

    CopyExpand

    ```
    echo "~/.snowflake/connections.toml" >> ~/.gitignore
    ```

    Show lessSee more

    Scroll to top

    Use environment variables to hold credentials and tokens, and incorporate them in your configuration files using `${VARIABLE_NAME}` syntax.

## Roles & access

Use appropriate roles per environment
:   For example, use a read-only role in production and a more expansive role in development.

    CopyExpand

    ```
    [dev]
    role = "DEVELOPER"

    [prod_readonly]
    role = "ANALYST"
    ```

    Show lessSee more

    Scroll to top

    Never use `ACCOUNTADMIN` for routine operations. Grant least privileges.

## Conversation history

Conversations are stored in `~/.snowflake/cortex/conversations/`. Use `cortex --private` when starting Cortex Code to disable session saving for sensitive work.
Alteranatively, use the `/clear` command to clear the current session before exiting Cortex Code CLI.

Use mode 700 to restrict access to conversation history to only your user.

CopyExpand

```
chmod 700 ~/.snowflake/cortex/conversations
```

Show lessSee more

Scroll to top

## MCP security

Only install trusted MCP servers
:   Verify the source and integrity of MCP servers before adding them. Use the following commands to get a list of servers and remove any untrusted ones:

    CopyExpand

    ```
    cortex mcp list
    cortex mcp remove <server>
    ```

    Show lessSee more

    Scroll to top

Never hardcode MCP credentials
:   Use environment variables. First, set in your shell:

    CopyExpand

    ```
    export GITHUB_TOKEN="your_token"
    ```

    Show lessSee more

    Scroll to top

    Then reference them in your MCP configuration:

    CopyExpand

    ```
    {
       "mcpServers": {
          "github": {
             "env": { "GITHUB_TOKEN": "${GITHUB_TOKEN}" }
          }
       }
    }
    ```

    Show lessSee more

    Scroll to top

## Production safety

Enable planning mode
:   Use the `/plan` command to review intended actions before execution.

    CopyExpand

    ```
    /plan
    Drop and recreate the ANALYTICS schema
    ```

    Show lessSee more

    Scroll to top

## If your personal access token is compromised

Revoke the PAT in Snowsight immediately! Then generate a new token and start using it instead. Remember, don’t use the
token in configuration files; use environment variables instead.

Review the query history to identify any suspicious activity.

CopyExpand

```
SHOW QUERIES IN ACCOUNT
```

Show lessSee more

Scroll to top

## Managed settings (enterprise policy)

In some organizations, administrators deploy managed settings that enforce policy for Cortex Code CLI. Managed settings can constrain or override user-level configuration (including permission prompts and bypass behavior).

For more information, see [Managed settings (organization policy)](settings.html#label-cortex-code-managed-settings).

## Permissions

Cortex Code has three operational modes:

| Mode | Indicator | Slash commands | Description |
| --- | --- | --- | --- |
| Confirm actions | Blue ⏵⏵ | Default mode | Prompts for permission before potentially dangerous actions. |
| Plan | Orange ⏸ | `/plan`, `/plan-off` | Presents a plan before taking any action. |
| Bypass | Red >> | `/bypass`, `/bypass-off` | All tool calls are approved. |

See moreShow less

Expand

Press `Shift-Tab` in Cortex Code CLI to cycle among these modes.

Warning

The Bypass mode disables all confirmation prompts. Use it only in trusted environments.

### Permission types

The following permission levels apply to Cortex Code tool calls:

| Type | Description |
| --- | --- |
| EXECUTE\_COMMAND | Run bash/shell commands |
| FILE\_READ | Read file contents |
| FILE\_WRITE | Create/modify files |
| FILE\_EDIT | Edit existing files |
| WEB\_ACCESS | Web search/fetch operations |

See moreShow less

Expand

### Trust model

Cortex Code classifies commands and operations by risk, as shown in the following table:

| Level | Examples | Behavior |
| --- | --- | --- |
| SAFE | `ls`, `cat`, `echo`, `grep` | Auto-approved |
| LOW | Create new files (e.g., `touch file.txt`) | Usually auto-approved |
| MEDIUM | Edit files (e.g., `nano file.txt`), moderate bash | Prompts in Confirm mode |
| HIGH | `rm`, `curl`, `wget`, `sudo` | Always prompts |
| CRITICAL | `rm -rf`, destructive ops | Extra confirmation |

See moreShow less

Expand

Shell and SQL commands are classified based on their potential impact.

#### Shell commands

Commands are analyzed for common risk factors.

Risky commands
:   * `rm`, `sudo`, `curl`, `wget`, `ssh`
    * `systemctl`, `chmod`, `chown`
    * `git push --force`, `git reset --hard`

Dangerous flags
:   * `-rf`, `--force`, `--recursive`
    * `--no-preserve-root`

Dangerous patterns
:   * Pipe to shell: `curl | bash`
    * Download and execute
    * Hidden file access (`.` prefix)
    * System path access (`/etc`, `/var`, `/usr`)

#### SQL queries

SQL is categorized by operation type:

| Category | Operations | Behavior |
| --- | --- | --- |
| READ\_ONLY | SELECT, SHOW, DESCRIBE | Auto-approved |
| WRITE | INSERT, UPDATE, DELETE, CREATE | Prompts |
| USE\_ROLE | USE ROLE, USE WAREHOUSE | Prompts |

See moreShow less

Expand

### Sandbox permissions

When sandboxing is enabled:

| Sandbox mode | Permission behavior |
| --- | --- |
| Container + Auto | Sandboxed commands auto-approved |
| Container + Regular | All commands prompt |
| OS + Auto | Sandboxed commands auto-approved |
| OS + Regular | All commands prompt |

See moreShow less

Expand

### Hook integration

You can customize permission policy using hooks. Here is an example pre-execution hook that approves auto-approves bash commands:

CopyExpand

```
{
   "hooks": {
      "PreToolUse": [
         {
         "matcher": "Bash",
         "hooks": [
            {
               "type": "command",
               "command": "bash .claude/hooks/auto-approve.sh"
            }
         ]
         }
      ]
   }
}
```

Show lessSee more

Scroll to top

This hook might return a JSON response like the following to auto-approve bash commands.

CopyExpand

```
{
   "hookSpecificOutput": {
      "hookEventName": "PreToolUse",
      "permissionDecision": "allow",
      "permissionDecisionReason": "Approved by policy"
   }
}
```

Show lessSee more

Scroll to top

### Permission prompts and caching

When Cortex Code requires your permission to proceed with an operation, it prompts you with details about the request.
You can choose to approve or deny the request. You can also opt to remember your choice for future similar requests:

* “Always allow (this session)” remembers until you exit Cortex Code CLI.
* “Always allow (persist)” remembers indefinitely.

These responses are cached and scoped to the project directory, the tool type, or the command pattern as appropriate.

Persistent permissions are stored in `~/.snowflake/cortex/permissions.json`. The following is an example cache:

CopyExpand

```
{
   "/path/to/project": {
      "Bash": {
         "npm test": "allow",
         "make build": "allow"
      },
      "Write": {
         "*": "allow"
      }
   }
}
```

Show lessSee more

Scroll to top

Delete this file to reset all persistent permissions. To reset permissions for a specific project, delete the corresponding entry.

To reset the session cache, use the `/new` command, which begins a new session, or exit and re-start Cortex Code CLI.

### Configuration

Set the environment variables described below to control permission behavior:

| Variable | Description |
| --- | --- |
| `CORTEX_PERMISSION_CACHE_TTL_SECONDS` | Sets the default timeout for session permission cache (in seconds). |
| `COCO_DANGEROUS_MODE_REQUIRE_SQL_WRITE_PERMISSION=true` | If set to `1`, always prompt for SQL write operations, even in bypass mode |

See moreShow less

Expand

## Security Checklist

* Use PATs with at most a 90 day expiration
* Set file permissions to 600/700
* Never commit credentials to git
* Use least privilege roles
* Never use ACCOUNTADMIN for routine work
* Enable planning mode for production and reserve bypass mode for trusted environments
* Only install trusted MCP servers
* Store credentials in environment variables
* Use hooks to enforce policies by automating custom security checks
* Periodically audit permissions
