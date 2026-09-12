# Headless agent with an Agent key

Tier: `read_only`. Environment: a nightly job with no browser, so OAuth is
not an option.

1. The user creates an Agent key in
   [Settings → For agents](https://app.sensefold.app/?nav=settings.agents),
   picks **Read only**, and copies it once. The full key is never shown again.
2. The key goes into the job's secret store as `SENSEFOLD_AGENT_KEY`. It is
   not committed, not logged, not pasted into a chat.
3. The job's MCP client sends it as a bearer token on every request.

Claude Code, non-interactive:

```bash
claude mcp add --transport http sensefold https://api.sensefold.app/mcp \
  --header "Authorization: Bearer $SENSEFOLD_AGENT_KEY"
```

Generic JSON (`.mcp.json`; VS Code uses `servers` instead of `mcpServers`):

```json
{
  "mcpServers": {
    "sensefold": {
      "type": "http",
      "url": "https://api.sensefold.app/mcp",
      "headers": { "Authorization": "Bearer ${SENSEFOLD_AGENT_KEY}" }
    }
  }
}
```

Self-check on first run: `tools/list` should return 6 tools (`search_hub`,
`list_items`, `get_item`, `get_quota`, `search`, `fetch`). Ten or eleven means
the key was created with a higher tier than intended; revoke it and create a
read-only one.

Typical nightly task:

- `list_items` with `start` and `end` covering yesterday to pull what the user saved.
- `get_item` on each, windowed (`windowStart` = previous `nextStart` while
  `truncated` is true).
- Produce a digest elsewhere. Nothing is written back; the key cannot.

If the job ever returns `401` with an empty body, the header is missing or
malformed, or the key was revoked. If the key leaks, revoke it in Settings
and create another; rotation is the whole recovery.
