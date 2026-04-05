---
description: Versioned, resolvable API and data schemas
---

# Schema Registry on DeDi

## The Problem

APIs and data formats evolve. Schemas are documented in PDFs, wikis, or version-controlled repos that consumers may not know about. When a schema changes, consumers have no reliable discovery mechanism. Deprecated schemas linger. New schemas go unnoticed. Integration breaks are discovered in production.

## The DeDi Solution

Publish API and data schemas as versioned, resolvable records on DeDi. Each schema record contains: the schema definition (JSON Schema, XML Schema, or any structured format), a namespace URI, version identifiers, deprecation status, and links to successor schemas. Consumers discover schemas via the same API they use for every other DeDi registry.

How It Works

| Publish  | Organization publishes schemas to a DeDi namespace. Each schema is a record with the schema body, version, status (active/deprecated/superseded), and a namespace URI. |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Discover | Developers query the DeDi registry to find all schemas for a namespace, filter by status (active only, or including deprecated), and retrieve specific versions.       |
| Version  | When a schema evolves, a new version is published. The old version’s record is updated to point to the successor. Both remain resolvable.                              |
| Resolve  | Any system can dereference a schema’s namespace URI to get the full schema definition from DeDi. Machine-readable, cryptographically verified.                         |

## What Changes

| Before DeDi                                 | After DeDi                                                         |
| ------------------------------------------- | ------------------------------------------------------------------ |
| Schemas in PDFs, wikis, scattered repos     | Single registry with all schemas, versions, and deprecation status |
| No machine-readable discovery               | Query API to find active schemas, resolve by namespace URI         |
| Breaking changes discovered in production   | Deprecation status and successor links enable graceful migration   |
| No provenance — who published this version? | Every schema version is signed, timestamped, and anchored on-chain |



<br>
