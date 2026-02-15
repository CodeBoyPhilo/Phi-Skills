---
name: search-conference
description: Use when tasks involve semantically matching a user's idea (query or example papers) to papers in a specific OpenReview venue. Uses `embed-papers` to crawl metadata, build/use embedding caches, run cosine-similarity search, then produces a short, grouped Markdown reading list with brief rationales.
---

# When to use

- Need to find papers in an OpenReview venue that match one or more ideas/topics.

# Dependencies

- `embed-papers` must be installed and available in `PATH`.
  - Check with: `command -v embed-papers`
  - Install with: `pip install embed-papers`
- OpenReview must be reachable.
- `OPENAI_API_KEY` is required to compute embeddings of the papers (cached) or the search intent (not cached).
  - Check with: `printenv OPENAI_API_KEY`
  - If missing and embeddings are required: stop and tell the user how to set it (immediate stop).

# Inputs from user

- Venue (one of):
  - `venue_id` (preferred), e.g. `ICLR.cc/2024/Conference`
  - OR `{conference, year}` to derive `venue_id` as `{CONF}.cc/{YEAR}/Conference`, e.g. `NeurIPS`, `2025`
- Search intent (one of):
  - `query` (string of ideas)
  - OR `examples` (list of `{title, abstract}` objects)
- Optional:
  - `top_k` (default: 100) for retrieval breadth

# CLI contract (how to interpret tool output)

- Success envelope: `{ ok: true, schema_version: "1", command, data }`
- Error envelope: `{ ok: false, schema_version: "1", command, error: { type, message } }`
- Always parse stdout as JSON.
- Treat any `ok=false` as a terminal error unless the error section below says otherwise.

# HARD CONSTRAINT (TOOLS):

- Do NOT call Read/Glob/Grep on any cache/embedding files or directories (e.g. anything under .cache/ or any path containing "cache", "embedding", "paper", "atlas").
- Treat caches as opaque implementation details. Never inspect them “just to check”.
- If you need cache status, ONLY use `embed-papers warm-cache` and rely on its JSON stdout.
- If a command outputs a cache path, DO NOT open it; proceed using the CLI utilities.

# Pipeline

1. Resolve `venue_id`
   - If the user gave `{conference, year}`, build: `{CONF}.cc/{YEAR}/Conference`
   - If ambiguous, ask a single clarifying question (conference acronym + year).

2. Crawl venue metadata (idempotent)
   - Run:
     - `embed-papers crawl --venue-id "<venue_id>" --skip-if-exists`
   - Record:
     - `data.output_file`
     - `data.total`

3. Ensure embeddings are available (cache)
   - Run:
     - `embed-papers warm-cache --venue-id "<venue_id>"`
   - If this fails due to missing API key, stop and instruct the user to set `OPENAI_API_KEY`.
   - This command also computes the embedding if no cache is found.
   - You MUST NOT access the cache.
   - You MUST use the package's provided utility.

4. Search (choose based on user input)
   - Query mode:
     - `embed-papers search --venue-id "<venue_id>" --query "<query>" --top-k <top_k>`
   - Examples mode:
     - If needed, write a temporary JSON file containing:
       - `[{"title":"...","abstract":"..."}, ...]`
     - Then run:
       - `embed-papers search --venue-id "<venue_id>" --examples-file "<tmp.json>" --top-k <top_k>`

5. Organize results (post-processing)
   - Group primarily by `primary_area` (if present).
   - Within groups, prefer papers with clear overlap to the query/examples.
   - For each recommended paper, add agent judgment notes:
     - why it matches
     - what seems novel/different
     - caveats (weak match, missing abstract, unclear claims, etc.)

# Report requirements (Markdown only)

- Output is a Markdown report only (no raw JSON).
- Keep the final recommendation list short: 5-10 papers max.
- Do not output a full ranked list or appendix by default (only if the user asks).

## What I'd start with

- Begin this section with a short, casual sentence (lowercase is fine).
  - Example: "here's what i recommend you to read as a beginning."
- Then list 5-10 papers.
- Each item must include:
  - Title (bold)
  - OpenReview link
  - 1-2 sentence rationale (fit + why it matters, use italic to emphasis)

## How I organized it

- Briefly explain grouping logic and where judgment calls were applied.
- Note missing metadata (e.g., missing abstracts) when relevant.

## Why these stand out

- Use informal labels in the narrative (no formal rubric), e.g.:
  - "the obvious hits"
  - "the surprisingly relevant ones"
  - "the quirky but promising picks"

# Error handling

- `NoPapersFoundError`
  - Likely invalid `venue_id`; suggest the pattern `{CONF}.cc/{YEAR}/Conference` and ask for the correct venue.
- `CacheMissRequiresApiKeyError`
  - Instruct the user to set `OPENAI_API_KEY` and retry.
- `OpenReviewRequestError` / `EmbeddingRequestError`
  - Suggest retrying, reducing load (smaller `top_k`), or trying later (rate limits / transient failures).
