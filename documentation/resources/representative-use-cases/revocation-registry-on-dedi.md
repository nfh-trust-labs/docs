---
description: Universal credential revocation for offline-first credentials
---

# Revocation Registry on DeDi

## The Problem

Offline-first credentials — QR codes, XML documents, Verifiable Credentials — are powerful because they work without calling the issuer at verification time. But this creates a critical gap: once issued, how does a verifier know if a credential has been revoked? Most offline credential systems today have zero revocation mechanism. A revoked credential looks identical to a valid one.

## The DeDi Solution

DeDi provides a tamper-proof, publicly queryable revocation registry. When an issuer revokes a credential, they publish the hashed credential identifier to their revocation registry on DeDi. Any verifier, anywhere, can check revocation status with a single API call — without contacting the issuer.

#### The Revocation Flow

| 1. Issue  | Issuer creates a credential. The credential’s credentialStatus field points to the DeDi revocation registry (see VC integration below).                                                           |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 2. Revoke | When a credential must be revoked, the issuer publishes the hashed credential UUID to the DeDi registry with status: revoked. The change is cryptographically anchored and timestamped on-chain.  |
| 3. Verify | Verifier hashes the credential’s ID, calls the DeDi Lookup API. If the hash is present with revoked status, the credential is no longer valid. If not found, the credential has not been revoked. |

## W3C Verifiable Credential Revocation

DeDi can support  with the W3C VC Data Model 2.0 via the credentialStatus field. When an issuer creates a Verifiable Credential, they embed a DeDi revocation check directly in the credential

{% code title="" overflow="wrap" %}
```
"credentialStatus": {
  "id": "https://dedi.global/dedi/lookup/{namespace}/{registry}/{hashed-cred-uuid}",
  "type": "dedi",
  "statusPurpose": "revocation",
  "statusListCredential": "https://dedi.global/dedi/query/{namespace}/{registry}"
}
```
{% endcode %}

#### VC Verification Flow Using DeDi

| Step 1: Receive VC         | Verifier receives a Verifiable Credential (JSON-LD, JWT, or SD-JWT format) from the holder.                                                                                                                        |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Step 2: Validate signature | Verifier checks the VC’s cryptographic signature using the issuer’s public key (which can also be resolved from DeDi — see Public Key Registry use case).                                                          |
| Step 3: Check revocation   | Verifier reads the credentialStatus.id field from the VC. This is a DeDi Lookup URL. The verifier calls this endpoint.                                                                                             |
| Step 4: Evaluate response  | If the DeDi API returns a record with status: revoked, the credential is invalid. If the record is not found (404), the credential has not been revoked. The response includes an on-chain proof for auditability. |
| Step 5: Trust decision     | Verifier now has: (a) signature validity, (b) revocation status, both independently verifiable without contacting the issuer.                                                                                      |

#### Why DeDi for Revocation

The revocation registry is tamper-proof — even the issuer cannot silently un-revoke a credential. All revocation events are on-chain with timestamps. The registry is publicly queryable, so any verifier can check without credentials or API keys. And because it’s on DeDi, it works across credential systems: national IDs, professional licenses, academic certificates, organizational authorizations.

<br>
