---
description: Decentralized Trust Infrastructure for Public Registries
---

# DeDi.global

## The Problem

Every day, millions of digital transactions depend on trust artifacts that live in the wrong formats: PDF lists of authorized entities, CSV downloads of public keys, web pages with schema definitions. Verifying these artifacts is slow, manual, and error-prone. There is no universal way to ask: “Is this information current, authentic, and unaltered?”

## The Solution

DeDi.global transforms static public registries into machine-readable, cryptographically verifiable, tamper-proof directories accessible via a single API. It is operated by Networks for Humanity Foundation as free, public good infrastructure.

DeDi.global is built on the Decentralized Directory Protocol (DDP) — an open standard under Linux Foundation Decentralized Trust (LFDT). The protocol is not a software product. It is a universal, interoperable foundation for accessing and verifying public information. Anyone can implement it.

## Architecture

DeDi organizes information into three tightly coupled tiers:

{% stepper %}
{% step %}
### Namespace

The root trust anchor. Corresponds to an organization and its verified domain name. All API calls begin with the namespace. Domain verification via DNS TXT records makes URLs human-readable (e.g., /dedi/dhiway.com/employees/Alice) and establishes organizational provenance.
{% endstep %}

{% step %}
### Registry

A directory of records within a namespace. Each registry has a configurable schema that defines the structure and types of its entries.
{% endstep %}

{% step %}
### Record

An individual data entry — the actual value or pointer to information. Each record is independently versioned and cryptographically anchored.
{% endstep %}
{% endstepper %}

## Two Core Protocol Endpoints

Lookup and Query are the protocol foundation. DeDi also exposes endpoints for version history, advanced search, cryptographic verification, subscriptions, and state management.

| **Lookup** | Retrieve a specific namespace, registry, or record by identifier. Returns the entry with its cryptographic proof. Supports version-specific and historical (as\_on) lookups. | Look up a public encryption key by record ID → get key material + on-chain proof                    |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| **Query**  | Search a namespace or registry by field values, status, date range, or name. Returns matching entries with proofs. Supports pagination, sorting, and historical queries.     | Query all active authorized entities → get list with entity codes, authorization status, and proofs |

Responses are JSON, sub-200ms, and include cryptographic proof that the entry is authentic, unaltered, and anchored on-chain.
