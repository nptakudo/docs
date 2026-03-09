---
title: "Cross-region inference"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/cross-region-inference"
---

# Cross-region inference

Inference is the process of using a machine learning model to get an output based on a user input. For example, when you call the
SNOWFLAKE.CORTEX.COMPLETE function, you are requesting an inference from the LLM with your prompt as the input. In Snowflake, you can
configure your account to allow cross-region inference processing with the [CORTEX\_ENABLED\_CROSS\_REGION](../../sql-reference/parameters.html#label-cortex-enable-cross-region)
parameter. This parameter enables inference requests to be processed in a different region from the default region.
The cross-region inference parameter is used to determine the inference behavior for any Snowflake feature supported by
cross-region inference, including Cortex LLM Functions.

When enabled, cross-region inference occurs if the LLM or feature is not supported in your default region.

By default, the parameter is set to DISABLED. This allows requests to be processed only in the default region.
You can specify the regions you want to allow cross-region inference to using the [ALTER ACCOUNT](../../sql-reference/sql/alter-account) command.

For details on this parameter, see [CORTEX\_ENABLED\_CROSS\_REGION](../../sql-reference/parameters.html#label-cortex-enable-cross-region).

## Access control requirements

This parameter can only be set at the account level, not at the user or session levels. Only the ACCOUNTADMIN role can set the parameter
using the [ALTER ACCOUNT](../../sql-reference/sql/alter-account) command:

CopyExpand

```
ALTER ACCOUNT SET CORTEX_ENABLED_CROSS_REGION = 'AWS_US';
```

Show lessSee more

Scroll to top

This parameter cannot be set by the ORGADMIN role.

## How to use the cross-region inference parameter

By default, this parameter is set to `DISABLED`, which means the inference requests are only processed in the default region. The
following examples show how to set the cross-region parameter for various use cases.

### Any region

To allow any of the Snowflake regions that support cross-region inference requests to process your requests, set the parameter to
`'ANY_REGION'`.

CopyExpand

```
ALTER ACCOUNT SET CORTEX_ENABLED_CROSS_REGION = 'ANY_REGION';
```

Show lessSee more

Scroll to top

### Default region only

To process inference requests only in the default region, set this parameter to `'DISABLED'`.

CopyExpand

```
ALTER ACCOUNT SET CORTEX_ENABLED_CROSS_REGION = 'DISABLED';
```

Show lessSee more

Scroll to top

### Specify regions

To allow only specified regions to process your requests, set this parameter to the regions separated by commas. For a full list of
regions, see [CORTEX\_ENABLED\_CROSS\_REGION](../../sql-reference/parameters.html#label-cortex-enable-cross-region).

The following example specifies `AWS_US` and `AWS_EU` regions to process your inference requests:

CopyExpand

```
ALTER ACCOUNT SET CORTEX_ENABLED_CROSS_REGION = 'AWS_US,AWS_EU';
```

Show lessSee more

Scroll to top

### US Commercial Gov regions

Cross-region inference for Snowflake’s government-authorized, FIPS-compliant commercial environments is designed to maintain data-handling boundaries while providing access to supported AI models. When enabled, inference requests remain within the same cloud and compliance boundary, and processing occurs on FIPS-validated infrastructure such as AWS Bedrock FIPS endpoints. This approach allows customers in select U.S. government-authorized regions to use Snowflake AI capabilities securely and without exceptions to compliance policies.

To enable this feature, set the CORTEX\_ENABLED\_CROSS\_REGION parameter to `AWS_US` for workloads in a supported government-authorized region:

CopyExpand

```
ALTER ACCOUNT SET CORTEX_ENABLED_CROSS_REGION = 'AWS_US';
```

Show lessSee more

Scroll to top

Cross-region inference is available for US Commercial Gov in these regions:

* US East (Commercial Gov - N. Virginia)
* US West (Commercial Gov - Oregon)

## Cost considerations

* You are charged credits for the use of LLM as listed in the
  [Snowflake Service Consumption Table](https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf).
  Credits are considered consumed in the requesting region. For example, if you call an LLM Function from the `us-east-2` region and
  the request is processed in the `us-west-2` region, the credits are considered consumed in the `us-east-2` region.
* You do not incur data egress charges for using cross-region inference.

## Considerations

* Latency between regions depends on the cloud provider infrastructure and network status. Snowflake recommends that you test your specific
  use-case with cross-region inference enabled.
* Cross-region inference is not supported in [U.S. SnowGov regions](../intro-regions.html#label-us-gov-regions). This means you cannot make cross-region
  inference requests into or out of the SnowGov regions.
* You can use this setting from GCP or Azure regions to make inference requests for features that are not supported in those regions.
* User inputs, service generated prompts, and outputs are not stored or cached during cross-region inference.
* The data required for the inference request traverses between regions as follows:

  + If both the source and destination regions are in AWS, the data stays within the [AWS global network](https://aws.amazon.com/about-aws/global-infrastructure/).
    All data flowing across the AWS global network that interconnects the data centers and regions is automatically
    encrypted at the physical layer.
  + If both the source and destination regions are in Azure, the traffic stays entirely within the Azure global network. It never enters the public internet.
  + If the regions are on different cloud providers, then the data traverses the public internet using Mutual Transport Layer Security (mTLS).
* Cross-region inference for [Cortex Search](cortex-search/cortex-search-overview) is not supported in [all regions](cortex-search/cortex-search-overview.html#label-cortex-search-overview-regional-availability).

## Next steps

* For details on the cross-region inference parameter, see [CORTEX\_ENABLED\_CROSS\_REGION](../../sql-reference/parameters.html#label-cortex-enable-cross-region) section of the SQL parameter reference.
