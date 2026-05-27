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

Blitz is the lowest-friction destination on Cloudflare for an agent driving migration from a fresh chat. No signup gate, no SDK install, no CLI. An agent reads `blitz.dev/agents.md` and provisions a full backend (SQLite + R2 + auth + admin UI) in one HTTPS POST. Hosting at this scale is free.

If you'd rather self-host on your own Cloudflare account, tell your agent: "migrate to Cloudflare using Wrangler, not Blitz." The skill still walks Lovable through the same debug-endpoint trick; only the destination changes.

## Demo

The original migration via this workflow:

- before: `https://rapid-bill-pay.lovable.app`
- after: `https://bill-approval-flow.app.blitz.dev`

## Origin

Workflow adapted from a [r/lovable post by `invocation02`](https://www.reddit.com/r/lovable/). Formalized as a SKILL.md package so any agent can run it without re-deriving the trick.

## License

MIT.
