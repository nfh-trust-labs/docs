# Watch And Notifications APIs

Watch And Notifications APIs let authenticated users create watches, remove them, and list their current subscriptions.

## Authentication

All endpoints in this section require authentication.

Use either of the supported authentication methods described in [Authentication APIs](authentication.md):

- API key authentication using `Authorization: Bearer <api_key>`
- Auth cookie authentication for browser-based sessions

## Watch Types

The current collection describes `subscribe` as a flexible watch-creation endpoint. Depending on which fields you supply, it can create a watch for:

- a namespace
- a registry
- a record
- a `registry_tag`

## Subscribe

Creates a namespace, registry, record, or `registry_tag` watch depending on the supplied fields.

**Endpoint:** `POST /dedi/subscribe`

**Request Body:**
```json
{
  "namespace": "namespace-id",
  "registry_name": "registry-name",
  "record_name": "record-name",
  "registry_tag": "membership",
  "webhook_url": "https://example.com/webhooks/dedi",
  "webhook_secret": "d2hzZWNfMTIzNDU2Nzg5MA=="
}
```

**Field Usage:**
- `namespace` (optional depending on watch type)
- `registry_name` (optional depending on watch type)
- `record_name` (optional): Use for a record-level watch
- `registry_tag` (optional): Use for a tag-based watch
- `webhook_url` (required): Webhook target URL
- `webhook_secret` (required): Secret used by your receiver to validate webhook deliveries

**Example Request: registry watch**
```typescript
const response = await fetch('https://api.dedi.global/dedi/subscribe', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json',
    'Accept': 'application/json'
  },
  body: JSON.stringify({
    namespace: 'acme',
    registry_name: 'employees',
    webhook_url: 'https://example.com/webhooks/dedi',
    webhook_secret: 'd2hzZWNfMTIzNDU2Nzg5MA=='
  })
});

const data = await response.json();
```

**Example Request: record watch**
```typescript
const response = await fetch('https://api.dedi.global/dedi/subscribe', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json',
    'Accept': 'application/json'
  },
  body: JSON.stringify({
    namespace: 'acme',
    registry_name: 'employees',
    record_name: 'john-doe',
    webhook_url: 'https://example.com/webhooks/dedi',
    webhook_secret: 'd2hzZWNfMTIzNDU2Nzg5MA=='
  })
});

const data = await response.json();
```

**Success Response Requirements In Collection:**
- `message`
- `data.id`

**Allowed Status Codes:**
- `201` - Watch created
- `400` - Invalid request body
- `401` - Authentication failed
- `404` - Target resource not found
- `409` - Duplicate or conflicting watch
- `500` - Internal server error

## Unsubscribe

Deletes a watch owned by the authenticated user.

**Endpoint:** `POST /dedi/unsubscribe`

**Request Body:**
```json
{
  "id": "subscription-id"
}
```

**Field Notes:**
- `id` (required): Subscription identifier returned by `subscribe`

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/unsubscribe', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Content-Type': 'application/json',
    'Accept': 'application/json'
  },
  body: JSON.stringify({
    id: 'subscription-id'
  })
});

const data = await response.json();
```

**Success Response Requirements In Collection:**
- `message`

**Allowed Status Codes:**
- `200` - Watch removed
- `400` - Invalid request body
- `401` - Authentication failed
- `404` - Watch not found
- `500` - Internal server error

## Get Subscriptions

Lists all watches for the authenticated user.

**Endpoint:** `GET /dedi/subscriptions`

**Example Request:**
```typescript
const response = await fetch('https://api.dedi.global/dedi/subscriptions', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_API_KEY',
    'Accept': 'application/json'
  }
});

const data = await response.json();
```

**Success Response Requirements In Collection:**
- `message`
- `data`

**Allowed Status Codes:**
- `200` - Subscriptions returned
- `401` - Authentication failed
- `500` - Internal server error

## Notes

- The current collection uses the section name `Watch & Notifications`, while the live docs nav currently points to `subscription.md`.
- This section documents only the watch endpoints in that folder:
  - `POST /dedi/subscribe`
  - `POST /dedi/unsubscribe`
  - `GET /dedi/subscriptions`
- I intentionally left out `Platform Statistics & Analytics` as requested.
