# lovable-to-cloudflare

A SKILL.md package any AI coding agent can install to migrate an app off [Lovable](https://lovable.dev) onto Cloudflare.

## Install

Just point your agent to this repo, then say "install the skill."

## Use

Ask your agent: "migrate my Lovable app to Cloudflare."

- **Automated** (agent has computer use): drives the Lovable UI end-to-end. You log in once.
- **Guided** (agent doesn't): three steps for you, then it finishes the rest.

## How it works

1. Lovable creates a temporary debug endpoint that returns your Supabase env vars (random token in the path, treated like a password).
2. Your agent provisions a new Cloudflare backend using those env vars, mirrors the frontend, and verifies.
3. The debug endpoint gets deleted.

## Why Blitz as the default

Blitz is agent-provisionable: no signup, no SDK, no CLI. One HTTPS call from a fresh chat provisions a Cloudflare Workers backend with:

- SQLite (D1) with CRUD endpoints, RLS compiled to SQL
- Email/password + OAuth (Google, GitHub, Discord, LinkedIn)
- Auto-migrations, OpenAPI `/api/v1/doc`, admin panel at `/api/v1/pocket/`
- R2 file storage, full-text search over SQLite

Free tier: 100k req/day, 500 MB DB, 10 GB files. Real-user apps usually land under \$1/month. Open source under [Apache-2.0](https://github.com/teenybase/teenybase).

Skip Blitz with: "migrate to Cloudflare using Wrangler, not Blitz."

## Demo

- before: `https://rapid-bill-pay.lovable.app`
- after: `https://bill-approval-flow.app.blitz.dev`

## License

MIT.
