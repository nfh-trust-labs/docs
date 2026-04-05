# VC Signing Keys on DeDi



Globally resolvable issuer keys for the Verifiable Credentials ecosystem

## The Problem

When an organization issues Verifiable Credentials, verifiers need the issuer’s public signing key to validate the credential’s signature. Today, these keys are scattered — buried in DID documents, hosted on proprietary resolvers, or distributed through bilateral agreements. There is no universal, standards-based way for a verifier to resolve an issuer’s VC signing keys without knowing the issuer’s specific infrastructure.

## The DeDi Solution

Publish VC signing keys (Ed25519, BBS+, SD-JWT, ECDSA) as structured records on DeDi. Any verifier globally can resolve an issuer’s keys in one API call, regardless of the credential format or the issuer’s DID method.

How It Works

| Publish keys | Issuer publishes their VC signing public keys to a DeDi namespace. Each key record includes: algorithm (Ed25519, BBS+, etc.), public key material, purpose (assertionMethod, authentication), and validity period. |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Resolve      | Verifier calls DeDi Lookup or Query API with the issuer’s namespace and key ID. Returns the key with cryptographic proof.                                                                                          |
| Rotate       | When keys are rotated, new versions are published. Old keys remain resolvable for historical credential verification.                                                                                              |
| Multi-format | A single issuer can publish keys for multiple credential formats (JSON-LD with Data Integrity, JWT, SD-JWT) in the same namespace.                                                                                 |

<br>

## What This Enables

Any verifier, anywhere in the world, can validate a Verifiable Credential from any issuer that publishes on DeDi — without bilateral integration, without knowing the issuer’s DID method, and without relying on the issuer’s infrastructure being online at verification time.

This is particularly critical for cross-border credential verification, where issuers and verifiers operate under different technical stacks and trust frameworks.

Example

GET https://dedi.global/dedi/lookup/{issuer-namespace}/{signing-keys}/{key-id}

Returns: JSON with public key material (JWK or PEM), algorithm identifier, purpose, validity dates, version history, on-chain proof.

DeDi.global  |  Networks for Humanity Foundation  |  Linux Foundation Decentralized Trust

[https://dedi.global](https://dedi.global)

<br>
