# CoPostly overlay

This branch (`copostly`) = upstream Hermes Agent **v0.22.6** + this folder. It is a submodule of
`list91/copost_main` (`hermes-agent/`); CI unit `hermes` builds `copostly/Dockerfile` with this folder as context.

- `Dockerfile` — the official image of the same release, pinned by digest, plus our managed config.
- `config.yaml` — managed scope (`/etc/hermes/config.yaml`): only the MCP server `space` (our backend), every
  built-in toolset, memory, skills and self-improvement off. No secrets here (the fork is public).

Runtime env (on the server, `.env.hermes`): `OPENROUTER_API_KEY`, `HERMES_MODEL`, `HERMES_MODEL_PROVIDER=openrouter`,
`HERMES_MODEL_BASE_URL=https://openrouter.ai/api/v1`, `API_SERVER_KEY`, `API_SERVER_HOST=0.0.0.0`,
`API_SERVER_PORT=8642`, `SPACE_MCP_URL`, `SPACE_MCP_TOKEN`, `TELEGRAM_BOT_TOKEN`, `TELEGRAM_ALLOWED_USERS`.

Upgrading Hermes: rebase this branch on the new tag, update the digest in `Dockerfile`, re-check the config keys.

## Persona (Buzzy)

`config.yaml` → `agent.system_prompt` is the short Buzzy brief (Russian-speaking assistant of the CoPostly Space board;
works only through the `space` tools; reads passports first; paid/irreversible actions are confirmed by the person on the
board; in Telegram: no ticket, say so when the board is closed). Why this key and not `SOUL.md`: Hermes applies
`agent.system_prompt` as the ephemeral system prompt of its messaging gateway (Telegram) and re-reads the managed
`/etc/hermes/config.yaml` on every deploy, whereas `/opt/data/SOUL.md` lives in the data volume and is seeded only once
(an existing volume keeps Nous' default identity). The brief is appended after that identity, so it says «Hermes — лишь
движок». The Runs API (board) does NOT read `agent.system_prompt`: the backend sends the same rules per run in
`instructions` (`app/api/hermes_runs.py`, `BUZZY_BRIEF`); a backend test keeps the shared sentences identical.
No `$` in the text (config values are env-expanded).

## Telegram and the MCP toolset

`platform_toolsets.telegram: []` leaves exactly the MCP server `space` for Telegram: `_get_platform_tools` ->
`_merge_mcp_servers` adds every enabled `mcp_servers` entry unless the list names servers or `no_mcp`
(checked by running it on this config: `telegram -> ['space']`, `api_server -> ['space']`). Telegram sessions have no run
ticket; the backend maps ticket-less calls to a fixed owner (see below).

## Backend env that goes with this (server `.env.self-service`)

`HERMES_TELEGRAM_USER_ID` (Space account the bot works as: Admin `5e1f5e1f-0000-4000-8000-000000000001`),
`HERMES_TELEGRAM_COMPANY_ID` (company uuid, prod `copostly` = `a7922094-8f07-4330-887f-42b7f88fb24f`),
`HERMES_MCP_EXTRA_TOOLSETS` (default `copostly,feedback`: read-only CoPostly data + feedback; see
`self-service-app/docs/agent-presence.md`, «Hermes: какие тулы и Telegram»). Telegram allow-list stays in this
container's `TELEGRAM_ALLOWED_USERS`.
