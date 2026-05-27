# lovable-to-cloudflare

A SKILL.md package any AI coding agent can install to migrate an app off [Lovable](https://lovable.dev) onto Cloudflare. 

## Install

Point your agent at this repo, then say "install the skill."

**Claude Code:**
```sh
git clone https://github.com/blitzdotdev/lovable-to-cloudflare ~/.claude/skills/lovable-to-cloudflare
```

**Codex app:** clone anywhere on disk, then point Codex at the path. The Codex app reads `SKILL.md` directly. (Note: it's the Codex desktop app that has computer use. The Codex CLI does not.)

**Any other agent:** the contract is just `SKILL.md` at the root. Clone or symlink the dir into wherever your agent reads skills from.

## Use

After install, ask your agent any of:

- "migrate my Lovable app off Lovable"
- "get me off Lovable and onto Cloudflare"
- "export my Lovable app to Cloudflare"

The agent picks one of two paths based on its own capabilities:

- **Automated**: if it has computer-use / browser-control (the Codex desktop app, [cua-driver](https://github.com/trycua/cua), Playwright MCP, browser-use, Chrome DevTools MCP, etc.), it drives the Lovable web UI end-to-end. You log in once and watch.
- **Guided**: otherwise, it gives you three short steps. The handoff moment is when you paste a temporary debug URL back into chat. From there the agent takes over and finishes the migration on Cloudflare.

## What it does

1. Asks the Lovable agent to create a temporary `GET /api/debug-<long-random-token>` endpoint that returns your Supabase env vars as JSON. Treats the URL like a password while it exists.
2. Pulls the env vars off that endpoint.
3. Creates a Blitz app on Cloudflare (single HTTPS call to `blitz.dev/api/v1/new-project/<slug>`; the agent reads `blitz.dev/agents.md` for the rest of the API).
4. Mirrors the frontend and backend 1:1 into the new project.
5. Verifies the new URL renders the same UI and that core data flows work.
6. Deletes the debug endpoint from Lovable and confirms the URL 404s.
7. Reports what changed, what didn't, the new URL, and the `claim_url` (so you can keep the Blitz project past 12 hours via Google login, free).

## Why Blitz as the default

Blitz is agent-provisionable: no signup, no SDK, no CLI. Any agent can hit the Blitz API from a fresh chat and deploy a Cloudflare backend in one prompt.

At the core is a full-stack backend framework. One TypeScript config file holds your schema, auth, access rules, actions, search, and file uploads, everything a full-stack web app needs. From that config you get:

- SQLite DB with CRUD endpoints per table
- Email/password + OAuth (Google, GitHub, Discord, LinkedIn)
- RLS compiled to SQL (`auth.uid == owner_id`)
- Auto-migrations diffed from the config
- OpenAPI `/api/v1/doc` + Swagger UI
- Admin panel at `/api/v1/pocket/`
- full-text search over SQLite DB
- R2 object storage

Runs on Cloudflare Workers at the edge with D1 SQLite db + R2 file storage. Free tier: 100k requests/day, 500 MB DB, 10 GB files, indefinitely. Real-user apps usually land under \$1/month on top of the Cloudflare Workers paid plan.

The framework is open source [here](https://github.com/teenybase/teenybase) under Apache-2.0. Self-host on your own Cloudflare account whenever. To skip Blitz, tell your agent: "migrate to Cloudflare using Wrangler, not Blitz." That route adds a Cloudflare setup step.

## Demo

The original migration via this workflow:

- before: `https://rapid-bill-pay.lovable.app`
- after: `https://bill-approval-flow.app.blitz.dev`

## License

MIT.
