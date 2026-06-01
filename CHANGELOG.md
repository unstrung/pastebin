# Changelog

All notable changes to this project will be documented in this file.

This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html)
and the format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

---

## [Unreleased]

### Added
- Planned: syntax highlighting for 50+ languages via Prism.js
- Planned: user authentication with OAuth2 (GitHub, Google)
- Planned: paste expiry notifications via email

### Changed
- Planned: migrate from file-based storage to PostgreSQL
- Planned: overhaul public API to RESTful v2 design

---

## [1.3.0] — 2026-05-18

### Added
- **Paste forking:** users can fork any public paste into their own account
- **Raw view endpoint:** `/raw/:id` returns plain text with `Content-Type: text/plain`
- **Paste statistics:** view count, unique visitor count, and last-accessed timestamp per paste
- **Dark mode toggle:** persisted via `localStorage` across sessions
- **CLI support (alpha):** initial `pastectl` command-line client for posting and fetching pastes

### Changed
- Upgraded Node.js runtime target from 18.x to 20.x LTS
- Replaced deprecated `crypto.createCipher` with `crypto.createCipheriv` for AES encryption
- Improved paste search: now full-text indexed with relevance ranking

### Fixed
- `403 Forbidden` incorrectly returned for anonymous users accessing unlisted pastes ([#88](../../issues/88))
- Paste title truncated at 64 chars in list view despite 256-char storage limit ([#91](../../issues/91))
- Markdown pastes rendering HTML entities as literal text ([#97](../../issues/97))

### Security
- Patched stored XSS in paste title field (sanitization bypassed in WebSocket push events)
- Rate limiting headers now correctly reflect per-IP quotas

---

## [1.2.1] — 2026-04-02

### Fixed
- `DELETE /api/paste/:id` returning `200 OK` when paste did not exist ([#79](../../issues/79))
- Memory leak in WebSocket handler on mid-stream client disconnect ([#81](../../issues/81))
- Incorrect UTC offset applied to timestamps for users in UTC+X timezones ([#84](../../issues/84))

### Security
- Updated `express` to v4.19.2 — patches CVE-2024-29041 (open redirect via `Host` header)
- Updated `multer` to v1.4.5-lts.1 — addresses DoS via crafted `Content-Type`

---

## [1.2.0] — 2026-03-10

### Added
- **Password-protected pastes:** AES-256-GCM encryption; password never stored server-side
- **Paste collections:** group multiple pastes under a named collection with shareable URL
- **Embed widget:** auto-generated `<iframe>` embed code for any public paste
- **Webhook support:** `POST` callback on paste create/delete events (configurable per user)
- `X-Request-ID` header propagated through all API responses

### Changed
- Default paste expiry for anonymous pastes changed from "never" to 30 days
- API response envelope standardized: all endpoints return `{ data, meta, errors }`
- Paste attachment storage migrated from local disk to S3-compatible object storage

### Deprecated
- `GET /api/paste/:id/content` — use `GET /raw/:id`; removed in v2.0.0
- `visibility: "hidden"` — replaced by `"unlisted"`; legacy values accepted until v2.0.0

### Removed
- Legacy XML response format (`Accept: application/xml`); JSON only going forward

### Fixed
- Collection page returning `500` when a contained paste was deleted ([#62](../../issues/62))
- Syntax highlighting theme not applied on first load with `system` preference ([#66](../../issues/66))
- `Content-Security-Policy` header missing on `/embed` routes ([#70](../../issues/70))

---

## [1.1.0] — 2026-01-20

### Added
- **Syntax highlighting:** auto-detection + manual language selection (40 languages via highlight.js)
- **Paste diffing:** side-by-side comparison at `/diff/:id1/:id2`
- **Keyboard shortcuts:** `Ctrl+S` save, `Ctrl+Shift+F` fullscreen editor
- **OpenGraph meta tags:** rich link previews when sharing paste URLs
- Health check endpoint: `GET /health` returns service status and uptime

### Changed
- Editor replaced from `<textarea>` to CodeMirror 6
- Pagination switched from page-based to cursor-based for consistency under concurrent writes
- `created_at` / `expires_at` now returned as ISO 8601 strings (previously Unix timestamps)

### Fixed
- Long lines causing horizontal overflow on mobile ([#41](../../issues/41))
- `expires_at: null` serialized as `"null"` string instead of JSON `null` ([#44](../../issues/44))
- Users able to edit others' pastes by manipulating `owner_id` query param ([#49](../../issues/49))

### Security
- Authorization checks moved from client-side route guards to server-side middleware — critical ownership bypass fix
- Added `Referrer-Policy: strict-origin-when-cross-origin` to all responses

---

## [1.0.1] — 2025-12-05

### Fixed
- Crash on startup when `DATABASE_URL` was unset (now exits with descriptive error)
- Paste creation returning `201 Created` with no `Location` header ([#12](../../issues/12))
- CORS preflight rejected for `DELETE` method ([#15](../../issues/15))
- Docker health check timing out on cold start ([#18](../../issues/18))

---

## [1.0.0] — 2025-11-28

### Added
- **Initial public release** of the Pastebin project
- Full CRUD for pastes via REST API
- Public, unlisted, and private visibility modes
- Configurable paste expiry: 1 hour → never
- Anonymous paste creation (no account required)
- User accounts with dashboard
- Short URL generation (base-62 encoded IDs)
- REST API v1 with `Bearer` token authentication
- Rate limiting: 60 req/min anonymous · 300 req/min authenticated
- Admin dashboard with usage metrics and moderation
- Docker Compose local dev environment
- GitHub Actions CI (lint / test / build)

---

[Unreleased]: ../../compare/v1.3.0...HEAD
[1.3.0]: ../../compare/v1.2.1...v1.3.0
[1.2.1]: ../../compare/v1.2.0...v1.2.1
[1.2.0]: ../../compare/v1.1.0...v1.2.0
[1.1.0]: ../../compare/v1.0.1...v1.1.0
[1.0.1]: ../../compare/v1.0.0...v1.0.1
[1.0.0]: ../../releases/tag/v1.0.0
