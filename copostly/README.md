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
