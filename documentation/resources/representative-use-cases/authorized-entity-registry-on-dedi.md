---
description: Real-time, queryable authorization status for ecosystem participants
---

# Authorized Entity Registry on DeDi

## The Problem

Most ecosystems maintain lists of authorized entities — service agencies, authentication providers, KYC operators, certified vendors. These lists are typically published as periodic PDFs or static web pages. The problem: when an entity’s authorization is revoked, the change only appears in the next PDF release. Between updates, revoked entities can continue operating because consuming systems have no real-time signal.

## The DeDi Solution

Publish authorized entity lists as queryable, real-time registries on DeDi. Each entity record contains: entity name, entity code, authorization type, status (active/suspended/revoked), authorization date, and any relevant metadata. Changes propagate instantly.

### How It Works

| Publish             | Ecosystem operator publishes the authorized entity list to a DeDi namespace. Each entity is a record with structured fields.              |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Query               | Any party queries the registry by entity code, status, or authorization type. Returns matching records with proofs.                       |
| Update in real-time | When an entity’s status changes (suspended, revoked, reinstated), the operator updates the record. The change is on-chain within seconds. |
| Audit trail         | Every status change is versioned and anchored. Regulators can verify the exact authorization state at any historical point in time.       |

{% code title="Example API Calls" overflow="wrap" %}
```
Look up a specific entity: GET /dedi/lookup/{namespace}/{registry}/{entity-code}
Query all active entities: GET /dedi/query/{namespace}/{registry}?status=active
```
{% endcode %}

## What Changes

| Periodic PDF lists, updated monthly or quarterly      | Real-time queryable registry, changes propagate in seconds |
| ----------------------------------------------------- | ---------------------------------------------------------- |
| Revoked entities invisible until next PDF release     | Revocations visible immediately via API                    |
| Manual verification — search a PDF, hope it’s current | Programmatic verification — one API call, with proof       |
| No audit trail for authorization changes              | Full version history, on-chain, independently verifiable   |

<br>
