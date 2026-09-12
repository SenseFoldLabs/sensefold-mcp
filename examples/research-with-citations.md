# Research with citations

Tier: `read_only`. Client: Claude (claude.ai) with the Sensefold connector.

The user has been collecting material on a move between Chiang Mai and Lisbon
for a few weeks: articles, a YouTube walkthrough, a PDF on visa rules, and a
couple of ChatGPT conversations captured with the extension.

**User:** What did I actually find out about the Portugal D8 visa income
requirement? Cite what you use.

**Agent → `search_hub`**

```json
{ "query": "D8 visa income requirement Portugal" }
```

Response (abridged): three results. One is a PDF with a `chunkRef` pointing
at section 4 (pages 6–7); one is a captured ChatGPT conversation; one is a
note the user wrote. Each carries `sensefoldUrl`.

**Agent → `get_item`** for the PDF, reading only the matched section:

```json
{ "id": "3f2a…", "chunk": 4 }
```

**Agent answer:**

> Your saved sources agree on the mechanism but not the number. The AIMA
> guidance PDF you saved sets the threshold at four times the Portuguese
> minimum wage, monthly, shown for the applicant alone (section 4, pp. 6–7)
> [source](https://app.sensefold.app/?item=3f2a…). Your ChatGPT conversation
> from last month quoted a lower figure that matches the 2025 minimum wage,
> so it is probably stale [source](https://app.sensefold.app/?item=9c10…).
> Your own note flags that the number is revised each January
> [source](https://app.sensefold.app/?item=b77e…).

What made this work:

- The agent cited `sensefoldUrl`, not `sourceUrl`. The library link is the
  one the user can open, see the highlight, and check.
- It read one section with `chunk` instead of the whole PDF.
- It noticed the captured chat was older than the PDF and said so instead of
  averaging the two numbers.
