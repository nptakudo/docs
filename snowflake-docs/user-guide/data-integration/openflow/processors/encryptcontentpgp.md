---
title: "EncryptContentPGP 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/encryptcontentpgp"
---

# EncryptContentPGP 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-pgp-nar

## Description

Encrypt contents using OpenPGP. The processor reads input and detects OpenPGP messages to avoid unnecessary additional wrapping in Literal Data packets.

## Tags

Encryption, GPG, OpenPGP, PGP, RFC 4880

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| file-encoding | File Encoding for encryption |
| passphrase | Passphrase used for encrypting data with Password-Based Encryption |
| public-key-search | PGP Public Key Search will be used to match against the User ID or Key ID when formatted as uppercase hexadecimal string of 16 characters |
| public-key-service | PGP Public Key Service for encrypting data with Public Key Encryption |
| symmetric-key-algorithm | Symmetric-Key Algorithm for encryption |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | Encryption Failed |
| success | Encryption Succeeded |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| pgp.symmetric.key.algorithm | Symmetric-Key Algorithm |
| pgp.symmetric.key.algorithm.block.cipher | Symmetric-Key Algorithm Block Cipher |
| pgp.symmetric.key.algorithm.key.size | Symmetric-Key Algorithm Key Size |
| pgp.symmetric.key.algorithm.id | Symmetric-Key Algorithm Identifier |
| pgp.file.encoding | File Encoding |
| pgp.compression.algorithm | Compression Algorithm |
| pgp.compression.algorithm.id | Compression Algorithm Identifier |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.pgp.DecryptContentPGP](decryptcontentpgp)
* [org.apache.nifi.processors.pgp.SignContentPGP](signcontentpgp)
* [org.apache.nifi.processors.pgp.VerifyContentPGP](verifycontentpgp)
