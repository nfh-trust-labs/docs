# Publish APIs

The Publish APIs create and manage namespaces, registries, draft records, and CSV-based bulk upload workflows on DeDi.global.

🔒 **Authentication Required**: Every endpoint in this section requires authentication.

## Endpoints Overview

| Endpoint | Method | Purpose |
| --- | --- | --- |
| `/dedi/create-namespace` | `POST` | Create a namespace |
| `/dedi/{namespace}/create-registry` | `POST` | Create a registry in a namespace |
| `/dedi/{namespace}/{registry_name}/save-record-as-draft` | `POST` | Create a draft record |
| `/dedi/{namespace}/{registry_name}/publish-records` | `POST` | Publish one or more draft records |
| `/dedi/bulk-upload` | `POST` | Upload a CSV for background processing |
| `/dedi/bulk-upload/status/{jobId}` | `GET` | Check bulk-upload job status |
| `/dedi/bulk-upload/jobs` | `GET` | List your bulk-upload jobs |
| `/dedi/bulk-upload/failed-entries/{jobId}` | `GET` | View failed CSV entries |
| `/dedi/bulk-upload/failed-entries/{jobId}/download` | `GET` | Download failed entries as CSV |
| `/dedi/{namespace}/{registry_name}/download-sample-csv` | `GET` | Download a schema-derived sample CSV |

## Core Concepts

- **Namespace**: A top-level container for related registries
- **Registry**: A schema-backed collection inside a namespace
- **Record**: A data entry stored in a registry
- **Draft**: A record created but not yet published
- **Bulk Upload Job**: An asynchronous CSV import process

## Create Namespace

Creates a namespace and authorizes the creator.

**Endpoint**: `POST /dedi/create-namespace`

**Request Body**:

```json
{
  "name": "my-namespace",
  "description": "Namespace for production publishing",
  "meta": {}
}
```

**Success Response**:

- `201 Created`
- Includes `message`
- Includes `data.namespace_id`

**Error Status Codes**:

- `400`
- `401`
- `404`
- `500`

## Create Registry

Creates a registry inside a namespace. The Postman collection currently documents a schema-based example request.

**Endpoint**: `POST /dedi/{namespace}/create-registry`

**Path Parameters**:

- `namespace`: namespace identifier

**Request Body**:

```json
{
  "registry_name": "my-registry",
  "description": "Registry created for publishing records",
  "schema": {
    "$schema": "http://json-schema.org/draft-07/schema#",
    "type": "object",
    "additionalProperties": false,
    "properties": {
      "name": {
        "type": "string"
      },
      "description": {
        "type": "string"
      }
    },
    "required": ["name"]
  },
  "meta": {}
}
```

**Success Response**:

- `201 Created`
- Includes `message`
- Includes `data.registry_id`

**Error Status Codes**:

- `400`
- `401`
- `403`
- `404`
- `409`
- `500`

## Save Record as Draft

Creates a draft record. This endpoint also accepts a `publish` query parameter. In the Postman collection, the documented flow uses `publish=false`.

**Endpoint**: `POST /dedi/{namespace}/{registry_name}/save-record-as-draft?publish=false`

**Path Parameters**:

- `namespace`: namespace identifier
- `registry_name`: registry name

**Query Parameters**:

- `publish`: when set to `true`, create and publish in one call

**Request Body**:

```json
{
  "record_name": "my-record",
  "description": "Sample record description",
  "details": {
    "name": "Sample Entity",
    "description": "Sample entity for registry schema"
  },
  "meta": {},
  "valid_till": "2026-12-31T23:59:59Z"
}
```

**Success Response**:

- `201 Created`
- Includes `message`
- May include `data.record_name`

**Error Status Codes**:

- `400`
- `401`
- `403`
- `404`
- `409`
- `500`

## Publish Records

Publishes one or more draft records by `record_name`.

This replaces the older single-record publish flow documented previously.

**Endpoint**: `POST /dedi/{namespace}/{registry_name}/publish-records`

**Request Body**:

```json
{
  "records": [
    "my-record"
  ]
}
```

**Success Response**:

- `200 OK`
- Includes `message`

**Error Status Codes**:

- `400`
- `401`
- `403`
- `404`
- `500`

## Bulk Upload

Uploads a CSV file for background draft creation in a live registry.

**Endpoint**: `POST /dedi/bulk-upload`

**Request Format**: `multipart/form-data`

**Form Fields**:

- `file`: CSV file to upload
- `namespace`: target namespace
- `registry_name`: target registry
- `record_name_field`: optional source column to use for record names

**Notes**:

- The Postman collection explicitly notes that a CSV file must be selected before sending the request

**Success Response**:

- `200 OK`
- Includes `status`
- Includes `data.jobId`

**Error Status Codes**:

- `400`
- `401`
- `404`
- `500`

## Job Status

Returns status, counters, and failed-entry references for a bulk upload job.

**Endpoint**: `GET /dedi/bulk-upload/status/{jobId}`

**Path Parameters**:

- `jobId`: bulk upload job identifier

**Success Response**:

- `200 OK`
- Includes `status`
- Includes `data.jobId`

**Error Status Codes**:

- `401`
- `404`
- `500`

## Get User Jobs

Lists bulk upload jobs for the authenticated user.

**Endpoint**: `GET /dedi/bulk-upload/jobs?page={page}&limit={limit}`

**Query Parameters**:

- `page`: page number
- `limit`: page size

**Success Response**:

- `200 OK`
- Includes `status`
- Includes `data.jobs`

**Error Status Codes**:

- `401`
- `500`

## Failed Entries

Paginates failed rows for a bulk-upload job owned by the authenticated user.

**Endpoint**: `GET /dedi/bulk-upload/failed-entries/{jobId}?page={page}&limit={limit}`

**Query Parameters**:

- `page`: page number
- `limit`: page size

**Success Response**:

- `200 OK`
- Includes `status`
- Includes `data.failedEntries`

**Error Status Codes**:

- `401`
- `404`
- `500`

## Download Failed Entries CSV

Downloads failed bulk-upload rows as CSV.

**Endpoint**: `GET /dedi/bulk-upload/failed-entries/{jobId}/download`

**Success Response**:

- `200 OK`
- `Content-Type: text/csv`

**Error Status Codes**:

- `400`
- `401`
- `404`
- `500`

## Download Sample CSV

Downloads a schema-derived CSV template for a registry.

**Endpoint**: `GET /dedi/{namespace}/{registry_name}/download-sample-csv`

**Success Response**:

- `200 OK`
- `Content-Type: text/csv`

**Error Status Codes**:

- `400`
- `401`
- `403`
- `404`
- `500`

## Important Changes Reflected Here

This page has been aligned to the current Postman collection and now reflects the following changes:

- `publish-records` is documented instead of the older single-record `publish-record` endpoint
- bulk-upload helper endpoints for failed entries are included
- `download-sample-csv` is included
- the older `export-as-csv` endpoint is no longer documented in this section because it is not present in the current collection

## Suggested Publish Flow

1. Create a namespace
2. Create a registry
3. Save records as drafts
4. Publish records in batches using `publish-records`
5. Use bulk upload for CSV-driven ingestion when needed
6. Monitor failures through job status and failed-entry endpoints
