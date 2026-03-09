---
title: "Troubleshooting the Openflow Connector for Oracle"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/oracle/troubleshoot"
---

# Troubleshooting the Openflow Connector for Oracle

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

Note

The Openflow Connector for Oracle is also subject to additional terms of service beyond the standard
connector terms of service. For more information, see the
[Openflow Connector for Oracle Addendum](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/openflow-oracle-terms/).

This topic describes how to troubleshoot common issues with the
Openflow Connector for Oracle.

## A table was added to replication but doesn’t appear in Snowflake

The table’s fully qualified name (FQN) may be incorrectly specified in the connector
configuration.

**Solution**

* Check the format of the FQN in `Oracle Ingestion Parameters`. It should be
  `<database_name>.<schema_name>.<table_name>` (note the database prefix).
* Check the database name in `Oracle Source Parameters` » `Oracle Connection URL`.
  While FQNs support specifying the name of the database, currently data must reside in
  the same database instance as the one used for this connection.
* Verify that you have provided the full database name including the domain name in the
  connector configuration. For example, use `MYDB.EXAMPLE.COM` instead of just `MYDB`.

  To find the correct database name, run the following query on your Oracle database:

  CopyExpand

  ```
  SELECT property_value
    FROM database_properties
    WHERE property_name = 'GLOBAL_DB_NAME';
  ```

  Show lessSee more

  Scroll to top

  In general, `property_value` is the same as the service name of the database.
  However, the returned database name might include an appended domain name (for
  example, for service name `FOO`, the query might return `FOO.EXAMPLE.COM`). In
  that case, use the full name with the domain (double-quoted, because it contains dots).

## No changes in incremental load

The incremental load is not capturing or applying changes from the source database.

**Solution**

Run the verification for the **Read Oracle CDC Stream** processor:

1. In your Openflow runtime, double-click the Oracle flow.
2. Double-click the process group named Incremental Load.
3. Find the Read Oracle CDC Stream processor.

   1. If it is running, right-click and select Stop. The processor must be stopped
      before you can verify its configuration.
4. Right-click Read Oracle CDC Stream again, then select Configure.
5. Select the Properties tab.
6. Select the Verification checkmark icon in the upper-right corner.
7. In the popup window that appears, select Verify in the lower-right corner.

   The results of the verification procedure appear below. The procedure validates
   database connectivity and checks the status of the components required for incremental
   load to work.

If any of the verification steps fail, view the error message, fix the issue, and run the
verification again. The following sections describe specific issues and solutions.

## Capture Status not ENABLED

The capture process status is `DISABLED` or `ABORTED`. A `DISABLED` status means the
capture process was stopped manually (with `DBMS_XSTREAM_ADM.STOP_OUTBOUND`) or the
database was restarted. An `ABORTED` status means the capture encountered an error,
usually because redo logs needed for the capture process have been deleted.
You can confirm this by checking the System Change Number (SCN) position or querying
the capture status.

**Solution**

Start the outbound server:

CopyExpand

```
BEGIN
   DBMS_XSTREAM_ADM.START_OUTBOUND('XOUT1');
END;
/
```

Show lessSee more

Scroll to top

## UNKNOWN status of LogMiner session

The LogMiner status is `UNKNOWN`, which means that archived logs that LogMiner depended
on were deleted. You can confirm this by querying `V$ARCHIVED_LOG` and checking for rows
where the DELETED column has value YES.

**Solution**

Recreate the XStream outbound server. For more information, see [Problems occur with the XStream outbound server](#label-recreate-xstream-outbound-server)

## WAITING FOR REDO status of XStream capture

The XStream capture status shows
`WAITING FOR REDO: FILE NA, THREAD 1, SEQUENCE 47, SCN 0x0000000000190ac4`.
This means LogMiner is waiting for an archived log file that is not available because it
was deleted. You can confirm this by querying `V$ARCHIVED_LOG` and checking for rows
where the DELETED column has value YES.

**Solution**

Recreate the XStream outbound server. For more information, see [Problems occur with the XStream outbound server](#label-recreate-xstream-outbound-server)

## XStream capture rules are incorrect

XStream is not configured to capture changes from the expected schemas or tables.

**Solution**

Verify the capture rules by running the following query:

CopyExpand

```
SELECT STREAMS_NAME, SCHEMA_NAME, OBJECT_NAME, RULE_TYPE
FROM DBA_XSTREAM_RULES
WHERE STREAMS_NAME = 'XOUT1';
```

Show lessSee more

Scroll to top

You can also query the capture status and error message directly:

CopyExpand

```
SELECT CLIENT_NAME, STATUS, ERROR_MESSAGE FROM ALL_CAPTURE;
```

Show lessSee more

Scroll to top

This query returns:

* `CLIENT_NAME`: The name of the XStream client (outbound server).
* `STATUS`: The current status of the capture process (for example, `ENABLED`,
  `DISABLED`, `ABORTED`).
* `ERROR_MESSAGE`: Any error message associated with the capture process.

## Error ORA-21560: argument last\_position is null, invalid, or out of range

The connector attempted to connect to an SCN position for which redo logs are no longer
available.

**Solution**

Confirm the issue by running the following query. The SCN for
`Last SCN processed by XStream` must be higher than the lowest SCN for which redo logs
exist.

CopyExpand

```
SELECT min(FIRST_CHANGE#) as SCN,
       'Lowest SCN for which redo logs still exist' AS DESCRIPTION
FROM V$ARCHIVED_LOG
WHERE DELETED = 'NO'
UNION ALL
SELECT PROCESSED_LOW_SCN,
       'Last SCN processed by XStream'
FROM DBA_XSTREAM_OUTBOUND_PROGRESS
WHERE SERVER_NAME = 'XOUT1'
ORDER BY SCN;
```

Show lessSee more

Scroll to top

To recover from this error, recreate the XStream outbound server. For more information,
see [Problems occur with the XStream outbound server](#label-recreate-xstream-outbound-server)

## Error ORA-26701: Streams process XOUT1 does not exist

The XStream outbound server cannot be found on the database instance.

**Solution**

Verify the following:

* The database name in `Oracle Source Parameters` » `XStream Out Server URL` points
  to the database instance with the XStream outbound server, not a different PDB.
* XStream has been created on this instance and has the same name.

## Error ORA-01722: invalid number when creating the outbound server

Executing `DBMS_XSTREAM_ADM.CREATE_OUTBOUND` fails with:

CopyExpand

```
ORA-01722: invalid number
ORA-06512: at "SYS.DBMS_LOGREP_UTIL", line 582
ORA-06512: at "SYS.DBMS_LOGREP_UTIL", line 636
ORA-06512: at "SYS.DBMS_XSTREAM_ADM_UTL", line 440
ORA-06512: at "SYS.DBMS_XSTREAM_UTL_IVK", line 2094
ORA-06512: at "SYS.DBMS_XSTREAM_UTL_IVK", line 2302
ORA-06512: at "SYS.DBMS_XSTREAM_ADM", line 44
ORA-06512: at line 8
```

Show lessSee more

Scroll to top

This error is misleading. The outbound server already exists.

**Solution**

No action is needed. Use the existing outbound server.

## Problems occur with the XStream outbound server

Multiple issues, such as deleted redo logs or corrupted LogMiner state, can be resolved
by recreating the XStream outbound server.

**Solution**

1. Drop the existing outbound server:

   CopyExpand

   ```
   BEGIN
      DBMS_XSTREAM_ADM.DROP_OUTBOUND('XOUT1');
   END;
   /
   ```

   Show lessSee more

   Scroll to top
2. Create the outbound server again. For more information, see
   [Create XStream Outbound Server](setup-oracledb.html#label-create-xstream-outbound-server).
