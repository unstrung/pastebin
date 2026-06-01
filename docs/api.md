# API Reference

This document covers the full REST API for the Pastebin project.

**Base URL:** `http://localhost:3000` (development) · `https://your-domain.com` (production)

**API Version:** v1

**Response Format:** All endpoints return JSON with a standard envelope:

    {
      "data": { ... },
      "meta": {
        "request_id": "abc123",
        "timestamp": "2026-06-01T12:00:00Z"
      },
      "errors": []
    }

**Authentication:** Authenticated endpoints require a `Bearer` token in the request header:

    Authorization: Bearer YOUR_API_TOKEN

---

## Table of Contents

- [Authentication](#authentication)
- [Pastes](#pastes)
- [Raw Content](#raw-content)
- [Collections](#collections)
- [Webhooks](#webhooks)
- [Users](#users)
- [Admin](#admin)
- [Utility](#utility)
- [Rate Limiting](#rate-limiting)
- [Error Codes](#error-codes)
- [Deprecations](#deprecations)

---

## Authentication

### Register

Create a new user account.

    POST /api/auth/register

**Request Body**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `username` | string | ✅ | 3–32 characters, alphanumeric and hyphens only |
| `email` | string | ✅ | Valid email address |
| `password` | string | ✅ | Minimum 8 characters |

**Example Request**

    POST /api/auth/register
    Content-Type: application/json

    {
      "username": "bill",
      "email": "bill@example.com",
      "password": "supersecret123"
    }

**Example Response** `201 Created`

    {
      "data": {
        "id": "usr_abc123",
        "username": "bill",
        "email": "bill@example.com",
        "created_at": "2026-06-01T12:00:00Z"
      },
      "meta": { "request_id": "req_xyz", "timestamp": "2026-06-01T12:00:00Z" },
      "errors": []
    }

---

### Login

Authenticate and receive a Bearer token.

    POST /api/auth/login

**Request Body**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `email` | string | ✅ | Registered email address |
| `password` | string | ✅ | Account password |

**Example Request**

    POST /api/auth/login
    Content-Type: application/json

    {
      "email": "bill@example.com",
      "password": "supersecret123"
    }

**Example Response** `200 OK`

    {
      "data": {
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
        "expires_at": "2026-07-01T12:00:00Z"
      },
      "meta": { "request_id": "req_xyz", "timestamp": "2026-06-01T12:00:00Z" },
      "errors": []
    }

---

## Pastes

### Create a Paste

    POST /api/paste

**Auth:** Optional (anonymous pastes are allowed)

**Request Body**

| Field | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `content` | string | ✅ | — | The paste content |
| `title` | string | | `"Untitled"` | Display title (max 256 chars) |
| `language` | string | | `"plaintext"` | Syntax highlighting language |
| `visibility` | string | | `"public"` | `"public"`, `"unlisted"`, or `"private"` |
| `expiry` | string | | `"30d"` (anon) · `"never"` (auth) | `"1h"`, `"1d"`, `"1w"`, `"1m"`, or `"never"` |
| `password` | string | | — | Protects the paste with AES-256-GCM encryption |

**Example Request**

    POST /api/paste
    Content-Type: application/json
    Authorization: Bearer YOUR_API_TOKEN

    {
      "title": "My Script",
      "content": "console.log('hello world');",
      "language": "javascript",
      "visibility": "public",
      "expiry": "7d"
    }

**Example Response** `201 Created`

    {
      "data": {
        "id": "pst_aB3xZ9",
        "url": "https://your-domain.com/pst_aB3xZ9",
        "raw_url": "https://your-domain.com/raw/pst_aB3xZ9",
        "title": "My Script",
        "language": "javascript",
        "visibility": "public",
        "expires_at": "2026-06-08T12:00:00Z",
        "created_at": "2026-06-01T12:00:00Z",
        "owner_id": "usr_abc123"
      },
      "meta": { "request_id": "req_xyz", "timestamp": "2026-06-01T12:00:00Z" },
      "errors": []
    }

---

### Fetch a Paste

    GET /api/paste/:id

**Auth:** Optional (required for private pastes)

**Path Parameters**

| Parameter | Description |
|-----------|-------------|
| `id` | The paste ID (e.g. `pst_aB3xZ9`) |

**Query Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `password` | string | Required if the paste is password-protected |

**Example Response** `200 OK`

    {
      "data": {
        "id": "pst_aB3xZ9",
        "title": "My Script",
        "content": "console.log('hello world');",
        "language": "javascript",
        "visibility": "public",
        "expires_at": "2026-06-08T12:00:00Z",
        "created_at": "2026-06-01T12:00:00Z",
        "stats": {
          "views": 42,
          "unique_visitors": 31,
          "last_accessed_at": "2026-06-01T18:30:00Z"
        }
      },
      "meta": { "request_id": "req_xyz", "timestamp": "2026-06-01T12:00:00Z" },
      "errors": []
    }

---

### Update a Paste

    PUT /api/paste/:id

**Auth:** Required — must be the paste owner

**Request Body** — all fields optional; only provided fields are updated

| Field | Type | Description |
|-------|------|-------------|
| `title` | string | New title |
| `content` | string | New content |
| `language` | string | New syntax language |
| `visibility` | string | New visibility (`"public"`, `"unlisted"`, `"private"`) |
| `expiry` | string | New expiry (`"1h"`, `"1d"`, `"1w"`, `"1m"`, `"never"`) |

**Example Response** `200 OK`

    {
      "data": {
        "id": "pst_aB3xZ9",
        "title": "My Updated Script",
        "updated_at": "2026-06-01T14:00:00Z"
      },
      "meta": { "request_id": "req_xyz", "timestamp": "2026-06-01T14:00:00Z" },
      "errors": []
    }

---

### Delete a Paste

    DELETE /api/paste/:id

**Auth:** Required — must be the paste owner or an admin

**Example Response** `200 OK`

    {
      "data": { "deleted": true, "id": "pst_aB3xZ9" },
      "meta": { "request_id": "req_xyz", "timestamp": "2026-06-01T14:00:00Z" },
      "errors": []
    }

---

### Fork a Paste

Creates a copy of any public paste under the authenticated user's account.

    POST /api/paste/:id/fork

**Auth:** Required

**Example Response** `201 Created`

    {
      "data": {
        "id": "pst_fK7qR2",
        "forked_from": "pst_aB3xZ9",
        "created_at": "2026-06-01T15:00:00Z"
      },
      "meta": { "request_id": "req_xyz", "timestamp": "2026-06-01T15:00:00Z" },
      "errors": []
    }

---

## Raw Content

Returns paste content as plain text with `Content-Type: text/plain`. Useful for
piping into other tools, embedding in scripts, or downloading directly.

    GET /raw/:id

**Auth:** Optional (required for private pastes)

**Query Parameters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `password` | string | Required if the paste is password-protected |

**Example Response** `200 OK`

    Content-Type: text/plain; charset=utf-8

    console.log('hello world');

---

## Collections

### Create a Collection

    POST /api/collection

**Auth:** Required

**Request Body**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | ✅ | Collection display name (max 128 chars) |
| `description` | string | | Optional description |
| `visibility` | string | | `"public"` or `"unlisted"` (default: `"public"`) |
| `paste_ids` | string[] | | Array of paste IDs to include |

**Example Response** `201 Created`

    {
      "data": {
        "id": "col_mN4pQ8",
        "name": "Useful Scripts",
        "url": "https://your-domain.com/col/col_mN4pQ8",
        "paste_count": 3,
        "created_at": "2026-06-01T12:00:00Z"
      },
      "meta": { "request_id": "req_xyz", "timestamp": "2026-06-01T12:00:00Z" },
      "errors": []
    }

---

### Fetch a Collection

    GET /api/collection/:id

**Auth:** Optional

**Example Response** `200 OK`

    {
      "data": {
        "id": "col_mN4pQ8",
        "name": "Useful Scripts",
        "description": "Scripts I use all the time.",
        "visibility": "public",
        "pastes": [
          { "id": "pst_aB3xZ9", "title": "My Script", "language": "javascript" }
        ],
        "created_at": "2026-06-01T12:00:00Z"
      },
      "meta": { "request_id": "req_xyz", "timestamp": "2026-06-01T12:00:00Z" },
      "errors": []
    }

---

### Update a Collection

    PUT /api/collection/:id

**Auth:** Required — must be the collection owner

**Request Body** — all fields optional

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | New collection name |
| `description` | string | New description |
| `visibility` | string | New visibility |
| `paste_ids` | string[] | Replaces the full list of paste IDs |

---

### Delete a Collection

    DELETE /api/collection/:id

**Auth:** Required — must be the collection owner

**Example Response** `200 OK`

    {
      "data": { "deleted": true, "id": "col_mN4pQ8" },
      "meta": { "request_id": "req_xyz", "timestamp": "2026-06-01T14:00:00Z" },
      "errors": []
    }

---

## Webhooks

Webhooks deliver `POST` requests to your configured URL when paste events occur.

### Supported Events

| Event | Trigger |
|-------|---------|
| `paste.created` | A new paste is created |
| `paste.updated` | An existing paste is updated |
| `paste.deleted` | A paste is deleted |
| `paste.expired` | A paste reaches its expiry time |

### Payload

    POST https://your-endpoint.com/webhook
    Content-Type: application/json
    X-Pastebin-Event: paste.created
    X-Pastebin-Signature: sha256=<hmac>

    {
      "event": "paste.created",
      "timestamp": "2026-06-01T12:00:00Z",
      "data": {
        "id": "pst_aB3xZ9",
        "title": "My Script",
        "visibility": "public",
        "owner_id": "usr_abc123"
      }
    }

**Signature Verification**

The `X-Pastebin-Signature` header contains an HMAC-SHA256 signature of the raw
request body, signed with your `WEBHOOK_SECRET`. Always verify this before
processing the payload:

    const crypto = require('crypto');

    function verifySignature(rawBody, signature, secret) {
      const expected = 'sha256=' + crypto
        .createHmac('sha256', secret)
        .update(rawBody)
        .digest('hex');
      return crypto.timingSafeEqual(
        Buffer.from(signature),
        Buffer.from(expected)
      );
    }

---

## Users

### Get Current User

    GET /api/user

**Auth:** Required

**Example Response** `200 OK`

    {
      "data": {
        "id": "usr_abc123",
        "username": "bill",
        "email": "bill@example.com",
        "created_at": "2026-06-01T12:00:00Z",
        "paste_count": 17,
        "collection_count": 3
      },
      "meta": { "request_id": "req_xyz", "timestamp": "2026-06-01T12:00:00Z" },
      "errors": []
    }

---

### List User Pastes

    GET /api/user/pastes

**Auth:** Required

**Query Parameters**

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `cursor` | string | — | Pagination cursor from previous response |
| `limit` | integer | `20` | Results per page (max `100`) |
| `visibility` | string | — | Filter by `"public"`, `"unlisted"`, or `"private"` |
| `language` | string | — | Filter by language |

**Example Response** `200 OK`

    {
      "data": {
        "pastes": [
          {
            "id": "pst_aB3xZ9",
            "title": "My Script",
            "language": "javascript",
            "visibility": "public",
            "created_at": "2026-06-01T12:00:00Z"
          }
        ]
      },
      "meta": {
        "request_id": "req_xyz",
        "timestamp": "2026-06-01T12:00:00Z",
        "cursor": "cur_next123",
        "has_more": true
      },
      "errors": []
    }

---

## Admin

Admin endpoints require a valid Bearer token belonging to a user with the `admin` role.

### List All Pastes

    GET /api/admin/pastes

**Query Parameters:** same as `GET /api/user/pastes` plus `owner_id` to filter by user.

---

### Moderate a Paste

    POST /api/admin/paste/:id/moderate

**Request Body**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `action` | string | ✅ | `"remove"` or `"flag"` |
| `reason` | string | ✅ | Reason for moderation action |

---

### Get Platform Metrics

    GET /api/admin/metrics

**Example Response** `200 OK`

    {
      "data": {
        "total_pastes": 18423,
        "pastes_today": 142,
        "total_users": 3091,
        "active_users_7d": 487,
        "storage_used_bytes": 2147483648
      },
      "meta": { "request_id": "req_xyz", "timestamp": "2026-06-01T12:00:00Z" },
      "errors": []
    }

---

## Utility

### Health Check

    GET /health

Returns the current service status and uptime. Does not require authentication.

**Example Response** `200 OK`

    {
      "status": "ok",
      "uptime_seconds": 86400,
      "database": "connected",
      "storage": "connected",
      "version": "1.3.0",
      "timestamp": "2026-06-01T12:00:00Z"
    }

---

### Paste Diff

Returns a side-by-side diff between two pastes.

    GET /diff/:id1/:id2

**Auth:** Optional (both pastes must be accessible to the requester)

**Example Response** `200 OK`

    {
      "data": {
        "paste_a": { "id": "pst_aB3xZ9", "title": "My Script" },
        "paste_b": { "id": "pst_fK7qR2", "title": "My Script (fork)" },
        "diff": [
          { "type": "unchanged", "line": 1, "content": "console.log('hello world');" },
          { "type": "added",     "line": 2, "content": "console.log('new line');" }
        ]
      },
      "meta": { "request_id": "req_xyz", "timestamp": "2026-06-01T12:00:00Z" },
      "errors": []
    }

---

## Rate Limiting

All responses include rate limit headers:

| Header | Description |
|--------|-------------|
| `X-RateLimit-Limit` | Maximum requests allowed per minute |
| `X-RateLimit-Remaining` | Requests remaining in the current window |
| `X-RateLimit-Reset` | Unix timestamp when the window resets |

| User Type | Limit |
|-----------|-------|
| Anonymous | 60 requests / minute |
| Authenticated | 300 requests / minute |

When the limit is exceeded the API returns `429 Too Many Requests`. Back off and retry
after the `X-RateLimit-Reset` timestamp.

---

## Error Codes

All errors are returned in the `errors` array of the standard response envelope.

| HTTP Status | Code | Description |
|-------------|------|-------------|
| `400` | `INVALID_REQUEST` | Missing or malformed request body or parameters |
| `401` | `UNAUTHORIZED` | Missing or invalid Bearer token |
| `403` | `FORBIDDEN` | Valid token but insufficient permissions |
| `404` | `NOT_FOUND` | Resource does not exist or has expired |
| `409` | `CONFLICT` | Resource already exists (e.g. duplicate username) |
| `422` | `UNPROCESSABLE` | Request is well-formed but semantically invalid |
| `429` | `RATE_LIMITED` | Too many requests — see Rate Limiting |
| `500` | `INTERNAL_ERROR` | Unexpected server error |

**Example Error Response**

    {
      "data": null,
      "meta": { "request_id": "req_xyz", "timestamp": "2026-06-01T12:00:00Z" },
      "errors": [
        {
          "code": "NOT_FOUND",
          "message": "Paste pst_aB3xZ9 does not exist or has expired.",
          "field": null
        }
      ]
    }

---

## Deprecations

| Deprecated Endpoint / Field | Removed In | Use Instead |
|-----------------------------|-----------|-------------|
| `GET /api/paste/:id/content` | v2.0.0 | `GET /raw/:id` |
| `visibility: "hidden"` | v2.0.0 | `visibility: "unlisted"` |
| `created_at` / `expires_at` as Unix timestamps | v1.1.0 | ISO 8601 strings |

---

*API Reference v1.3.0 · Last updated June 2026*
