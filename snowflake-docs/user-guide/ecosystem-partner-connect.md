---
title: "Snowflake Partner Connect"
url: "https://docs.snowflake.com/en/user-guide/ecosystem-partner-connect"
---

# Snowflake Partner Connect

Partner Connect lets you easily create trial accounts with selected Snowflake business partners and integrate these accounts with
Snowflake. This feature provides a convenient option for trying various 3rd-party tools and services, and then adopting the ones
that best meet your business needs.

## Supported Partners

Important

Snowflake neither determines nor dictates the conditions or terms (length, supported features, etc.) for partner trial accounts; these
policies are set by each Snowflake partner and vary according to the partner.

For details about a specific trial, please contact the partner directly.

Currently, Partner Connect includes the following partners:

| Partner | Category | Notes |
| --- | --- | --- |
| [ALTR](https://www.altr.com/) | [Security, Governance & Observability](ecosystem-security) | Free forever plan |
| [CARTO](https://carto.com/) | [Business Intelligence (BI)](ecosystem-bi) |  |
| [Census](https://www.getcensus.com/) | [Data Integration](ecosystem-etl) |  |
| [Coalesce](https://coalesce.io/) | [Data Integration](ecosystem-etl) |  |
| [Dataiku](https://www.dataiku.com/dss/editions/) | [Machine Learning & Data Science](ecosystem-analytics) |  |
| [dbt Labs](https://www.getdbt.com/) | [Data Integration](ecosystem-etl) | dbt Cloud |
| [Domo](https://www.domo.com/) | [Business Intelligence (BI)](ecosystem-bi) |  |
| [Etleap](https://etleap.com/) | [Data Integration](ecosystem-etl) |  |
| [Fivetran](https://www.fivetran.com) | [Data Integration](ecosystem-etl) |  |
| [Hevo Data](https://hevodata.com/) | [Data Integration](ecosystem-etl) | Hevo Data CDC for ETL |
| [Hex](https://hex.tech/) | [Machine Learning & Data Science](ecosystem-analytics) |  |
| [Hightouch](https://hightouch.com/) | [Data Integration](ecosystem-etl) |  |
| [Hunters](https://hunters.ai/) | [Security, Governance & Observability](ecosystem-security) |  |
| [Informatica](https://www.informatica.com) | [Data Integration](ecosystem-etl) | Informatica Cloud |
| [Informatica Data Loader](https://marketplace.informatica.com/forms/data-loader.html) | [Data Integration](ecosystem-etl) | Informatica Data Loader |
| [Keboola](http://www.keboola.com/) | [Data Integration](ecosystem-etl) |  |
| [Matillion Data Productivity Cloud](https://www.matillion.com/products/data-loader/) | [Data Integration](ecosystem-etl) | Matillion Data Productivity Cloud |
| [Sigma Computing](https://sigmacomputing.com/) | [Business Intelligence (BI)](ecosystem-bi) |  |
| [SnapLogic](https://www.snaplogic.com/) | [Data Integration](ecosystem-etl) |  |
| [SqlDBM](https://sqldbm.com/Home/) | [SQL Development & Management](ecosystem-editors) |  |
| [Striim](https://www.striim.com/) | [Data Integration](ecosystem-etl) |  |
| [ThoughtSpot](https://www.thoughtspot.com/) | [Business Intelligence (BI)](ecosystem-bi) |  |

See moreShow less

Expand

## Security Requirements

Partner Connect is limited to account administrators (i.e. users with the ACCOUNTADMIN role) who have a verified email address in
Snowflake:

* To use Partner Connect, you must switch to the ACCOUNTADMIN role or contact someone in your organization who has the role.
* To verify your email address:

  Snowsight:
  :   In some cases, you automatically receive an email prompting you to Please Validate Your Email. If you didn’t, follow these
      steps to verify your email address:

      1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
      2. In the lower-left corner, select your name » Settings.
      3. In My Profile, configure your email address:

         + If you don’t have an email address listed, enter an email address in the Email field, and then select Save.
         + If you can’t enter an email address, an account administrator must either add an email address on your behalf or grant your user
           the role with the OWNERSHIP privilege on your user.
         + If you didn’t receive an email, select Resend verification email. Snowflake sends a verification email to the address listed.
      4. Open your email, and then select the link in the email to validate your email address.

## Connecting with a Snowflake Partner

To initiate a trial account with any Snowflake partner currently in Partner Connect:

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. To switch to the account administrator role, in the lower-left corner, select your name » Switch role » ACCOUNTADMIN.
3. In the navigation menu, select Admin » Partner Connect.
4. Click on the corresponding tile for the partner to which you wish to connect.

   A dialog displays the requirements for connecting to the partner, as well as a list of the objects automatically created in Snowflake
   during the connection process, including an empty database, warehouse, default user, and custom role. The partner application uses
   these objects when reading from or writing to your account.
5. Optionally specify one or more existing databases in Snowflake to automatically use with the trial. This creates an additional
   custom role that makes existing data in Snowflake quickly and easily available to the partner application.

   If you do not specify any databases during the initial connection process, you can specify them later; however, specifying them later
   is a manual task.

   > To use shared databases with a trial:
   >
   > * Use [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in) to complete the initial connection process.
   > * Manually specify the shared database after the process completes.
6. Click the Connect button below the partner description to initiate creating a trial account with the partner and connecting the
   partner application to Snowflake.

When the process is complete and the objects have been created, the partner tile is updated with a checkmark.

### Objects Created for the Partner

During the connection process, the following Snowflake objects for the partner application are created in your account:

| Object Name | Type | Notes |
| --- | --- | --- |
| PC\_<*partner*>\_DB | Database | This database is empty and can be used to load/store data for querying. If you wish to use existing databases that already contain data, during the initial connection process, you can specify any non-shared databases to use in the field provided. You can also manually specify other databases after the process completes. |
| PC\_<*partner*>\_WH | Warehouse | The default size of the warehouse is X-Small, but can be changed if needed. |
| PC\_<*partner*>\_USER | System User | This is the user that connects to Snowflake from the partner application. As noted in the dialog, a random password for the user is automatically generated. |
| PC\_<*partner*>\_ROLE | Role | The PUBLIC role is granted to this custom role, which enables the role to access any objects owned/granted to the PUBLIC role. In addition, this role is granted to the SYSADMIN role, which enables users with the SYSADMIN role (or higher) to also access any Snowflake objects created for partner access. |

See moreShow less

Expand

In addition, if you optionally chose to specify one or more existing databases during the initial connection process, a second custom
role is created with all of the necessary privileges to access the tables in the databases:

PC\_<*partner*>\_DB\_PICKER\_ROLE

This role is then granted to the PC\_<*partner*>\_ROLE, which enables all the tables in the specified databases to be used by the partner
application with minimal (or no) additional configuration.

Note that this second role is not displayed in the dialog, but the role is created automatically after all the other objects listed in
the dialog are created.

Tip

The above objects are created to enable a quick, convenient setup:

* If you prefer to use existing Snowflake objects (databases, warehouses, users, etc.), you can update the preferences in the partner
  application to reference the desired objects in Snowflake.
* An account administrator can use [ALTER USER](../sql-reference/sql/alter-user) to change the generated password for
  PC\_<*partner*>\_USER.
* To enable access to objects owned by (or granted to) roles other than PUBLIC, grant the other roles to PC\_<*partner*>\_ROLE.

### Automated Application Features and Resource Usage

Partner applications may include automated features such as dashboards that run on a schedule and consume compute resources. We
encourage you to read the product documentation for a partner application and to
[monitor usage](warehouses-load-monitoring) of the PC\_<*partner*>\_WH warehouse to avoid unexpected Snowflake
credit usage by the application.

## Adding Partner IP Addresses to Network Policies

If you use a [network policy](network-policies) to restrict access to your Snowflake account based on user IP
address, partner applications will not be able to access your account unless you add the partner’s IP addresses to the list of
allowed IP addresses in the network policy. For detailed instructions, see [Modify a network policy](network-policies.html#label-modifying-network-policies).

The following table lists the IP addresses to add for each partner (if available and supported) or provides links to pages on the
partner sites for this information:

| Partner | IP Addresses | Notes |
| --- | --- | --- |
| ALTR | `3.145.219.176/28` . `35.89.45.128/28` . `44.203.133.160/28` |  |
| CARTO | N/A |  |
| Census | N/A |  |
| Coalesce | N/A |  |
| Dataiku | N/A |  |
| dbt Labs | `52.22.161.231` . `52.45.144.63` . `54.81.134.249` |  |
| Domo | N/A |  |
| Etleap | N/A |  |
| Fivetran | `52.0.2.4` | For more setup details, see the [Fivetran Documentation](https://fivetran.com/docs/warehouses/snowflake). |
| Hunters | `18.192.165.147` . `34.223.20.125` . `34.223.186.164` . `34.223.221.217` . `52.32.222.121` . `52.35.55.27` . `52.35.219.75` . `52.40.78.172` . `54.68.155.124` . `54.72.125.231` . `54.73.199.243` . `54.75.50.99` . `54.212.81.93` . `54.214.94.117` . `54.220.191.11` |  |
| Hevo Data CDC for ETL | TBD |  |
| Hex | N/A |  |
| Hightouch | N/A |  |
| Informatica | N/A |  |
| Informatica Data Loader | N/A |  |
| Keboola | N/A |  |
| Matillion Data Productivity Cloud | N/A |  |
| Sigma | `104.197.169.18` . `104.197.193.23` |  |
| SnapLogic | Various | For the IP addresses, see the [SnapLogic Documentation](https://docs-snaplogic.atlassian.net/wiki/spaces/SD/pages/1439269/Network+Setup#NetworkSetup-IPAddressWhitelisting). |
| SqlDBM | N/A |  |
| Striim | N/A |  |
| ThoughtSpot | `35.164.213.211` |  |

See moreShow less

Expand

## Launching a Partner Application

After a partner application is connected to Snowflake:

1. On the Snowflake Partner Connect page, click the corresponding tile.
2. Click the Launch button to open the partner web site.

## Disconnecting from a Partner Account

If you decide to discontinue a trial account initiated through Partner Connect for any reason, complete the following steps:

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. To switch to the account administrator role, in the lower-left corner, select your name » Switch role » ACCOUNTADMIN.
3. In the navigation menu, select Admin » Partner Connect.
4. Click the tile for the partner application you are disconnecting from. In the dialog that opens, note the names of the database,
   warehouse, system user, and custom role objects that were created for the partner application during the initial connection process.
5. Use the appropriate [DROP <object>](../sql-reference/sql/drop) command to remove each of the objects created for the partner application.

   Tip

   During the initial connection process, if you specified existing databases to use with the partner application, remember to also
   drop the PC\_<*partner*>\_DB\_PICKER\_ROLE role that was automatically created along with the other objects.
6. Open a new worksheet in [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in) and run the following command to complete the removal of the partner
   connection:

   CopyExpand

   ```
   select system$remove_etl_integration('partnername');
   ```

   Show lessSee more

   Scroll to top

   Replace `<partner_name>` with the name of the partner application you are disconnecting from.
7. If the trial does not expire on its own, contact the partner to end your participation in the trial.

## Troubleshooting a Connection

### Connection Already Exists

If your organization already has an account with the partner, initiated either with the partner directly or using Partner Connect on
another one of your Snowflake accounts, initiating another trial account might fail with a message that a connection already exists.

In this case, the trial for this account must be initiated directly through the partner.
