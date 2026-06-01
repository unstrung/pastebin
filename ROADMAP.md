# Roadmap

This document outlines the planned evolution of the Pastebin project — from active
milestones to the long-term backlog and stretch goals. It is a living document updated
as priorities shift and community feedback is gathered.

> **Legend:**
> - ✅ Completed · 🔄 In Progress · 🗓 Planned · 💡 Stretch Goal · ❄️ Backlog

---

## Milestones

### ✅ Milestone 1 — Foundation (v1.0.0) · *Shipped November 2025*

| # | Feature | Status |
|---|---------|--------|
| 1.1 | Create, read, update, delete pastes via REST API | ✅ |
| 1.2 | Public / unlisted / private visibility modes | ✅ |
| 1.3 | Configurable paste expiry (1h → never) | ✅ |
| 1.4 | Anonymous paste creation | ✅ |
| 1.5 | User accounts with paste history dashboard | ✅ |
| 1.6 | Short URL generation (base-62 encoded IDs) | ✅ |
| 1.7 | Rate limiting (anonymous + authenticated tiers) | ✅ |
| 1.8 | Admin moderation dashboard | ✅ |
| 1.9 | Docker Compose local dev environment | ✅ |
| 1.10 | GitHub Actions CI pipeline (lint / test / build) | ✅ |

---

### ✅ Milestone 2 — Developer Experience (v1.1.0 – v1.2.x) · *Shipped Jan–Apr 2026*

| # | Feature | Status |
|---|---------|--------|
| 2.1 | Syntax highlighting (40+ languages via highlight.js) | ✅ |
| 2.2 | CodeMirror 6 editor with keyboard shortcuts | ✅ |
| 2.3 | Side-by-side paste diffing (`/diff/:id1/:id2`) | ✅ |
| 2.4 | Password-protected pastes (AES-256-GCM) | ✅ |
| 2.5 | Paste collections with shareable URLs | ✅ |
| 2.6 | Embed widget (`<iframe>`) for public pastes | ✅ |
| 2.7 | Webhook callbacks on paste create/delete events | ✅ |
| 2.8 | S3-compatible object storage for attachments | ✅ |
| 2.9 | Standardized API response envelope | ✅ |
| 2.10 | OpenGraph / rich link previews | ✅ |

---

### 🔄 Milestone 3 — Scale & Ecosystem (v1.3.0 – v1.5.0) · *Target: Q3 2026*

| # | Feature | Status |
|---|---------|--------|
| 3.1 | Paste forking | ✅ Shipped in v1.3.0 |
| 3.2 | Paste view & visitor statistics | ✅ Shipped in v1.3.0 |
| 3.3 | `pastectl` CLI client (alpha) | ✅ Shipped in v1.3.0 |
| 3.4 | Expanded syntax highlighting — 50+ languages via Prism.js | 🔄 In progress |
| 3.5 | PostgreSQL migration from file-based storage | 🔄 In progress |
| 3.6 | OAuth2 login (GitHub + Google) | 🗓 v1.4.0 |
| 3.7 | RESTful API v2 with versioned routes | 🗓 v1.4.0 |
| 3.8 | Paste expiry email notifications | 🗓 v1.4.0 |
| 3.9 | `pastectl` CLI — stable release with full CRUD & config profiles | 🗓 v1.5.0 |
| 3.10 | Plugin/extension API for third-party integrations | 🗓 v1.5.0 |

---

### 🗓 Milestone 4 — Collaboration & Teams (v2.0.0) · *Target: Q1 2027*

| # | Feature | Status |
|---|---------|--------|
| 4.1 | Team workspaces with role-based access (Owner / Editor / Viewer) | 🗓 Planned |
| 4.2 | Real-time collaborative editing (CRDT-based, WebSocket) | 🗓 Planned |
| 4.3 | Inline commenting on paste lines | 🗓 Planned |
| 4.4 | Paste review / approval workflow for teams | 🗓 Planned |
| 4.5 | API v2 GA — v1 deprecations removed | 🗓 Planned |
| 4.6 | SSO / SAML 2.0 for enterprise teams | 🗓 Planned |
| 4.7 | Audit log for team paste activity | 🗓 Planned |
| 4.8 | Custom domain support for self-hosted instances | 🗓 Planned |
| 4.9 | Granular API key scopes (read / write / admin) | 🗓 Planned |
| 4.10 | Import from GitHub Gist, Hastebin, and Ghostbin | 🗓 Planned |

---

## Backlog

Items with no committed milestone, triaged by community demand and contributor availability.

### 🧑‍💻 Core Functionality
- ❄️ **Multi-file pastes** — group files in one paste displayed as tabs (à la GitHub Gist)
- ❄️ **Paste templates** — boilerplate starters (bug report, meeting notes, code snippet)
- ❄️ **Revision history** — edit tracking with diff view between versions
- ❄️ **Paste pinning** — pin important pastes to top of dashboard
- ❄️ **Bulk operations** — select multiple pastes to delete, change visibility, or move to collection
- ❄️ **Offline support** — service worker cache for previously viewed pastes
- ❄️ **Print view** — clean printer-friendly stylesheet with syntax highlighting preserved
- ❄️ **QR code generation** — scannable QR code for any paste URL

### 🔌 Integrations & API
- ❄️ **VS Code extension** — create and fetch pastes directly from the editor
- ❄️ **Slack / Discord bot** — post paste previews and create pastes from messages
- ❄️ **GitHub Action** — publish files or build artifacts as pastes in CI/CD
- ❄️ **Zapier / Make connector** — no-code automation triggers for paste events
- ❄️ **GraphQL API** — alternative query interface alongside REST v2

### 🖼 UX & Interface
- ❄️ **Customizable themes** — VS Code Dark+, Solarized, Gruvbox, and more
- ❄️ **Font size & family preferences** — persisted per user account
- ❄️ **Drag-and-drop file upload** — drop a file onto the editor to import its contents
- ❄️ **Focus mode** — distraction-free fullscreen writing environment
- ❄️ **Accessibility audit** — WCAG 2.1 AA compliance review and remediation
- ❄️ **i18n / l10n** — internationalization infrastructure + three language translations

### 📊 Analytics & Admin
- ❄️ **Referral tracking** — see which sites link to your pastes
- ❄️ **Abuse detection** — automated scanning for malware URLs in paste content
- ❄️ **GDPR tooling** — one-click data export and account deletion
- ❄️ **Admin analytics dashboard** — platform-wide metrics (pastes/day, active users, storage)
- ❄️ **Custom retention policies** — per-plan storage quotas and paste retention rules

---

## Stretch Goals

Ambitious ideas requiring deeper research or significant engineering investment.

| # | Goal | Description |
|---|------|-------------|
| 💡 S-1 | **AI Paste Summarizer** | One-click AI summary and title suggestion powered by a local or API-backed LLM |
| 💡 S-2 | **End-to-End Encrypted Pastes** | Zero-knowledge encryption — keys derived client-side, never stored server-side |
| 💡 S-3 | **Federated Paste Sharing** | ActivityPub-based federation across self-hosted instances |
| 💡 S-4 | **Native Mobile Apps** | React Native iOS and Android with offline creation and expiry push notifications |
| 💡 S-5 | **Notebook Mode** | Jupyter-inspired interface mixing code pastes with Markdown cells, sandboxed execution |
| 💡 S-6 | **Paste Marketplace** | Curated, searchable gallery of high-quality public snippets, configs, and templates |
| 💡 S-7 | **Self-Hosted Helm Chart** | Production-ready Kubernetes chart with auto-scaling, PVC, and secrets management |
| 💡 S-8 | **Monetization Tier** | Optional paid plan with higher rate limits, private collections, and priority support |

---

## How to Contribute

1. **Open a Feature Request** using the [Feature Request template](.github/ISSUE_TEMPLATE/feature_request.md)
2. **Join the discussion** — comment on existing issues; 👍 reactions help with prioritization
3. **Submit a PR** — reference the relevant issue and follow the [Contributing Guide](CONTRIBUTING.md)

All backlog and stretch goal items are open for community contribution unless marked `core-team-only`.

---

*Last updated: June 2026 · Maintained by the Pastebin project core team*
