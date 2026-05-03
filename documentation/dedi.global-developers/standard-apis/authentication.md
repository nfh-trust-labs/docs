# Authentication APIs

Authentication on DeDi.global supports two modes:

- API key authentication for server-to-server integrations
- Cookie authentication for browser-based flows

Most write operations across the platform require authentication. The Postman collection treats the authentication flow below as the source of truth.

## Base URL

All endpoints in this section are relative to:

```text
https://api.dedi.global
```

## Authentication Methods

### API Key Authentication

This is the recommended authentication method for production integrations.

1. Call `POST /dedi/register` with `action=register` or `action=login`
2. Complete email verification using the magic link
3. Call `GET /dedi/get-api-key`
4. Send the returned key as a bearer token

```typescript
const response = await fetch('https://api.dedi.global/dedi/auth/me', {
  method: 'GET',
  headers: {
    Authorization: `Bearer ${apiKey}`,
    Accept: 'application/json'
  }
});
```

### Cookie Authentication

This is useful for browser-based testing and web applications.

After `verify-email` succeeds, the backend sets the auth cookie. The same authenticated endpoints can then be accessed through the browser session.

### Magic-Link Tokens

For `verify-email` and `reset-password/confirm`, the `token` is the magic-link identifier from the URL sent over email, not the raw JWT.

## Endpoints Overview

| Endpoint | Method | Auth Required | Purpose |
| --- | --- | --- | --- |
| `/dedi/register` | `POST` | No | Start register or login flow |
| `/dedi/verify-email` | `GET` | No | Verify email magic link and create session |
| `/dedi/auth/me` | `GET` | Yes | Get current authenticated user |
| `/dedi/token/refresh` | `POST` | Yes | Refresh access credentials |
| `/dedi/logout` | `POST` | Yes | Logout current session |
| `/dedi/get-api-key` | `GET` | Yes | Generate an API key |
| `/dedi/reset-password` | `POST` | Yes | Reset password for authenticated user |
| `/dedi/forgot-password` | `POST` | No | Send password reset magic link |
| `/dedi/reset-password/confirm` | `POST` | No | Complete password reset with magic-link token |
| `/dedi/resend-magic-link` | `POST` | No | Resend verification magic link |

## Register or Login

Start either the registration flow or the login flow.

**Endpoint**: `POST /dedi/register`

**Request Body**:

```json
{
  "email": "user@example.com",
  "name": "Jane Doe",
  "action": "register",
  "password": "Test@123"
}
```

**Notes**:

- `action` must be either `register` or `login`
- Both flows require `verify-email` afterwards

**Success Response**:

- `201 Created`
- Includes a `message`

**Error Status Codes**:

- `400`
- `401`
- `409`
- `429`
- `500`

## Verify Email

Consumes the email verification magic-link token and creates an authenticated session.

**Endpoint**: `GET /dedi/verify-email?token={token}`

**Query Parameters**:

- `token`: magic-link identifier from the email URL

**Success Response**:

- `200 OK`
- Includes `status`
- May include `data.refresh_token`

**Error Status Codes**:

- `400`
- `404`
- `500`

## Get Current User

Returns the currently authenticated user for either cookie or API-key authentication.

**Endpoint**: `GET /dedi/auth/me`

**Success Response**:

- `200 OK`
- Includes `id`, `email`, and `profile_id`

**Error Status Codes**:

- `401`
- `404`

## Refresh Token

Refreshes access credentials using a previously issued `refresh_token`.

**Endpoint**: `POST /dedi/token/refresh`

**Request Body**:

```json
{
  "refresh_token": "<refresh-token>"
}
```

**Success Response**:

- `200 OK`
- Includes `message`
- May include a refreshed `data.refresh_token`

**Error Status Codes**:

- `400`
- `401`
- `500`

## Logout

Logs out the current authenticated session.

**Endpoint**: `POST /dedi/logout`

**Success Response**:

- `200 OK`
- Includes `message`

**Error Status Codes**:

- `401`
- `500`

## Get API Key

Generates and persists a new API key for the authenticated user.

**Endpoint**: `GET /dedi/get-api-key`

**Success Response**:

- `200 OK`
- Includes `api_key`

**Error Status Codes**:

- `401`
- `404`
- `500`

## Reset Password

Changes the password for the currently authenticated user.

**Endpoint**: `POST /dedi/reset-password`

**Request Body**:

```json
{
  "new_password": "NewPass@123",
  "confirm_password": "NewPass@123"
}
```

**Success Response**:

- `200 OK`
- Includes `message`

**Error Status Codes**:

- `400`
- `401`
- `500`
- `502`

## Forgot Password

Sends a password reset magic link if the account exists.

**Endpoint**: `POST /dedi/forgot-password`

**Request Body**:

```json
{
  "email": "user@example.com"
}
```

**Success Response**:

- `201 Created`
- Includes `message`

**Error Status Codes**:

- `400`
- `429`
- `500`

## Confirm Password Reset

Completes a password reset using the password-reset magic-link token.

**Endpoint**: `POST /dedi/reset-password/confirm`

**Request Body**:

```json
{
  "token": "<reset-magic-link-id>",
  "new_password": "NewPass@123",
  "confirm_password": "NewPass@123"
}
```

**Success Response**:

- `200 OK`
- Includes `message`

**Error Status Codes**:

- `400`
- `401`
- `500`
- `502`

## Resend Magic Link

Resends the verification magic link used by the register and login flows.

**Endpoint**: `POST /dedi/resend-magic-link`

**Request Body**:

```json
{
  "action": "register",
  "email": "user@example.com"
}
```

**Success Response**:

- `201 Created`
- Includes `message`

**Error Status Codes**:

- `400`
- `401`
- `404`
- `429`
- `500`

## Recommended Flow

For production API integrations:

1. `POST /dedi/register` with `action=register`
2. `GET /dedi/verify-email?token=...`
3. `GET /dedi/get-api-key`
4. Use the API key for all authenticated requests

For password recovery:

1. `POST /dedi/forgot-password`
2. `POST /dedi/reset-password/confirm`

## Error Handling

Authentication endpoints return JSON responses and, on non-success responses, typically expose one of:

- `message`
- `error`
- `status`
