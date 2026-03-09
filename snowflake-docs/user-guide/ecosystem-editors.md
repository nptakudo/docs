---
title: "SQL Development & Management"
url: "https://docs.snowflake.com/en/user-guide/ecosystem-editors"
---

# SQL Development & Management

Snowflake provides the following native SQL development and data querying interfaces:

| Solution |  | Description | Notes |
| --- | --- | --- | --- |
| [Snowflake clients and interfaces](https://www.snowflake.com/) | [Snowsight Worksheets](ui-snowsight-worksheets) | Browser-based SQL development and editing. | * No installation or configuration required. * Supports multiple, independent working environments that can be opened/closed, named, and reused across multiple sessions   (all work is automatically saved). |
| [Snowflake clients and interfaces](https://www.snowflake.com/) | [Snowflake CLI](../developer-guide/snowflake-cli/index) | Open-source command-line tool explicitly designed for developer-centric workloads in addition to SQL operations, including querying, executing DDL/DML commands, and bulk loading/unloading of data. | * Download the installer from the [Snowflake CLI repository](https://sfc-repo.snowflakecomputing.com/snowflake-cli/index.html) page. |
| [Snowflake clients and interfaces](https://www.snowflake.com/) | [SnowSQL](snowsql) | Python-based client for performing all tasks in Snowflake, including querying, executing DDL/DML commands, and bulk loading/unloading of data. | * Download the installer from the [SnowSQL Download](https://developers.snowflake.com/snowsql/) page. |
| [Snowflake clients and interfaces](https://www.snowflake.com/) | [Snowflake Extension for Visual Studio Code](vscode-ext) | Snowflake provides an extension for Visual Studio Code to enable Snowflake users to write and execute Snowflake SQL statements directly in VSC, using either SQL files or Python files containing Snowpark Python code. | * Install the extension directly from within Visual Studio Code, or indirectly by downloading a specific version. |

See moreShow less

Expand

In addition, Snowflake works with a variety of 3rd-party SQL tools for managing the modeling, development, and deployment of SQL code in
your Snowflake applications, including, but not limited to:

| Solution |  | Version / Installation Requirements | Notes |
| --- | --- | --- | --- |
| [Agile Data Engine](https://www.agiledataengine.com/) |  | **Agile Data Engine:** No requirements  **Snowflake:** No requirements |  |
| [Aginity Pro / Aginity Team](https://www.aginity.com/) |  | **Aginity:** Pro or Team  **Snowflake:** No requirements |  |
| [DataOps.live](https://www.dataops.live/) |  | **DataOps.live:** No requirements  **Snowflake:** No requirements | * Available for trial via [Snowflake Partner Connect](ecosystem-partner-connect). |
| [DBeaver SQL client](http://dbeaver.jkiss.org/) |  | **DBeaver:** 4.3.4 (or higher)  **Snowflake:** [JDBC Driver](../developer-guide/jdbc/jdbc) — automatically downloaded and installed by DBeaver |  |
| [erwin](https://erwin.com/) |  | **erwin:** Data Modeler 2020 (or higher)  **Snowflake:** No requirements | * Additional resources:    + [User Guides > erwin Data Modeler … Snowflake Object Support](https://erwin.com/bookshelf/public_html/2020R2/Content/User%20Guides/erwin%20Help/Snowflake_Object_Support.html) (erwin Documentation)   + [User Guides > erwin Data Modeler … Database Connection Parameters](https://erwin.com/bookshelf/public_html/2020R2/Content/User%20Guides/erwin%20Help/Database_Connection_Parameters.html) (erwin Documentation) |
|  |  | **Hackolade:** Studio 5.2.0 (or higher)  **Snowflake:** No requirements | * Additional resources:    + [Snowflake](https://hackolade.com/help/Snowflake.html) (Hackolade Documentation)   + [Connect to a Snowflake instance](https://hackolade.com/help/ConnecttoaSnowflakeinstance.html) (Hackolade Documentation) |
| [SeekWell](https://www.seekwell.io/) |  | **SeekWell:** No requirements  **Snowflake:** No requirements | * Additional resources:    + [Connecting to Snowflake](https://intercom.help/seekwell/articles/2725663-connecting-to-snowflake) (SeekWell Help Center) |
| [Solita Agine Data Engine](https://www.solita.fi/en/agiledataengine/) |  | **Solita Agile Data Engine:** No requirements  **Snowflake:** No requirements |  |
| [SqlDBM](https://sqldbm.com/Home/) | [Available in Partner Connect](https://docs.snowflake.com/en/user-guide/ecosystem-partner-connect.html) | **SqlDBM:** No requirements  **Snowflake:** No requirements | * Available for trial via [Snowflake Partner Connect](ecosystem-partner-connect). * Additional resources:    + [SqlDBM Partnership with Snowflake](http://blog.sqldbm.com/snowflake-data-modelling-with-sqldbm/) (SqlDBM Blog) |
| [SQL Workbench/J client](http://www.sql-workbench.net/) |  | **SQL Workbench:** No requirements  **Snowflake:** [JDBC Driver](../developer-guide/jdbc/jdbc) — download from the [JDBC Driver page in the Maven Central Repository](https://central.sonatype.com/search?q=g%3Anet.snowflake%20snowflake-jdbc) | * Additional resources:    + [Configuring SQL Workbench/J to Use Snowflake](https://community.snowflake.com/s/article/configuring-sql-workbenchj-to-use-snowflake)     (Snowflake Community) |
| [Statsig](https://statsig.com/) |  | **Statsig:** No requirements  **Snowflake:** No requirements | * Additional resources:    + [Integrations > Data Imports > Snowflake](https://docs.statsig.com/integrations/data-imports/snowflake) (Statsig Documentation) |

See moreShow less

Expand

Note

This is not a complete list of SQL management tools that work with Snowflake; these are known tools that have been validated for use
with Snowflake. Other tools can be used with Snowflake; however, we do not guarantee that all features/functionality in these 3rd-party
tools will interoperate with Snowflake.
