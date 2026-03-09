---
title: "DecryptContentPGP 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/decryptcontentpgp"
---

# DecryptContentPGP 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-pgp-nar

## Description

Decrypt contents of OpenPGP messages. Using the Packaged Decryption Strategy preserves OpenPGP encoding to support subsequent signature verification.

## Tags

Encryption, GPG, OpenPGP, PGP, RFC 4880

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| decryption-strategy | Strategy for writing files to success after decryption |
| passphrase | Passphrase used for decrypting data encrypted with Password-Based Encryption |
| private-key-service | PGP Private Key Service for decrypting data encrypted with Public Key Encryption |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | Decryption Failed |
| success | Decryption Succeeded |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| pgp.literal.data.filename | Filename from decrypted Literal Data |
| pgp.literal.data.modified | Modified Date from decrypted Literal Data |
| pgp.symmetric.key.algorithm.block.cipher | Symmetric-Key Algorithm Block Cipher |
| pgp.symmetric.key.algorithm.id | Symmetric-Key Algorithm Identifier |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.pgp.EncryptContentPGP](encryptcontentpgp)
* [org.apache.nifi.processors.pgp.SignContentPGP](signcontentpgp)
* [org.apache.nifi.processors.pgp.VerifyContentPGP](verifycontentpgp)
