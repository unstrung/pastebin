# Pastebin

> A lightweight, self-hosted text sharing platform for code snippets, notes, and documents.

Pastebin is a lightweight, self-hosted text sharing platform that lets users create,
manage, and share code snippets, notes, and documents through a clean REST API and web
interface. It supports public, unlisted, and private paste visibility, configurable
expiry, password-protected pastes, syntax highlighting for 50+ languages, and short URL
generation — all with no account required for anonymous use. Built on Node.js and
PostgreSQL, it ships with a Docker Compose development environment, rate limiting, an
admin moderation dashboard, and a growing ecosystem of integrations including webhooks,
an embed widget, S3-compatible attachment storage, and a command-line client, making it
a solid foundation for both personal use and team deployments.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [Configuration](#configuration)
- [API Overview](#api-overview)
- [CLI Client](#cli-client)
- [Docker](#docker)
- [Project Structure](#project-structure)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [Community](#community)
- [License](#license)

---

## Features

### Core
- Create, read, update, and delete pastes via REST API
- Public, unlisted, and private visibility modes
- Configurable paste expiry — 1 hour, 1 day, 1 week, 1 month, or never
- Anonymous paste creation — no account required
- Short URL generation via base-62 encoded IDs
- Raw view endpoint at `/raw/:id` with correct `Content-Type: text/plain`

### Editor & Display
- Syntax highlighting for 50+ languages via Prism.js
- CodeMirror 6 editor with keyboard shortcuts (`Ctrl+S` save, `Ctrl+Shift+F` fullscreen)
- Dark mode toggle persisted via `localStorage`
- OpenGraph meta tags for rich link previews

### Sharing & Collaboration
- Password-protected pastes (AES-256-GCM, key never stored server-side)
- Paste collections with shareable URLs
- Paste forking — clone any public paste into your own account
- Side-by-side paste diffing at `/diff/:id1/:id2`
- Embeddable `<iframe>` widget auto-generated for any public paste

### API & Integrations
- REST API v1 with `Bearer` token authentication
- Webhook callbacks on paste create/delete events
- S3-compatible object storage for paste attachments
- `pastectl` command-line client (alpha)
- `X-Request-ID` header propagated on all responses for request tracing

### Admin & Operations
- Rate limiting — 60 req/min anonymous · 300 req/min authenticated
- Admin dashboard with usage metrics, abuse reports, and paste moderation
- Health check endpoint at `GET /health`
- Paste view count, unique visitor count, and last-accessed timestamp per paste

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Runtime | Node.js v20.x LTS |
| Framework | Express v4 |
| Database | PostgreSQL 15 |
| Object Storage | S3-compatible (AWS S3, MinIO, Cloudflare R2) |
| Editor | CodeMirror 6 |
| Syntax Highlighting | Prism.js |
| Encryption | AES-256-GCM (Node.js `crypto`) |
| CI/CD | GitHub Actions |
| Containerization | Docker + Docker Compose |

---

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) v20.x LTS or later
- [npm](https://www.npmjs.com/) v9+
- [Docker](https://www.docker.com/) and Docker Compose
- [Git](https://git-scm.com/)

### Installation

    # 1. Clone the repository
    git clone https://github.com/YOUR_USERNAME/pastebin.git
    cd pastebin

    # 2. Install dependencies
    npm install

    # 3. Copy the example environment file and configure it
    cp .env.example .env

    # 4. Start the database and supporting services
    docker compose up -d

    # 5. Run database migrations
    npm run db:migrate

    # 6. Start the development server
    npm run dev

The app will be available at http://localhost:3000.

### Running Tests

    npm test              # run the full test suite
    npm run test:watch    # run in watch mode
    npm run test:coverage # generate a coverage report

### Linting

    npm run lint          # check for lint errors
    npm run lint:fix      # auto-fix lint errors

---

## Configuration

All configuration is managed via environment variables. Copy `.env.example` to `.env`
and fill in the values before starting the app.

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `DATABASE_URL` | ✅ | — | PostgreSQL connection string |
| `PORT` | | `3000` | Port the app listens on |
| `NODE_ENV` | | `development` | `development`, `production`, or `test` |
| `JWT_SECRET` | ✅ | — | Secret key for signing auth tokens |
| `RATE_LIMIT_ANON` | | `60` | Requests/min for anonymous users |
| `RATE_LIMIT_AUTH` | | `300` | Requests/min for authenticated users |
| `S3_ENDPOINT` | | — | S3-compatible storage endpoint URL |
| `S3_BUCKET` | | — | Storage bucket name |
| `S3_ACCESS_KEY` | | — | Storage access key |
| `S3_SECRET_KEY` | | — | Storage secret key |
| `DEFAULT_EXPIRY_ANON` | | `30d` | Default paste expiry for anonymous users |
| `WEBHOOK_SECRET` | | — | Secret for signing outbound webhook payloads |

---

## API Overview

All endpoints return a standard JSON response envelope:

    {
      "data": { ... },
      "meta": { "request_id": "abc123", "timestamp": "2026-06-01T12:00:00Z" },
      "errors": []
    }

Authentication uses `Bearer` tokens passed in the `Authorization` header.

### Pastes

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/api/paste/:id` | Optional | Fetch a paste by ID |
| `POST` | `/api/paste` | Optional | Create a new paste |
| `PUT` | `/api/paste/:id` | Required | Update an existing paste |
| `DELETE` | `/api/paste/:id` | Required | Delete a paste |
| `GET` | `/raw/:id` | Optional | Fetch paste content as plain text |
| `POST` | `/api/paste/:id/fork` | Required | Fork a paste into your account |

### Collections

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/api/collection/:id` | Optional | Fetch a collection |
| `POST` | `/api/collection` | Required | Create a new collection |
| `PUT` | `/api/collection/:id` | Required | Update a collection |
| `DELETE` | `/api/collection/:id` | Required | Delete a collection |

### Users

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `POST` | `/api/auth/register` | — | Register a new account |
| `POST` | `/api/auth/login` | — | Log in and receive a token |
| `GET` | `/api/user/pastes` | Required | List pastes for the current user |

### Utility

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/health` | — | Service health check |
| `GET` | `/diff/:id1/:id2` | Optional | Side-by-side paste diff |

> Full API reference documentation is available in [`/docs/api.md`](docs/api.md).

---

## CLI Client

`pastectl` is the official command-line client for Pastebin (currently in alpha).

### Install

    npm install -g pastectl

### Usage

    # Create a paste from a file
    pastectl create ./my-script.js --lang javascript --expiry 7d

    # Fetch a paste
    pastectl get <paste-id>

    # Delete a paste
    pastectl delete <paste-id>

    # Configure your instance and API token
    pastectl config set url http://localhost:3000
    pastectl config set token YOUR_API_TOKEN

Full CLI documentation is available in [`/docs/cli.md`](docs/cli.md).

---

## Docker

### Development

    docker compose up -d

This starts PostgreSQL and any supporting services. The app itself runs via `npm run dev`
for hot reload support.

### Production

    docker compose -f docker-compose.prod.yml up -d

The production Compose file includes the app container, PostgreSQL, and a reverse proxy.
Make sure all required environment variables are set before starting.

### Health Check

The container exposes a health check on `GET /health`. The Docker Compose file is
configured with an appropriate `start_period` to avoid false negatives during cold start.

---

## Project Structure

    pastebin/
    ├── .github/
    │   └── ISSUE_TEMPLATE/     # Bug report, feature request, security, task templates
    ├── docs/
    │   ├── api.md              # Full API reference
    │   └── cli.md              # CLI client documentation
    ├── src/
    │   ├── controllers/        # Route handler functions
    │   ├── middleware/         # Auth, rate limiting, error handling
    │   ├── models/             # Database models and queries
    │   ├── routes/             # Express route definitions
    │   ├── services/           # Business logic layer
    │   └── utils/              # Shared utilities (crypto, validation, etc.)
    ├── test/                   # Integration and end-to-end tests
    ├── .env.example            # Environment variable template
    ├── docker-compose.yml      # Development Compose file
    ├── docker-compose.prod.yml # Production Compose file
    ├── CHANGELOG.md            # Version history
    ├── CODE_OF_CONDUCT.md      # Community standards
    ├── CONTRIBUTING.md         # Contribution guidelines
    ├── ROADMAP.md              # Project roadmap and backlog
    └── README.md               # This file

---

## Roadmap

See [ROADMAP.md](ROADMAP.md) for the full list of planned milestones, backlog items,
and stretch goals. Highlights coming in the next release:

- OAuth2 login (GitHub + Google)
- RESTful API v2
- Paste expiry email notifications
- `pastectl` CLI stable release

---

## Contributing

Contributions are welcome and appreciated! Please read [CONTRIBUTING.md](CONTRIBUTING.md)
for details on the development workflow, commit message conventions, branch naming, and
the pull request process.

**Quick start:**

    git checkout -b feat/your-feature-name
    # make your changes
    npm run lint && npm test
    git commit -m "feat: describe your change"
    # open a pull request

For bugs, use the [Bug Report template](.github/ISSUE_TEMPLATE/bug_report.md).
For features, use the [Feature Request template](.github/ISSUE_TEMPLATE/feature_request.md).
For security issues, use [GitHub Security Advisories](../../security/advisories/new).

---

## Community

- 📋 [Roadmap](ROADMAP.md) — see what's planned
- 📝 [Changelog](CHANGELOG.md) — see what's changed
- 🤝 [Contributing Guide](CONTRIBUTING.md) — how to contribute
- 📜 [Code of Conduct](CODE_OF_CONDUCT.md) — community standards
- 💬 [GitHub Discussions](../../discussions) — questions and ideas

---

## License

This project is licensed under the [MIT License](LICENSE).

---

*Built with ❤️ by the Pastebin project contributors.*
