# Changelog

The Sensefold MCP server is hosted at `https://api.sensefold.app/mcp`. There
is nothing to install or upgrade on the client side; entries here describe
what a connected client sees.

## 2026-09-12

- Published `server.json` and this repository for the MCP registry and
  directories. No server-side change.

## Current contract (as of 2026-09-12)

- Transport: Streamable HTTP. Auth: OAuth 2.1 (PKCE, dynamic client
  registration) or a revocable Agent key sent as `Authorization: Bearer`.
- Tiers shape `tools/list`: 6 tools on `read_only`, 10 on `edit`, 11 on
  `full`.
- Search results carry `sensefoldUrl` (citation link), `sourceUrl` (original
  capture source), and `chunkRef` (matched section, with PDF page numbers).
  `get_item` accepts `chunk` to read one section with context.
- `search_hub` reports `rerankApplied` and `vectorSearchApplied` so a client
  knows when to rephrase.
- Writes are idempotent by client-supplied UUID and guarded by
  `expectedVersion`; every edit is a revision the user can diff and revert.
