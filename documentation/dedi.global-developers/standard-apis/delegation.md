# Delegation Management APIs

The Delegation APIs allow namespace owners and delegates to share management access across namespaces and registries.

🔒 **Authentication Required**: Every endpoint in this section requires authentication.

## Endpoints Overview

| Endpoint | Method | Purpose |
| --- | --- | --- |
| `/dedi/{namespace}/{registry_name}/add-delegate` | `POST` | Add a registry delegate |
| `/dedi/{namespace}/{registry_name}/remove-registry-delegate` | `POST` | Remove a registry delegate |
| `/dedi/{namespace}/{registry_name}/get-delegates-by-registry` | `GET` | List registry delegates |
| `/dedi/{namespace}/add-namespace-delegate` | `POST` | Add a namespace delegate |
| `/dedi/{namespace}/remove-namespace-delegate` | `POST` | Remove a namespace delegate |
| `/dedi/{namespace}/get-delegates-by-namespace` | `GET` | List namespace delegates |
| `/dedi/{namespace}/transfer-ownership` | `POST` | Transfer namespace ownership |

## Core Concepts

- **Namespace delegation** gives access across the namespace and propagates registry delegate actions
- **Registry delegation** gives access to a single registry
- **Ownership transfer** moves namespace ownership to another registered user

## Add Registry Delegate

Queues registry delegate addition for the target user.

**Endpoint**: `POST /dedi/{namespace}/{registry_name}/add-delegate`

**Request Body**:

```json
{
  "email": "delegate@example.com"
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

## Remove Registry Delegate

Queues registry delegate removal for the target user.

**Endpoint**: `POST /dedi/{namespace}/{registry_name}/remove-registry-delegate`

**Request Body**:

```json
{
  "email": "delegate@example.com"
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

## Get Registry Delegates

Returns owner and non-owner delegate emails for the registry.

**Endpoint**: `GET /dedi/{namespace}/{registry_name}/get-delegates-by-registry`

**Success Response**:

- `200 OK`
- Includes `message`
- Includes `owner`
- Includes `delegates`

**Error Status Codes**:

- `400`
- `401`
- `404`
- `500`

## Add Namespace Delegate

Adds a namespace delegate and propagates registry delegate actions.

**Endpoint**: `POST /dedi/{namespace}/add-namespace-delegate`

**Request Body**:

```json
{
  "email": "delegate@example.com"
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

## Remove Namespace Delegate

Removes a namespace delegate and queues registry delegate removals where needed.

**Endpoint**: `POST /dedi/{namespace}/remove-namespace-delegate`

**Request Body**:

```json
{
  "email": "delegate@example.com"
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

## Get Namespace Delegates

Returns owner and non-owner delegate emails for the namespace.

**Endpoint**: `GET /dedi/{namespace}/get-delegates-by-namespace`

**Success Response**:

- `200 OK`
- Includes `message`
- Includes `owner`
- Includes `delegates`

**Error Status Codes**:

- `400`
- `401`
- `404`
- `500`

## Transfer Ownership

Transfers namespace ownership to another registered user.

**Endpoint**: `POST /dedi/{namespace}/transfer-ownership`

**Request Body**:

```json
{
  "email": "delegate@example.com"
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

## Practical Guidance

- Use namespace delegates when a user should manage multiple registries in the same namespace
- Use registry delegates when access should stay limited to one registry
- Review delegate lists regularly using the `get-delegates` endpoints
- Treat ownership transfer as a high-impact administrative action

## Important Changes Reflected Here

This page has been aligned to the current Postman collection.

- Registry and namespace delegate endpoints are unchanged in broad purpose, but descriptions now reflect queueing and propagation behavior from the collection
- `transfer-ownership` is now explicitly documented in this section
