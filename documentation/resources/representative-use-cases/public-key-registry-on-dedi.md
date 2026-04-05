---
description: Machine-discoverable, versioned, verifiable public keys
---

# Public Key Registry on DeDi

### The Problem

Organizations distribute public keys — encryption keys, signing certificates, TLS root CAs — as static files (.cer, .pem, .pub) on websites. Consumers download them manually, cache them locally, and have no reliable way to know when a key has been rotated, revoked, or replaced. Stale keys cause silent verification failures. Manual distribution cannot scale.

### The DeDi Solution

Publish public keys as structured, versioned records on DeDi. Each key record contains the actual key material (PEM-encoded), metadata (algorithm, purpose, validity period), and a cryptographic proof anchored on-chain. Consumers discover and retrieve keys via a single API call.

### How It Works

| Publish  | Organization publishes its public key(s) to a DeDi namespace. Each key is a record with PEM material, algorithm identifier, purpose tag, and validity dates.                                                            |
| -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Rotate   | When a key is rotated, the organization publishes a new version. The old version remains on-chain with its proof — verifiers processing historical data can still resolve the key that was active at any point in time. |
| Discover | Any system calls the DeDi Lookup API with the key’s record ID. Response: JSON with the PEM key, metadata, version history, and on-chain proof. Sub-200ms.                                                               |
| Verify   | The consumer verifies the cryptographic proof to confirm the key is authentic and unaltered. No need to contact the publisher.                                                                                          |

<pre data-title="Example API Call" data-overflow="wrap"><code><strong>
</strong></code></pre>

### What Changes

| Static .cer/.pem files on a website                            | Structured, versioned records with actual key material  |
| -------------------------------------------------------------- | ------------------------------------------------------- |
| Manual download, no change notification                        | Key rotation propagates instantly via API               |
| Stale keys cached indefinitely                                 | Version history on-chain; current key always resolvable |
| No way to verify key authenticity without contacting publisher | Cryptographic proof verifiable by anyone, independently |

<br>

