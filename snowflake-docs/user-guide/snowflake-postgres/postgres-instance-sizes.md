---
title: "Snowflake Postgres Instance Sizes"
url: "https://docs.snowflake.com/en/user-guide/snowflake-postgres/postgres-instance-sizes"
---

# Snowflake Postgres Instance Sizes

Snowflake Postgres offers three tiers of instances — Burstable, Standard, and Memory — to cover a variety of use cases.

For credit costs for each instance size, see the [Snowflake Service Consumption Table](https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf).

In general:

* **Burstable** instances have a baseline CPU level but can temporarily burst above this baseline.
* **Standard** instances have a good balance of CPU and memory.
* **Memory-optimized** instances have a higher ratio of memory to CPU, which may improve performance for workloads with greater
  memory needs.

## Burstable

*Important notes*

* Burstable instances can be provisioned with a maximum of 100GB storage.
* Burstable instances have burstable vCPUs. Utilization in excess of the CPU baseline shown below will deplete available vCPU
  credits, leading to CPU rate limiting. This may appear as a sudden downgrade in performance with no other cause.
* Burstable instances do not support High Availability standbys.

| Name | Cores | Memory | IOPS | HA supported |
| --- | --- | --- | --- | --- |
| BURST\_XS | 2 | 1GB | 11,800 | No |
| BURST\_S | 2 | 2GB | 11,800 | No |
| BURST\_M | 2 | 4GB | 11,800 | No |

See moreShow less

Expand

## General purpose

| Name | Cores | Memory | IOPS | HA supported |
| --- | --- | --- | --- | --- |
| STANDARD\_M | 1 | 4GB | 20,000 | Yes |
| STANDARD\_L | 2 | 8GB | 40,000 | Yes |
| STANDARD\_XL | 4 | 16GB | 40,000 | Yes |
| STANDARD\_2XL | 8 | 32GB | 40,000 | Yes |
| STANDARD\_4XL | 16 | 64GB | 40,000 | Yes |
| STANDARD\_8XL | 32 | 128GB | 40,000 | Yes |
| STANDARD\_12XL | 48 | 192GB | 60,000 | Yes |
| STANDARD\_24XL | 96 | 384GB | 78,000 | Yes |

See moreShow less

Expand

Note

The STANDARD\_M instance size is not available on Microsoft Azure.

## Memory optimized

| Name | Cores | Memory | IOPS | HA supported |
| --- | --- | --- | --- | --- |
| HIGHMEM\_L | 2 | 16GB | 40,000 | Yes |
| HIGHMEM\_XL | 4 | 32GB | 40,000 | Yes |
| HIGHMEM\_2XL | 8 | 64GB | 40,000 | Yes |
| HIGHMEM\_4XL | 16 | 128GB | 40,000 | Yes |
| HIGHMEM\_8XL | 32 | 256GB | 40,000 | Yes |
| HIGHMEM\_12XL | 48 | 384GB | 78,000 | Yes |
| HIGHMEM\_16XL | 64 | 512GB | 78,000 | Yes |
| HIGHMEM\_24XL | 96 | 768GB | 78,000 | Yes |
| HIGHMEM\_32XL | 128 | 1TB | 78,000 | Yes |
| HIGHMEM\_48XL | 192 | 1.5TB | 78,000 | Yes |

See moreShow less

Expand
