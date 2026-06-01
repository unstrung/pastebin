# CLI Reference — pastectl

`pastectl` is the official command-line client for the Pastebin project. It lets you
create, fetch, update, and delete pastes directly from your terminal without opening
a browser.

**Current version:** 1.3.0-alpha

---

## Table of Contents

- [Installation](#installation)
- [Configuration](#configuration)
- [Commands](#commands)
  - [create](#create)
  - [get](#get)
  - [update](#update)
  - [delete](#delete)
  - [list](#list)
  - [fork](#fork)
  - [raw](#raw)
  - [collection](#collection)
  - [config](#config)
  - [login](#login)
  - [logout](#logout)
  - [version](#version)
- [Global Flags](#global-flags)
- [Environment Variables](#environment-variables)
- [Examples](#examples)
- [Exit Codes](#exit-codes)

---

## Installation

### via npm (recommended)

    npm install -g pastectl

### via npx (no install required)

    npx pastectl <command>

### Verify the installation

    pastectl version

---

## Configuration

Before using `pastectl`, point it at your Pastebin instance and optionally set your
API token for authenticated requests.

    pastectl config set url http://localhost:3000
    pastectl config set token YOUR_API_TOKEN

Config is stored in `~/.pastectl/config.json`. You can view the current config at
any time:

    pastectl config list

You can also authenticate interactively using `pastectl login` instead of setting
a token manually — see [login](#login).

---

## Commands

---

### create

Create a new paste. Content can be piped from stdin, read from a file, or typed
interactively.

    pastectl create [file] [flags]

**Arguments**

| Argument | Description |
|----------|-------------|
| `file` | Path to a file whose contents will be pasted. Omit to read from stdin. |

**Flags**

| Flag | Short | Default | Description |
|------|-------|---------|-------------|
| `--title` | `-t` | `"Untitled"` | Paste title (max 256 chars) |
| `--language` | `-l` | `"plaintext"` | Syntax highlighting language |
| `--visibility` | `-v` | `"public"` | `public`, `unlisted`, or `private` |
| `--expiry` | `-e` | `"30d"` (anon) · `"never"` (auth) | `1h`, `1d`, `1w`, `1m`, or `never` |
| `--password` | `-p` | — | Protect the paste with a password |
| `--output` | `-o` | `url` | Output format: `url`, `json`, or `id` |

**Examples**

    # Create a paste from a file
    pastectl create ./script.js --language javascript --expiry 7d

    # Pipe content from stdin
    cat error.log | pastectl create --title "Error Log" --visibility unlisted

    # Create a private paste with a password
    pastectl create ./secrets.txt --visibility private --password mysecretpass

    # Output the full JSON response
    pastectl create ./script.js --output json

**Output**

By default, `pastectl create` prints the paste URL to stdout:

    https://your-domain.com/pst_aB3xZ9

---

### get

Fetch and display a paste by ID.

    pastectl get <id> [flags]

**Arguments**

| Argument | Description |
|----------|-------------|
| `id` | The paste ID (e.g. `pst_aB3xZ9`) |

**Flags**

| Flag | Short | Default | Description |
|------|-------|---------|-------------|
| `--password` | `-p` | — | Password for a protected paste |
| `--output` | `-o` | `content` | Output format: `content`, `json`, or `url` |

**Examples**

    # Print paste content to stdout
    pastectl get pst_aB3xZ9

    # Fetch a password-protected paste
    pastectl get pst_aB3xZ9 --password mysecretpass

    # Get the full JSON response
    pastectl get pst_aB3xZ9 --output json

    # Save paste content to a file
    pastectl get pst_aB3xZ9 > output.js

---

### update

Update the title, content, language, visibility, or expiry of an existing paste.
You must be the paste owner.

    pastectl update <id> [file] [flags]

**Arguments**

| Argument | Description |
|----------|-------------|
| `id` | The paste ID to update |
| `file` | Optional — path to a file to use as new content |

**Flags**

| Flag | Short | Description |
|------|-------|-------------|
| `--title` | `-t` | New title |
| `--language` | `-l` | New syntax language |
| `--visibility` | `-v` | New visibility (`public`, `unlisted`, `private`) |
| `--expiry` | `-e` | New expiry (`1h`, `1d`, `1w`, `1m`, `never`) |

**Examples**

    # Update the title of a paste
    pastectl update pst_aB3xZ9 --title "Updated Script"

    # Replace paste content from a file
    pastectl update pst_aB3xZ9 ./new-script.js

    # Change visibility to unlisted
    pastectl update pst_aB3xZ9 --visibility unlisted

---

### delete

Delete a paste by ID. You must be the paste owner or an admin.

    pastectl delete <id> [flags]

**Arguments**

| Argument | Description |
|----------|-------------|
| `id` | The paste ID to delete |

**Flags**

| Flag | Short | Description |
|------|-------|-------------|
| `--confirm` | `-y` | Skip the confirmation prompt |

**Examples**

    # Delete with confirmation prompt
    pastectl delete pst_aB3xZ9

    # Delete without prompting
    pastectl delete pst_aB3xZ9 --confirm

---

### list

List pastes belonging to the authenticated user.

    pastectl list [flags]

**Flags**

| Flag | Short | Default | Description |
|------|-------|---------|-------------|
| `--limit` | `-n` | `20` | Number of results to show (max `100`) |
| `--visibility` | `-v` | — | Filter by `public`, `unlisted`, or `private` |
| `--language` | `-l` | — | Filter by language |
| `--output` | `-o` | `table` | Output format: `table` or `json` |

**Examples**

    # List your 20 most recent pastes as a table
    pastectl list

    # List only private pastes, output as JSON
    pastectl list --visibility private --output json

    # List your 50 most recent JavaScript pastes
    pastectl list --language javascript --limit 50

**Table Output**

    ID            TITLE            LANGUAGE      VISIBILITY  CREATED
    pst_aB3xZ9    My Script        javascript    public      2026-06-01
    pst_fK7qR2    Error Log        plaintext     unlisted    2026-05-30

---

### fork

Fork a public paste into your own account.

    pastectl fork <id> [flags]

**Arguments**

| Argument | Description |
|----------|-------------|
| `id` | The paste ID to fork |

**Flags**

| Flag | Short | Default | Description |
|------|-------|---------|-------------|
| `--output` | `-o` | `url` | Output format: `url`, `json`, or `id` |

**Examples**

    # Fork a paste and print the new paste URL
    pastectl fork pst_aB3xZ9

    # Fork and get the full JSON response
    pastectl fork pst_aB3xZ9 --output json

---

### raw

Fetch and print the raw plain-text content of a paste. Equivalent to `pastectl get`
with `--output content` but bypasses the API envelope entirely.

    pastectl raw <id> [flags]

**Flags**

| Flag | Short | Description |
|------|-------|-------------|
| `--password` | `-p` | Password for a protected paste |

**Examples**

    # Print raw content
    pastectl raw pst_aB3xZ9

    # Pipe raw content into another command
    pastectl raw pst_aB3xZ9 | grep "error"

    # Save raw content to a file
    pastectl raw pst_aB3xZ9 > recovered.js

---

### collection

Manage paste collections.

    pastectl collection <subcommand> [args] [flags]

**Subcommands**

| Subcommand | Description |
|------------|-------------|
| `create` | Create a new collection |
| `get <id>` | Fetch a collection and list its pastes |
| `delete <id>` | Delete a collection |
| `add <col-id> <paste-id>` | Add a paste to a collection |
| `remove <col-id> <paste-id>` | Remove a paste from a collection |

**Examples**

    # Create a collection
    pastectl collection create --name "Useful Scripts" --visibility public

    # View a collection
    pastectl collection get col_mN4pQ8

    # Add a paste to a collection
    pastectl collection add col_mN4pQ8 pst_aB3xZ9

    # Remove a paste from a collection
    pastectl collection remove col_mN4pQ8 pst_aB3xZ9

    # Delete a collection
    pastectl collection delete col_mN4pQ8 --confirm

---

### config

View and manage `pastectl` configuration.

    pastectl config <subcommand> [args]

**Subcommands**

| Subcommand | Description |
|------------|-------------|
| `set <key> <value>` | Set a configuration value |
| `get <key>` | Print a single configuration value |
| `list` | Print all current configuration values |
| `unset <key>` | Remove a configuration value |

**Configuration Keys**

| Key | Description |
|-----|-------------|
| `url` | Base URL of your Pastebin instance |
| `token` | API Bearer token for authenticated requests |
| `default_language` | Default syntax language for new pastes |
| `default_visibility` | Default visibility for new pastes |
| `default_expiry` | Default expiry for new pastes |
| `output` | Default output format (`url`, `json`, `table`) |

**Examples**

    # Point pastectl at your instance
    pastectl config set url https://your-domain.com

    # Set your API token
    pastectl config set token eyJhbGci...

    # Set defaults for new pastes
    pastectl config set default_language javascript
    pastectl config set default_visibility unlisted
    pastectl config set default_expiry 7d

    # View all config
    pastectl config list

    # Remove the stored token
    pastectl config unset token

---

### login

Authenticate interactively. Prompts for your email and password, retrieves a Bearer
token, and stores it in your config automatically.

    pastectl login [flags]

**Flags**

| Flag | Short | Description |
|------|-------|-------------|
| `--url` | `-u` | Pastebin instance URL (uses config value if already set) |

**Example**

    pastectl login --url https://your-domain.com
    # Email: bill@example.com
    # Password: ************
    # Logged in as bill. Token saved to ~/.pastectl/config.json.

---

### logout

Remove the stored Bearer token from your config.

    pastectl logout

**Example**

    pastectl logout
    # Logged out. Token removed from ~/.pastectl/config.json.

---

### version

Print the current `pastectl` version.

    pastectl version

**Example Output**

    pastectl v1.3.0-alpha

---

## Global Flags

These flags are available on every command.

| Flag | Short | Description |
|------|-------|-------------|
| `--help` | `-h` | Show help for the current command |
| `--json` | | Force JSON output regardless of command default |
| `--no-color` | | Disable colored output |
| `--silent` | `-s` | Suppress all output except errors |
| `--url` | `-u` | Override the configured instance URL for this request |
| `--token` | | Override the configured token for this request |

---

## Environment Variables

Environment variables override values in `~/.pastectl/config.json`.

| Variable | Description |
|----------|-------------|
| `PASTECTL_URL` | Base URL of your Pastebin instance |
| `PASTECTL_TOKEN` | API Bearer token |
| `PASTECTL_OUTPUT` | Default output format (`url`, `json`, `table`, `content`) |
| `NO_COLOR` | Set to any value to disable colored output |

**Example**

    PASTECTL_URL=https://your-domain.com PASTECTL_TOKEN=mytoken pastectl list

---

## Examples

### Post a file and copy the URL to clipboard

    pastectl create ./my-script.js --language javascript | pbcopy   # macOS
    pastectl create ./my-script.js --language javascript | xclip    # Linux

### Share a paste that expires in one hour

    cat /var/log/app.log | pastectl create --title "App Logs" --expiry 1h --visibility unlisted

### Fetch a paste and run it immediately

    pastectl raw pst_aB3xZ9 | node

### Back up all your pastes to local files

    pastectl list --output json | jq -r '.[].id' | while read id; do
      pastectl raw "$id" > "$id.txt"
      echo "Saved $id"
    done

### Create a private paste in CI

    PASTECTL_URL=$PASTE_URL PASTECTL_TOKEN=$PASTE_TOKEN \
      pastectl create ./build.log --title "Build Log #$CI_RUN_ID" \
      --visibility private --expiry 7d

---

## Exit Codes

| Code | Meaning |
|------|---------|
| `0` | Success |
| `1` | General error (see stderr for details) |
| `2` | Invalid usage — bad flags or missing arguments |
| `3` | Authentication error — missing or invalid token |
| `4` | Not found — paste or collection does not exist |
| `5` | Rate limited — too many requests, retry after backoff |
| `6` | Network error — could not reach the Pastebin instance |

---

*CLI Reference v1.3.0-alpha · Last updated June 2026*
