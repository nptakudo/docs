---
title: "Supported dbt Core versions for dbt Projects on Snowflake"
url: "https://docs.snowflake.com/en/user-guide/data-engineering/dbt-projects-on-snowflake-dbt-core-versions"
---

# Supported dbt Core versions for dbt Projects on Snowflake

Snowflake provides managed runtimes for dbt Projects to ensure a secure and predictable execution environment. Because dbt Core releases can
introduce breaking changes or security vulnerabilities, Snowflake follows a structured lifecycle for each version. This policy allows users
to pin specific versions for governance and reproducibility while providing a clear timeline for required migrations.

Supported versions for dbt Projects

| dbt Core Version Supported | Snowflake Support Level | dbt Labs Support |
| --- | --- | --- |
| 1.10.15 | Active support | Critical support until Jun 15, 2026 |
| 1.9.4 | Active support | Deprecated |

See moreShow less

Expand

The DBT\_VERSION parameter implicitly defines the execution engine based on the version, as shown in the table below.

Version based engine mapping

| User Input (DBT\_VERSION) | Condition | Resulting Engine |
| --- | --- | --- |
| ‘1.x’ (for example, `1.9.4`) | Version `< 2.0` | dbt Core (Python-based) |

See moreShow less

Expand

## View supported dbt Core versions

To view supported dbt Core versions, run the [SYSTEM$SUPPORTED\_DBT\_VERSIONS](../../sql-reference/functions/system_supported_dbt_versions) system function, as shown
in the following example:

CopyExpand

```
SELECT SYSTEM$SUPPORTED_DBT_VERSIONS();
```

Show lessSee more

Scroll to top

Expand

```
[{"dbt_version":"1.9.4","type":"dbt Core"},{"dbt_version":"1.10.15","type":"dbt Core"}]
```

Show lessSee more

Scroll to top

## Alter dbt Core execution version

To alter the dbt Core version that the dbt project object will execute, run the [ALTER DBT PROJECT](../../sql-reference/sql/alter-dbt-project) command as shown
in the following example:

CopyExpand

```
ALTER DBT PROJECT my_dbt_project SET DBT_VERSION = '1.10.15';
```

Show lessSee more

Scroll to top

## Create a dbt project pinned to a version

The following example creates a dbt project pinned to the 1.10.15 dbt version:

CopyExpand

```
CREATE OR REPLACE DBT PROJECT my_dbt_project
  FROM '@my_stage/dbt_files'
  DBT_VERSION = '1.10.15';
```

Show lessSee more

Scroll to top

For more information and examples, see [CREATE DBT PROJECT](../../sql-reference/sql/create-dbt-project) and [ALTER DBT PROJECT](../../sql-reference/sql/alter-dbt-project).

## How deprecation and decommissioning work

* Snowflake supported versions: These versions are available for all new and existing projects. Snowflake provides full technical support,
  including security patches.
* Snowflake deprecated versions: These versions have reached the end of their active development cycle. While they remain fully functional for
  existing projects, users are discouraged from starting new projects on a deprecated version.
* Snowflake decommissioned versions: These versions are officially removed from the Snowflake environment. At this stage, any project pinned to
  a decommissioned version will fail to execute until it’s updated to a currently supported version.
* dbt Core Support Levels: Even if a version reaches *Critical Support*, *Deprecated*, or *End of Life* status according to
  [dbt Labs](https://docs.getdbt.com/docs/dbt-versions/core#latest-releases), it remains supported on Snowflake. This means that you aren’t
  forced into immediate upgrades and can maintain your existing environment for as long as you choose.
