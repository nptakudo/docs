---
title: "VerifyContentPGP 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/verifycontentpgp"
---

# VerifyContentPGP 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-pgp-nar

## Description

Verify signatures using OpenPGP Public Keys

## Tags

Encryption, GPG, OpenPGP, PGP, RFC 4880, Signing

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| public-key-service | PGP Public Key Service for verifying signatures with Public Key Encryption |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | Signature Verification Failed |
| success | Signature Verification Succeeded |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| pgp.literal.data.filename | Filename from Literal Data |
| pgp.literal.data.modified | Modified Date Time from Literal Data in milliseconds |
| pgp.signature.created | Signature Creation Time in milliseconds |
| pgp.signature.algorithm | Signature Algorithm including key and hash algorithm names |
| pgp.signature.hash.algorithm.id | Signature Hash Algorithm Identifier |
| pgp.signature.key.algorithm.id | Signature Key Algorithm Identifier |
| pgp.signature.key.id | Signature Public Key Identifier |
| pgp.signature.type.id | Signature Type Identifier |
| pgp.signature.version | Signature Version Number |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.pgp.DecryptContentPGP](decryptcontentpgp)
* [org.apache.nifi.processors.pgp.EncryptContentPGP](encryptcontentpgp)
* [org.apache.nifi.processors.pgp.SignContentPGP](signcontentpgp)
