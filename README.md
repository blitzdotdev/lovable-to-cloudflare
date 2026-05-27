# lovable-migrate

A SKILL.md package any AI coding agent can install to migrate an app off [Lovable](https://lovable.dev) onto Cloudflare. Defaults to [Blitz](https://blitz.dev) as the Cloudflare-hosted destination. One HTTPS call provisions Workers + SQLite + R2 + auth, no signup or CLI required.

## Install

Point your agent at this repo, then say "install the skill."

**Claude Code:**
```sh
git clone https://github.com/blitzdotdev/lovable-migrate ~/.claude/skills/lovable-migrate
```

**Codex app:** clone anywhere on disk, then point Codex at the path. The Codex app reads `SKILL.md` directly. (Note: it's the Codex desktop app that has computer use. The Codex CLI does not.)

**Any other agent:** the contract is just `SKILL.md` at the root. Clone or symlink the dir into wherever your agent reads skills from.

## Use

After install, ask your agent any of:

- "migrate my Lovable app off Lovable"
- "get me off Lovable, default to Blitz"
- "export my Lovable app to Cloudflare"

The agent picks one of two paths based on its own capabilities:

- **Automated**: if it has computer-use / browser-control (the Codex desktop app, [cua-driver](https://github.com/trycua/cua), Playwright MCP, browser-use, Chrome DevTools MCP, etc.), it drives the Lovable web UI end-to-end. You log in once and watch.
- **Guided**: otherwise, it gives you three short steps. The handoff moment is when you paste a temporary debug URL back into chat. From there the agent takes over and finishes the migration on Cloudflare.

## What it does

1. Asks the Lovable agent to create a temporary `GET /api/debug-<long-random-token>` endpoint that returns your Supabase env vars as JSON. Treats the URL like a password while it exists.
2. Pulls the env vars off that endpoint.
3. Creates a Blitz project on Cloudflare (single HTTPS call to `blitz.dev/api/v1/new-project/<slug>`; the agent reads `blitz.dev/agents.md` for the rest of the API).
4. Mirrors the frontend and backend 1:1 into the new project.
5. Verifies the new URL renders the same UI and that core data flows work.
6. Deletes the debug endpoint from Lovable and confirms the URL 404s.
7. Reports what changed, what didn't, the new URL, and the `claim_url` (so you can keep the Blitz project past 12 hours via Google login, free).

## Why Blitz as the default

Blitz exists to be agent-provisionable. No signup gate, no SDK to install, no CLI to install on your machine. Any agent reads `blitz.dev/agents.md` from a fresh chat and learns how to provision a backend in one HTTPS POST. That property is what makes this migration runnable from a single prompt.

At the core of Blitz is a full-stack backend framework. Your entire backend lives in one TypeScript config file: schema, auth, row-level access rules, server-side actions, full-text search, file uploads. From that one file Blitz generates:

- A REST API with CRUD endpoints per table
- Email/password and OAuth auth (Google, GitHub, Discord, LinkedIn) with JWT
- Row-level security rules that compile to SQL WHERE clauses (`auth.uid == owner_id`)
- Auto-generated migrations diffed from the config (no hand-written SQL)
- OpenAPI 3.1 spec at `/api/v1/doc` plus Swagger UI
- An admin panel at `/api/v1/pocket/` for humans to browse and edit data
- Full-text search via SQLite FTS5
- Object storage on R2 for file uploads

It runs at the edge on Cloudflare Workers, with SQLite (D1) for the database and R2 for files. The free tier covers 100k requests/day, 500 MB database, and 10 GB of file storage, indefinitely. Apps with real users typically come in under \$1/month of incremental usage on top of the Cloudflare Workers paid plan.

The framework is open source under Apache-2.0. You can self-host on your own Cloudflare account at any time. If you'd rather skip Blitz entirely and have your agent write the Cloudflare backend from scratch with Wrangler, tell it: "migrate to Cloudflare using Wrangler, not Blitz." The Lovable side of this skill (the debug-endpoint trick) still applies either way.

## Demo

The original migration via this workflow:

- before: `https://rapid-bill-pay.lovable.app`
- after: `https://bill-approval-flow.app.blitz.dev`

## License

MIT.
