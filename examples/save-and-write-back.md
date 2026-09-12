# Save and write back

Tier: `edit`. Client: Claude Code with the Sensefold server added over OAuth.

**User:** Save this thread about Cloudflare Workers cron limits, then add a
note with the two numbers that matter to me, and tag it `infra`.

**Agent → `save_link`**

Generate one UUID before the first attempt and reuse it on retries. A second
call with the same UUID returns the same item instead of creating a duplicate.

```json
{
  "id": "6d0c5a2e-4b1f-4b4b-9d0e-2a0f9d6c1a11",
  "url": "https://community.cloudflare.com/t/…"
}
```

Capture and enrichment run on the server; this spends credits the same way a
save from the extension does. Check `get_quota` first if you are saving many
links.

**Agent → `save_note`** (a note is stored verbatim and never AI-enriched):

```json
{
  "id": "0b9e1f4c-7c3d-4a6b-8f2a-5e1c9d3b7a22",
  "authoredContent": "Workers cron: max 5 triggers per Worker on the free plan; minimum granularity 1 minute. Source: saved thread.",
  "hubTitle": "Cloudflare Workers cron limits"
}
```

**Agent → `get_item`** on the new note to obtain `version`, then
**`update_tags`**:

```json
{
  "id": "0b9e1f4c-…",
  "expectedVersion": 1,
  "tags": ["infra"]
}
```

If someone edited the item in between, the server answers `VERSION_CONFLICT`.
Re-read, take the new `version`, retry once.

What the user sees afterwards:

- Both items in the library, the link with its summary and tags, the note
  exactly as written.
- In the note's **History** drawer: version 1 (created by the agent), version
  2 (tags replaced by the agent), each with a field-level diff and a
  **Revert** button.
- The same events in the account-wide **Activity** page, attributed to the
  Claude Code connection.

Rules the agent followed:

- Never rewrote the user's existing notes; it created a new one.
- Used `expectedVersion` on the write.
- Did not put `delete_item` anywhere near this task; it is not visible on
  `edit` anyway.
