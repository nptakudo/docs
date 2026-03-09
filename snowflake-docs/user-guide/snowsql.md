---
title: "SnowSQL (CLI client)"
url: "https://docs.snowflake.com/en/user-guide/snowsql"
---

# SnowSQL (CLI client)

Note

[Snowflake CLI](../developer-guide/snowflake-cli/index) is an open-source command-line tool explicitly designed for developer-centric workloads in addition to SQL operations. Snowflake CLI is a more modern, robust, and efficient CLI client than legacy SnowSQL. Snowflake CLI not only lets you execute SQL commands, but also lets you execute commands for other Snowflake products like Streamlit in Snowflake, Snowpark Container Services, and Snowflake Native App Framework. Snowflake will only add new features and enhancements to Snowflake CLI. Consequently, Snowflake recommends that you begin transitioning from SnowSQL to Snowflake CLI.

To help you with the transition from SnowSQL to Snowflake CLI, see [Migrating from SnowSQL to Snowflake CLI](snowsql-migrate).

As of July 2025, Snowflake will provide support based on the minor releases for SnowSQL, as follows:

> | SnowSQL version | Initial release date | Support end date |
> | --- | --- | --- |
> | 1.2.x | February 02, 2023 | December 19, 2025 |
> | 1.3.x | May 02, 2024 | May 02, 2026 |
> | 1.4.x | May 22, 2025 | May 22, 2027 |
>
> See moreShow less
>
> Expand

SnowSQL is a legacy command-line client for connecting to Snowflake to execute SQL queries and perform all DDL and DML operations, including loading data into and unloading data out of database tables.

SnowSQL (`snowsql` executable) can be run as an interactive shell or in batch mode through `stdin` or using the `-f` option.

SnowSQL is an example of an application developed using the [Snowflake Connector for Python](../developer-guide/python-connector/python-connector); however, the connector is not a prerequisite for installing SnowSQL. All required software for installing SnowSQL
is bundled in the installers.

Snowflake provides platform-specific versions of SnowSQL for download for the following platforms:

| Operating System | Supported Versions |
| --- | --- |
| Linux | CentOS 7, 8 |
|  | Red Hat Enterprise Linux (RHEL) 7, 8 |
|  | Ubuntu 16.04, 18.04, 20.04 or later |
| macOS | 10.14 or later |
| Microsoft Windows | Microsoft Windows 8 or later |
|  | Microsoft Windows Server 2012, 2016, 2019, 2022 |

See moreShow less

Expand

## Related videos

> Snowflake 101 | SnowSQL

**Next Topics:**

* [Installing SnowSQL](snowsql-install-config)
* [Configuring SnowSQL](snowsql-config)
* [Connecting through SnowSQL](snowsql-start)
* [Using SnowSQL](snowsql-use)
