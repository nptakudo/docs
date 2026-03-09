---
title: "Snowflake generation 2 standard warehouses"
url: "https://docs.snowflake.com/en/user-guide/warehouses-gen2"
---

# Snowflake generation 2 standard warehouses

Generation 2 Standard Warehouse (Gen2) is an updated version (the “next generation”) of the
current standard virtual warehouse in Snowflake, focused on improving performance for
analytics and data engineering workloads. Gen2 is built on top of faster underlying hardware
and intelligent software optimizations, such as enhancements to delete, update, and merge operations,
and table scan operations. With Gen2, you can expect the majority of queries finish faster, and you can do more work
at the same time. The exact details depend on your configuration and workload. Conduct tests to verify how much this feature
improves your costs, performance, or both.

You can specify the generation for standard warehouses in the [CREATE WAREHOUSE](../sql-reference/sql/create-warehouse)
or [ALTER WAREHOUSE](../sql-reference/sql/alter-warehouse) commands, using either the GENERATION clause or the RESOURCE\_CONSTRAINT clause:

**Using GENERATION clause (recommended):**

* `GENERATION = '1'` represents Snowflake’s original, industry-leading standard virtual warehouses.
* `GENERATION = '2'` represents the next generation of Snowflake’s standard virtual warehouses.

**Using RESOURCE\_CONSTRAINT clause:**

* STANDARD\_GEN\_1 represents Snowflake’s original, industry-leading standard virtual warehouses.
* STANDARD\_GEN\_2 represents the next generation of Snowflake’s standard virtual warehouses.

Note

Currently, the GENERATION clause and the
STANDARD\_GEN\_1 and STANDARD\_GEN\_2 values aren’t available in Snowsight. You must specify them
with SQL commands.

Generation 2 standard warehouses aren’t available for warehouse sizes X5LARGE and X6LARGE.

This feature applies to standard warehouses. It doesn’t apply to Snowpark-optimized warehouses.

STANDARD\_GEN\_1 provides the same memory capacity for standard warehouses as MEMORY\_1X does
for Snowpark-optimized warehouses.

## Default value for the RESOURCE\_CONSTRAINT for standard warehouses

For the following regions, any account associated with a new organization created after June 27th, 2025 will have standard
warehouses default to Gen2:

* AWS US West (Oregon)
* AWS EU (Frankfurt)
* Azure East US 2 (Virginia)
* Azure West Europe (Netherlands)

For all other regions where Gen2 warehouses are available, all new organizations created after July 15th, 2025 will have standard
warehouses default to Gen2. For information about region availability, see
[Region availability](#label-gen-2-standard-warehouses-region-availability).

For any regions or organizations where the preceding factors don’t apply, if you don’t specify the GENERATION or RESOURCE\_CONSTRAINT clause when
you create a standard warehouse, Snowflake creates a Gen1 standard warehouse.

## Changing a warehouse to or from a generation 2 warehouse

You can alter a standard warehouse and specify a different GENERATION clause or RESOURCE\_CONSTRAINT clause to change
it from generation 1 to generation 2, or from generation 2 to generation 1. You can make that change
whether the warehouse is running or suspended.

You can also switch between a Gen2 standard warehouse and a Snowpark-optimized warehouse by
changing the value of the WAREHOUSE\_TYPE and RESOURCE\_CONSTRAINT clauses. You can make that change
whether the warehouse is running or suspended. Note that the GENERATION clause applies only to standard warehouses
and cannot be used with Snowpark-optimized warehouses.

Note

When you convert a Gen1 warehouse to Gen2 without suspending it first, existing queries that were running on Gen1 continue to run
to completion using the Gen1 compute resources. At the same time, the warehouse runs any new queries on the Gen2 compute
resources. While the existing queries are running, you are charged for both sets of compute resources. The warehouse doesn’t
automatically suspend during this period, whether or not any queries are using the Gen2 compute resources. When the existing
queries complete, the workload shifts entirely to the Gen2 compute resources. Therefore, you can maximize availability by
converting the warehouse while it’s running. Or, you can reduce costs by converting the warehouse while it’s suspended and no
queries are running.

The same consideration applies to converting between standard and Snowpark-optimized warehouses, or any other change
to the RESOURCE\_CONSTRAINT property. Existing queries will complete on the warehouse they began on and with the
RESOURCE\_CONSTRAINT that was in effect at the initialization of the query, while new queries will operate on the new warehouse
type or the new RESOURCE\_CONSTRAINT that you set.

You can see the setting for a standard warehouse in the `"resource_constraint"` column of
the SHOW WAREHOUSES output.

This setting isn’t reflected in the INFORMATION\_SCHEMA views for warehouses.

## Region availability

Gen2 standard warehouses are available for the Amazon Web Services (AWS), Microsoft Azure, and Google Cloud Platform (GCP)
cloud service providers (CSPs).

Gen2 standard warehouses are available in all [CSP regions](intro-regions),
with some exceptions. Currently, Gen2 standard warehouses *aren’t* available in these CSP regions:

* AWS EU (Zurich)
* AWS Africa (Cape Town)
* GCP Middle East Central2 (Dammam)
* Azure US Gov Virginia (FedRAMP High Plus)
* Azure US Gov Virginia

Important

If you use account replication for your warehouses, and you create any Gen2 warehouses, any secondary regions must
also have Gen2 warehouse support. Otherwise, the Gen2 warehouses might not be able to resume in the
secondary regions after a failover. Make sure to test that any Gen2 warehouses can be resumed in secondary regions.

The defaults for Snowflake standard warehouses are changing, based on the availability of Gen2 standard warehouses. Currently, the
default value of the RESOURCE\_CONSTRAINT property depends on your organization and the CSP region of your account. For more
information, see [Default value for the RESOURCE\_CONSTRAINT for standard warehouses](#label-gen-2-standard-warehouses-default-generation).

## Cost and billing for Gen2 standard warehouses

For general information about credit usage with Snowflake virtual warehouses,
see [Virtual warehouse credit usage](cost-understanding-compute.html#label-virtual-warehouse-credit-usage).

For information about credit consumption for Gen2 standard warehouses,
see the [Snowflake Service Consumption Table](https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf).

## Examples

The following examples show how you can specify Gen2 standard warehouses when creating a new
warehouse or altering an existing one. The examples show variations such as changing the warehouse size,
type, and memory capacity at the same time.

### Examples using GENERATION clause (recommended approach)

The following example creates a Gen2 warehouse with all other properties left as defaults. The warehouse
type is STANDARD and the size is XSMALL. Those defaults are the same for both generation 1 and generation 2
standard warehouses.

CopyExpand

```
CREATE OR REPLACE WAREHOUSE next_generation_default_size
  GENERATION = '2';
```

Show lessSee more

Scroll to top

The following example creates a Gen2 standard warehouse with size SMALL.

CopyExpand

```
CREATE OR REPLACE WAREHOUSE next_generation_size_small
  GENERATION = '2'
  WAREHOUSE_SIZE = SMALL;
```

Show lessSee more

Scroll to top

### Examples using RESOURCE\_CONSTRAINT clause

The following example creates a Gen2 warehouse using the RESOURCE\_CONSTRAINT syntax:

CopyExpand

```
CREATE OR REPLACE WAREHOUSE next_generation_default_size
  RESOURCE_CONSTRAINT = STANDARD_GEN_2;
```

Show lessSee more

Scroll to top

The following example creates a Gen2 standard warehouse with size SMALL.

CopyExpand

```
CREATE OR REPLACE WAREHOUSE next_generation_size_small
  RESOURCE_CONSTRAINT = STANDARD_GEN_2
  WAREHOUSE_SIZE = SMALL;
```

Show lessSee more

Scroll to top

### Examples of converting between generations

The following example shows how to convert a generation 1 standard warehouse to generation 2. The
warehouse size remains the same, XLARGE, throughout the operation. This example uses the
GENERATION clause (recommended):

CopyExpand

```
CREATE OR REPLACE WAREHOUSE old_to_new_xlarge_gen
  WAREHOUSE_SIZE = XLARGE;

ALTER WAREHOUSE old_to_new_xlarge_gen
  SET GENERATION = '2';
```

Show lessSee more

Scroll to top

The following example shows the same conversion using the RESOURCE\_CONSTRAINT clause:

CopyExpand

```
CREATE OR REPLACE WAREHOUSE old_to_new_xlarge
  WAREHOUSE_SIZE = XLARGE;

ALTER WAREHOUSE old_to_new_xlarge
  SET RESOURCE_CONSTRAINT = STANDARD_GEN_2;
```

Show lessSee more

Scroll to top

### Examples of converting to or from Snowpark-optimized warehouses

The following example shows how to convert a Gen2 standard warehouse to Snowpark-optimized.
Snowpark-optimized warehouses currently aren’t available as Gen2 warehouses. Because the warehouse
has size XSMALL when it has the type STANDARD, we specify a RESOURCE\_CONSTRAINT value of MEMORY\_1X.
That RESOURCE\_CONSTRAINT produces a memory size that’s compatible with Snowpark-optimized warehouses
of XSMALL size.

CopyExpand

```
CREATE OR REPLACE WAREHOUSE gen2_to_snowpark_optimized
  RESOURCE_CONSTRAINT = STANDARD_GEN_2;

ALTER WAREHOUSE gen2_to_snowpark_optimized
  SET WAREHOUSE_TYPE = 'SNOWPARK-OPTIMIZED' RESOURCE_CONSTRAINT = MEMORY_1X;
```

Show lessSee more

Scroll to top

The following example shows how to convert a Snowpark-optimized warehouse to a standard Gen2
warehouse. The Snowpark-optimized warehouse starts with size MEDIUM and a relatively large memory
capacity represented by a RESOURCE\_CONSTRAINT value of MEMORY\_16X. After the change, the warehouse
is of type STANDARD, still with size MEDIUM. However, its memory capacity is lower. That’s because
the RESOURCE\_CONSTRAINT value of STANDARD\_GEN\_2 has the same memory capacity as a Snowpark-optimized
warehouse with a resource constraint of MEMORY\_1X.

CopyExpand

```
CREATE OR REPLACE WAREHOUSE snowpark_optimized_medium_to_gen2
  WAREHOUSE_TYPE = 'SNOWPARK-OPTIMIZED'
  WAREHOUSE_SIZE = MEDIUM
  RESOURCE_CONSTRAINT = MEMORY_16X;

ALTER WAREHOUSE snowpark_optimized_medium_to_gen2
  SET WAREHOUSE_TYPE = STANDARD GENERATION = '2';
```

Show lessSee more

Scroll to top
