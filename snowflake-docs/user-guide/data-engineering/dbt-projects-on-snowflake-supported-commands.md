---
title: "Supported dbt commands and flags"
url: "https://docs.snowflake.com/en/user-guide/data-engineering/dbt-projects-on-snowflake-supported-commands"
---

# Supported dbt commands and flags

The following table shows the dbt commands that are supported in dbt Projects on Snowflake. Any [dbt command](https://docs.getdbt.com/reference/dbt-commands) that isn’t listed here isn’t supported.

> dbt Projects on Snowflake, supported dbt commands by execution method
>
>
>
>
>
>
> | dbt command | Workspaces | EXECUTE DBT PROJECT | `snow dbt execute` (CLI) |
> | --- | --- | --- | --- |
> | [build](https://docs.getdbt.com/reference/commands/build) | ✔ | ✔ | ✔ |
> | [compile](https://docs.getdbt.com/reference/commands/compile) | ✔ | ✔ | ✔ |
> | [deps](https://docs.getdbt.com/reference/commands/deps) [1] | ✔ | ✔ | ✔ |
> | [docs generate](https://docs.getdbt.com/reference/commands/cmd-docs#dbt-docs-generate) [2] | ✔ | ✔ | ❌ |
> | [list](https://docs.getdbt.com/reference/commands/list) | ✔ | ✔ | ✔ |
> | [parse](https://docs.getdbt.com/reference/commands/parse) | ❌ | ✔ | ✔ |
> | [run](https://docs.getdbt.com/reference/commands/run) | ✔ | ✔ | ✔ |
> | [retry](https://docs.getdbt.com/reference/commands/retry) | ✔ | ❌ | ❌ |
> | [run-operation](https://docs.getdbt.com/reference/commands/run-operation) | ✔ | ✔ | ✔ |
> | [seed](https://docs.getdbt.com/reference/commands/seed) | ✔ | ✔ | ✔ |
> | [show](https://docs.getdbt.com/reference/commands/show) | ✔ | ✔ | ✔ |
> | [snapshot](https://docs.getdbt.com/reference/commands/snapshot) | ✔ | ✔ | ✔ |
> | [test](https://docs.getdbt.com/reference/commands/test) | ✔ | ✔ | ✔ |
>
> See moreShow less
>
> Expand

[1] A dbt project object is a versioned snapshot of your project. Running the deps command on it doesn’t modify any files; it’s primarily
used to verify that your external access configuration is correct. When a dbt project object is created with an external access integration, dbt deps is run before dbt compile to package all dependencies and project files.

[2] dbt Projects on Snowflake don’t support dbt docs serve.

## About flags

In dbt Core, you run commands (for example, `dbt build`) and modify their behavior with flags. Flags are configuration options that modify how a command behaves; some are command-specific, others are global. For more information, see [flags](https://docs.getdbt.com/reference/global-configs/about-global-configs).

You always run a command, and you attach flags to scope or alter it. For example, to run only incremental models and rebuild them, you would run the following command and flags:

CopyExpand

```
dbt run --select config.materialized:incremental --full-refresh;
```

Show lessSee more

Scroll to top

The following flags aren’t supported in dbt Projects on Snowflake:

* `--state`
* `--target-path`
* `--log-path`
* `--profiles-dir`
* `--project-dir`
* `--log-format`
* `--log-format-file`
