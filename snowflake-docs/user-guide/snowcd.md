---
title: "SnowCD (Connectivity Diagnostic Tool)"
url: "https://docs.snowflake.com/en/user-guide/snowcd"
---

# SnowCD (Connectivity Diagnostic Tool)

SnowCD (i.e. Snowflake Connectivity Diagnostic Tool) helps users to diagnose and troubleshoot their network connection to Snowflake.

## Overview

SnowCD leverages the Snowflake hostname IP addresses and ports listed by either the `SYSTEM$ALLOWLIST()` or `SYSTEM$ALLOWLIST_PRIVATELINK()` functions to run a series of connection checks to evaluate and help troubleshoot the network connection to Snowflake.

Important

If your Snowflake account uses private connectivity to the Snowflake service, execute the
[SYSTEM$ALLOWLIST\_PRIVATELINK](../sql-reference/functions/system_allowlist_privatelink) function to obtain the Snowflake hostname IP address and ports to evaluate
and troubleshoot network connections to Snowflake.

For more information, see:

* [AWS PrivateLink and Snowflake](admin-security-privatelink)
* [Azure Private Link and Snowflake](privatelink-azure)
* [Google Cloud Private Service Connect and Snowflake](private-service-connect-google)

SnowCD returns one of the following:

> 1. `All checks passed` to indicate a healthy network connection.
> 2. A message to state that one or more checks failed with a troubleshooting suggestion.

Users can leverage SnowCD to evaluate the network connection to Snowflake at any time to verify the required configuration settings are correct. For example, users can integrate SnowCD into these use cases:

> 1. Automated deployment scripts.
> 2. A prerequisite check before deploying a service that connects to Snowflake.
> 3. Environment checks while starting a new machine.
> 4. Periodic checks on running machines.

SnowCD works with either direct connections or connections through proxy servers.

SnowCD checks access to the Snowflake database and to stages used to temporarily store
data (for example, for loading).

SnowCD verifies that an HTTP response was returned from the HTTP host. This can detect
problems such as the following:

* No HTTP server is running at the specified IP address and port.
* There was a DNS (Domain Name System) lookup failure.
* A Man-In-The-Middle attack occurred and used an invalid certificate to impersonate
  the desired service.
* Certain types of other network failures below the HTTP level.

SnowCD does not detect all possible problems. The known limitations include:

* Stages require additional authentication information that SnowCD does not have.
  Although SnowCD verifies basic access to a stage, SnowCD does not perform a strict
  check on the HTTP response code from the stage. Therefore, SnowCD does not detect
  problems such as:

  + Access policy denial for Amazon S3 Bucket, Azure Blob storage, or Google Cloud Storage
    for stages.
  + There is a problem connecting to the customer’s proxy server, for example the proxy server returns an HTTP
    403 error.

Because SnowCD does not detect all possible problems, Snowflake recommends that after you successfully verify access to a stage
through SnowCD, you follow up by running a PUT command to load a file to the stage.
The simplest way to run a PUT command is usually through SnowSQL.

Attention

Troubleshooting one or more network connection issues is challenging. Depending on the environment, it may be necessary to use SnowCD with other troubleshooting approaches. For example, if SnowCD returns information on an OCSP issue, consult the OCSP sections on this page.

## Using SnowCD

### Step 1: Run the SYSTEM$ALLOWLIST or SYSTEM$ALLOWLIST\_PRIVATELINK Function

This is a prerequisite step and needs to be completed once unless the hostnames or ports change.

1. Connect to Snowflake through the web interface.
2. Execute `SELECT SYSTEM$ALLOWLIST();` or `SELECT SYSTEM$ALLOWLIST_PRIVATELINK();`.
3. Save the query result to a file (i.e. `allowlist.json`).

For more information about these functions, see [SYSTEM$ALLOWLIST](../sql-reference/functions/system_allowlist) or
[SYSTEM$ALLOWLIST\_PRIVATELINK](../sql-reference/functions/system_allowlist_privatelink).

The examples below use JSON as the output format from the corresponding SQL function. SnowCD accepts either JSON, CSV, or TSV format as its input for [Step 3: Run SnowCD](#step-3-run-snowcd).

To save the query result in CSV or TSV format, in the web interface, click the Download or View Results icon, select either CSV or TSV, and click Export.

**Example file (Not indented and redacted)**

Execute [SYSTEM$ALLOWLIST](../sql-reference/functions/system_allowlist) and save the output to a file (e.g. `allowlist.json`).

> Where:
>
> > `<storage_location>` is the storage location (Amazon S3, Google Cloud Storage, or Microsoft Azure) that stores the file that a Snowflake
> > client can read or write.
> >
> > `<region_id>` is the AWS region where your VPCs and Snowflake account are located.
>
> CopyExpand
>
> ```
> [{"type":"STAGE","host":"<storage_location>.s3.<region_id>.amazonaws.com","port":443},
> {"type":"STAGE","host":"<storage_location>.s3-<region_id>.amazonaws.com","port":443},
> {"type":"STAGE","host":"<storage_location>.s3.amazonaws.com","port":443},
> {"type":"SNOWSQL_REPO","host":"<repository_name_1>.s3.<region_id>.amazonaws.com","port":443},
> {"type":"SNOWSQL_REPO","host":"<repository_name_2>.snowflakecomputing.com","port":443},
> {"type":"OUT_OF_BAND_TELEMETRY","host":"<telemetry_subdomain>.snowflakecomputing.com","port":443},
> {"type":"OCSP_CACHE","host":"ocsp.snowflakecomputing.com","port":80},
> {"type":"OCSP_RESPONDER","host":"ocsp.digicert.com","port":80}]
> ```
>
> Show lessSee more
>
> Scroll to top

**Example file (Indented and redacted)**

Execute [SYSTEM$ALLOWLIST](../sql-reference/functions/system_allowlist) and save the output to a file (e.g. `allowlist.json`).

> Where:
>
> > `<storage_location>` is the storage location (Amazon S3, Google Cloud Storage, or Microsoft Azure) that stores the file that a Snowflake
> > client can read or write.
> >
> > `<region_id>` is the AWS region where your VPCs and Snowflake account are located.
>
> CopyExpand
>
> ```
> [{
>   "type": "STAGE",
>   "host": "<storage_location>.s3.<region_id>.amazonaws.com",
>   "port": 443
> }, {
>   "type": "STAGE",
>   "host": "<storage_location>.s3-<region_id>.amazonaws.com",
>   "port": 443
> }, {
>   "type": "STAGE",
>   "host": "<storage_location>.s3.amazonaws.com",
>   "port": 443
> }, {
>   "type": "SNOWSQL_REPO",
>   "host": "<repository_name_1>.s3.<region_id>.amazonaws.com",
>   "port": 443
> }, {
>   "type": "SNOWSQL_REPO",
>   "host": "<repository_name_2>.snowflakecomputing.com",
>   "port": 443
> }, {
>   "type": "OUT_OF_BAND_TELEMETRY",
>   "host": "<telemetry_subdomain>.snowflakecomputing.com",
>   "port": 443
> }, {
>   "type": "OCSP_CACHE",
>   "host": "ocsp.snowflakecomputing.com",
>   "port": 80
> }, {
>   "type": "OCSP_RESPONDER",
>   "host": "ocsp.digicert.com",
>   "port": 80
> }]
> ```
>
> Show lessSee more
>
> Scroll to top

**Example file (Not indented and redacted)**

Execute [SYSTEM$ALLOWLIST\_PRIVATELINK](../sql-reference/functions/system_allowlist_privatelink) and save the output to a file (e.g. `allowlist.json`).

> Where:
>
> > `<storage_location>` is the storage location (Amazon S3, Google Cloud Storage, or Microsoft Azure) that stores the file that a Snowflake
> > client can read or write.
> >
> > `<region_id>` is the AWS region where your VPCs and Snowflake account are located.
> >
> > CopyExpand
> >
> > ```
> > [{"type":"SNOWFLAKE_DEPLOYMENT","host":"<storage_location>.<region>.privatelink.snowflakecomputing.com","port":443},
> > {"type":"STAGE","host":"<storage_location>.<region>.amazonaws.com","port":443},
> > {"type":"STAGE","host":"<storage_location>-<region>.amazonaws.com","port":443},
> > {"type":"STAGE","host":"<storage_location>.amazonaws.com","port":443},
> > {"type":"SNOWSQL_REPO","host":"<repository_name_1>.s3.<region>.amazonaws.com","port":443},
> > {"type":"SNOWSQL_REPO","host":"<repository_name_2>.snowflakecomputing.com","port":443},
> > {"type":"OUT_OF_BAND_TELEMETRY","host":"<telemetry_subdomain>.snowflakecomputing.com","port":443},
> > {"type":"OCSP_CACHE","host":"ocsp.<storage_location>.<region>.privatelink.snowflakecomputing.com","port":80}]
> > ```
> >
> > Show lessSee more
> >
> > Scroll to top

**Example file (Indented and redacted)**

Execute [SYSTEM$ALLOWLIST\_PRIVATELINK](../sql-reference/functions/system_allowlist_privatelink) and save the output to a file (e.g. `allowlist.json`).

> Where:
>
> > `<storage_location>` is the storage location (Amazon S3, Google Cloud Storage, or Microsoft Azure) that stores the file that a Snowflake
> > client can read or write.
> >
> > `<region_id>` is the AWS region where your VPCs and Snowflake account are located.
> >
> > CopyExpand
> >
> > ```
> > [{
> >   "type": "SNOWFLAKE_DEPLOYMENT",
> >   "host": "<storage_location>.<region>.privatelink.snowflakecomputing.com",
> >   "port": 443
> > }, {
> >   "type": "STAGE",
> >   "host": "<storage_location>.<region>.amazonaws.com",
> >   "port": 443
> > }, {
> >   "type": "STAGE",
> >   "host": "<storage_location>-<region>.amazonaws.com",
> >   "port": 443
> > }, {
> >   "type": "STAGE",
> >   "host": "<storage_location>.amazonaws.com",
> >   "port": 443
> > }, {
> >   "type": "SNOWSQL_REPO",
> >   "host": "<repository_name_1>.s3.<region>.amazonaws.com",
> >   "port": 443
> > }, {
> >   "type": "SNOWSQL_REPO",
> >   "host": "<repository_name_2>.snowflakecomputing.com",
> >   "port": 443
> > }, {
> >   "type": "OUT_OF_BAND_TELEMETRY",
> >   "host": "<telemetry_subdomain>.snowflakecomputing.com",
> >   "port": 443
> > }, {
> >   "type": "OCSP_CACHE",
> >   "host": "ocsp.<storage_location>.<region>.privatelink.snowflakecomputing.com",
> >   "port": 80
> > }]
> > ```
> >
> > Show lessSee more
> >
> > Scroll to top

Attention

Save the `allowlist.json` file in the location where other external allowed hostnames and ports are defined for your environment.

Tip

If you do not want the output in JSON format and instead prefer table format, execute the following:

CopyExpand

```
use warehouse my_warehouse;
select value:type as type,
       value:host as host,
       value:port as port
   from table(flatten(input => parse_json(system$allowlist())));
```

Show lessSee more

Scroll to top

### Step 2: Download and Install SnowCD

#### Linux

To download and install SnowCD on Linux, complete the following steps:

1. Download the latest version of the SnowCD from the [SnowCD Download](https://developers.snowflake.com/snowcd/) page.
2. Open the Linux Terminal application and navigate to the directory where you downloaded the file.
3. Verify the SHA256 checksum matches.

   > CopyExpand
   >
   > ```
   > $ sha256sum <filename>
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
4. Extract the file.

   > CopyExpand
   >
   > ```
   > $ gunzip <filename>
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
5. Make the file executable.

   > CopyExpand
   >
   > ```
   > $ chmod +x <filename>
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
6. Rename the executable to `snowcd`.

   > CopyExpand
   >
   > ```
   > $ mv <filename> snowcd
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

Note

Linux users running RHEL or CentOS can install SnowCD using yum while Debian users can install using apt.

#### macOS

To download and install SnowCD on macOS, complete the following steps:

1. Download the latest version of the notarized SnowCD `pkg` file from the [SnowCD Download](https://developers.snowflake.com/snowcd/) page.

   The pkg files use the following naming convention:

   > snowcd-<version\_number>-darwin\_x86\_64.pkg

   For example:

   > snowcd-1.0.5-darwin\_x86\_64.pkg
2. Open the Terminal application and navigate to the directory where you downloaded the file.
3. Verify the SHA256 checksum matches.

   To get the checksum of the file, execute the command:

   CopyExpand

   ```
   $ shasum -a 256 <filename>
   ```

   Show lessSee more

   Scroll to top

   Compare the checksum of the file to the checksum shown at the download site.
4. Open the Finder application and navigate to the directory where you downloaded the pkg file.
5. Extract and install SnowCD by double clicking on the pkg file.

The files, including the snowcd executable, are installed in the /opt/snowflake/snowcd directory.

#### Windows

To download and install SnowCD on Windows, complete the following steps:

1. Download the latest version of the SnowCD from the [SnowCD Download](https://developers.snowflake.com/snowcd/) page.
2. Run the MSI file using the Windows Installer.

### Step 3: Run SnowCD

Before running SnowCD in macOS and Linux environments, you can add its directory to your `$PATH`. In Windows environments, you can add SnowCD to your Environment Variables.

1. In macOS or Linux environments, you can run the snowcd executable from the
   command line by executing `snowcd <path_to_allowlist.json> [flags]`.
2. In Windows environments, execute `snowcd.exe <path_to_allowlist.json> [flags]`.

Tip

For a full description on the flags `snowcd` supports, execute `snowcd -h`.

If all checks are valid, SnowCD returns the number of checks on the number of hosts with the message `All checks passed` as follows.

CopyExpand

```
Performing 30 checks on 12 hosts
All checks passed
```

Show lessSee more

Scroll to top

If you try to run SnowCD without passing in the JSON allow list information from SELECT SYSTEM$ALLOWLIST(), the following error message displays as a reminder to include the file, with the list of currently supported flags, their data type where applicable, and a brief description of the flag.

CopyExpand

```
Error: please provide whitelist generated by SYSTEM$ALLOWLIST()
Usage:
./snowcd <path to input json file> [flags]

Examples:
./snowcd test.json

Flags:
  -h, --help                   help for ./snowcd
  --logLevel string            log level (panic, fatal[default], error, warning, info, debug, trace) (default "fatal")
  --logPath string             Output directory for log. When not specified, no log is generated
  --proxyHost string           host for http proxy. (When not specified, does not use proxy at all)
  --proxyIsHTTPS               Is connection to proxy secure, i.e. https. (default false)
  --proxyPassword string       password for http proxy.(default empty)
  --proxyPort int              port for http proxy.(default 8080) (default 8080)
  --proxyUser string           user name for http proxy.(default empty)
  -t, --timeout int            timeout for each hostname's checks in seconds (default 5) (default 5)
  --version                    version for ./snowcd
```

Show lessSee more

Scroll to top

If SnowCD detects an incorrect setting or configuration, information on the failed check(s) displays with a
troubleshooting suggestion. For example, the response below indicates an invalid hostname.

CopyExpand

```
Check for 1 hosts failed, display as follow:
==============================================
Host: www.google1.com
Port: 443
Type: SNOWFLAKE_DEPLOYMENT
Failed Check: DNS Check
Error: lookup www.google1.com: no such host
Suggestion: Check your configuration on DNS server
```

Show lessSee more

Scroll to top

### Using SnowCD with an HTTP Proxy

SnowCD can be run against an HTTP proxy to determine its connectivity status.

Important

Currently, Snowflake does not support SSL-terminating proxy servers.

During the configuration of your firewall and proxy allow list, use SSL pass through (i.e. bypass SSL decryption).

Using Linux as a representative example, execute the following command to run SnowCD against a proxy, replacing the
flag values where necessary.

CopyExpand

```
snowcd allowlist.json \
  --proxyHost <hostname> \
  --proxyPort <port_number> \
  --proxyUser <username> \
  --proxyPassword <password>
```

Show lessSee more

Scroll to top

Logging is optional and you can add the two logging flags to the proxy command. It is important to include a path to the log
file to ensure logging occurs when running the command.

CopyExpand

```
snowcd allowlist.json \
  --proxyHost <hostname> \
  --proxyPort <port_number> \
  --proxyUser <username> \
  --proxyPassword <password> \
  --logLevel trace \
  --logPath test.log
```

Show lessSee more

Scroll to top

After executing this command, you can view the trace in the `test.log` file.
