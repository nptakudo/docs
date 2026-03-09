---
title: "Cortex Code CLI agent tools"
url: "https://docs.snowflake.com/en/user-guide/cortex-code/tools"
---

# Cortex Code CLI agent tools

Cortex Code has access to a comprehensive set of tools for file operations, shell commands, Web access, and more.
You don’t need to install anything extra; these tools are built into Cortex Code CLI and ready to use. Cortex Code
automatically uses appropriate tools based on your requests. You do not need to invoke them manually; just describe
what you want. For example:

CopyExpand

```
Read the first 10 lines of the file src/main.py
Search for TODO comments in all Python files
Execute a bash command to list running processes
```

Show lessSee more

Scroll to top

When creating custom skills, you must specify the tools the skill can use. See [Skills](extensibility.html#extensibility-skills).

## File tools

### Read

Read file contents from the local filesystem. Supports:

* Text files with line numbers
* Images (PNG, JPG, etc.) - displayed visually
* PDFs - page-by-page extraction
* Jupyter notebooks - cells with outputs
* Line ranges: @file.py$10-20

### Write

Create or overwrite files. Supports:

* Creates parent directories automatically
* Tracks line changes for session statistics
* Overwrites existing files

### Edit

Search and replace in files. Supports:

* Exact string replacement
* Diff preview before changes
* Supports replace\_all for global replacement

### Glob

Find files by pattern matching. Examples:

| Pattern | Description |
| --- | --- |
| `**/*.py` | All Python files |
| `src/**/*.ts` | TypeScript files in `src/` directory |
| `**/test_*.py` | Python test files |
| `!node_modules` | Exclude patterns |

See moreShow less

Expand

### Grep

Search file contents using a regular expression. Supports:

* Recursive search
* Regex patterns
* Binary file detection
* Output modes: content, files, count

## Shell tools

### Bash

Execute shell commands. Supports:

* Streaming output
* Background execution (run\_in\_background)
* Timeout control (default 2 min, max 10 min)
* Sandbox runtime support

### BashOutput

Retrieve output from a background shell process.

* Filter output by regex
* Status checking
* Use with run\_in\_background

### KillShell

Terminate running background shells.

## Agent tools

### RunSubagent

Launch subagents for specialized tasks. Types:

* general-purpose: All tools, research tasks
* Explore: Fast codebase exploration
* Plan: Architecture and planning
* Custom agents from .cortex/agents/

See [Subagents](extensibility.html#extensibility-subagents) for details.

### AskUserQuestion

Prompt user for input during execution. Supports:

* Multiple choice questions
* Free-form input
* Multi-select options

### Review

Launch a review subagent for quality assurance.

## Web tools

### WebSearch

Search the Web using multiple engines. Supports:

* Fallback search engines
* Snippet extraction
* Result caching
* 30-second timeout

Note

WebSearch requires enabling web search in the Cortex Code settings in Snowsight. See [Web search](cortex-code-snowsight.html#label-cortex-code-web-search).

### WebFetch

Retrieve content from web URLs. Supports:

* HTML to text conversion
* Content extraction
* Max 10,000 characters
* 30-second timeout

## Snowflake tools

### SnowflakeSqlExecute

Execute SQL queries on Snowflake. Supports:

* Permission checks
* Result caching
* Token refresh
* Large result offloading

### SnowflakeObjectSearch

Semantic search for database objects.

|  |  |
| --- | --- |
| Searches | tables, viewss, schemas, databases, functions |
| Returns | names, columns, descriptions |

See moreShow less

Expand

### SnowflakeProductDocs

Search Snowflake documentation. Supported categories:

* User guide
* SQL reference
* Developer guide
* Cortex Code topics

### ReflectSemanticModel

Validate Cortex Analyst semantic models. Validation stages:

* File existence
* YAML syntax
* Schema validation
* Server-side validation

### SnowflakeMultiCortexAnalyst

Execute Cortex Analyst queries. Supports:

* Natural language to SQL
* Semantic model support
* Verified Query Retrieval

## Data tools

### DataDiff

Compare data between databases/tables. Supports:

* Snowflake connection handling
* Account identifier derivation
* 300-second timeout

### NotebookExecute

Execute Jupyter notebooks. Supports:

* Timeout control
* Kernel management
* Parameter injection
* Custom Python environments

### NotebookEdit

Edit Jupyter notebook cells. Supported modes:

* replace: Replace cell content
* insert: Add new cell
* delete: Remove cell

## Plan mode tools

### EnterPlanMode

Request plan mode for complex tasks. Supports:

* User approval workflow
* Automatic invocation for multi-step tasks

### ExitPlanMode

Present plan to user and exit plan mode. Supports:

* Plan confirmation
* Streaming control

## Memory tools

### Memory

Store and retrieve information across sessions. Supported commands:

* view: See stored memories
* create: Store new memory
* str\_replace: Update memory
* insert: Add to memory
* delete: Remove memory
* rename: Rename memory file

Note

The Memory tool must be enabled by setting the CORTEX\_ENABLE\_MEMORY environment variable.

## Permission levels

Tools have different permission requirements:

| Level | Tools | Behavior |
| --- | --- | --- |
| Safe | Read, Glob, Grep | Auto-approved |
| Low | Write (new files) | Usually auto-approved |
| Medium | Edit, Bash (safe) | Prompts in Confirm mode |
| High | Bash (risky), SQL write | Always prompts |
| Critical | rm -rf, sudo | Extra confirmation |

See moreShow less

Expand

See [Security](security) for details.
