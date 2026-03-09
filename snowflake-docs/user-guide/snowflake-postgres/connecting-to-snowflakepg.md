---
title: "Connecting to Snowflake Postgres"
url: "https://docs.snowflake.com/en/user-guide/snowflake-postgres/connecting-to-snowflakepg"
---

# Connecting to Snowflake Postgres

Once you create a Snowflake Postgres instance, you can connect to it with any PostgreSQL client, such as `psql` or DBeaver. To
establish a connection, you configure your client with:

* The **hostname** of the instance. This is the URL of the virtual machine host.
* A **username**. When you create an instance, the `snowflake_admin` user is created by default and designed for administrative
  access.
* The **Postgres database** that you want to connect to. This parameter is required to create Postgres connections. The default
  database is named `postgres`.
* A **password** for your user.

Here is an example of these connections details used with the `psql` command line client:

CopyExpand

```
$ psql -h abcefg.snowflake.app  -U snowflake_admin -d postgres
```

Show lessSee more

Scroll to top

(`psql` will prompt for a password.)

If you need to specify a port, use 5432:

CopyExpand

```
$ psql -h abcefg.snowflake.app  -U snowflake_admin -p 5432 -d postgres
```

Show lessSee more

Scroll to top

Important

SSL is required to connect to Snowflake Postgres instances.

## About connection strings

When creating a Postgres instance via Snowsight, Snowflake Postgres provides a connection string in
[libpq URI format](https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNSTRING) to use to connect directly
via `psql` or to input into your application configuration.

Note

A cluster’s connection string remains the same across cluster management operations, unless you explicitly reset access for
a given role.

The connection string as a database URL contains the following parameters:

* protocol: `postgres://`
* username: See [Snowflake Postgres Roles](postgres-roles) for more details
* password
* hostname
* port: 5432
* database\_name: Defaults to `postgres`

These are then used to build a URI connection string with this format:

CopyExpand

```
postgresql://<username>:<password>@hostname:<port>/<database_name>
```

Show lessSee more

Scroll to top

If your client environment is not otherwise configured to enforce SSL connections, you can append `?sslmode=require`
to the URI:

CopyExpand

```
postgresql://<username>:<password>@hostname:<port>/<database_name>?sslmode=require
```

Show lessSee more

Scroll to top

The [sslmode](https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNECT-SSLMODE) parameter will accept different values
indicating different levels of SSL encryption and certificate verification to be used. `sslmode=require` is the minimum level
required to enforce SSL encryption. For configuring your client to perform SSL certificate verification of your Snowflake Postgres server
certificates, see [Snowflake Postgres SSL certificates](postgres-ssl-certs).

You can specify several other client connection parameters in a connection URI in the same way as `sslmode` is
specified above. For a full list, see the PostgreSQL documentation’s [list of URI connection parameters](https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-PARAMKEYWORDS).

You can also set many of these parameters via [environment variables recognized by libpq](https://www.postgresql.org/docs/current/libpq-envars.html). For example, the following ensures that the `psql` connection is made with
`?sslmode=require` set:

CopyExpand

```
export PGSSLMODE=require
psql -h {hostname} -U {username} {dbname}
```

Show lessSee more

Scroll to top

Setting client connection parameters via environment variables is useful when configuring connections for application frameworks that
do not otherwise provide configuration options for needed connection parameters.

> Note
>
> For applications that use non-`libpq`-based database drivers, consult the documentation for those other drivers for their
> client configuration parameter options and specification format. For example, [PostgreSQL’s JDBC driver](https://jdbc.postgresql.org/documentation/use/) provides many parameters equivalent to those provided by `libpq`, but their
> specification in URIs is slightly different.
