---
name: Check HN
description: >
  The brief, end to end — find the cursor in the reading log, fetch the front page and
  everything new since, refresh the watchlist, filter by the taste profile in guide.md, and
  present three parts: worth your time, watching, the bill. Every number from this session's
  API responses. Use whenever the owner asks what's on HN.
---

# Check HN

> Fetch fresh, filter by the file, show the bill.

The API is Algolia's public Hacker News search — keyless, no auth, no signup. Endpoints and
response shapes below were verified live on 2026-08-26. Be cheap with it: a brief is a handful of
requests, never a polling loop.

## 1. Find the cursor

The last dated entry in [`reading-log.md`](../../reading-log.md) is the last brief's time. No
entries means first run: skip the "new since" fetch and brief the front page only.

## 2. Fetch

The raw Algolia response carries `_highlightResult`, full comment-id arrays, and raw HTML
`story_text` on every hit — none of it is used. Pipe through `jq` to keep only what the brief
needs; it cuts a ~30-story front page from ~40KB to ~6KB and skips the persisted-output-file
detour for a large tool result.

**Front page, as ranked right now:**

```
curl -s "https://hn.algolia.com/api/v1/search?tags=front_page&hitsPerPage=30" \
  | jq -c '.hits[] | {id: .objectID, title, url, points, comments: .num_comments, t: .created_at_i}'
```

**New since the cursor** (epoch seconds). Comparison operators must be URL-encoded — a literal
`>` in `numericFilters` returns an error, `%3E` works:

```
curl -s "https://hn.algolia.com/api/v1/search_by_date?tags=story&numericFilters=created_at_i%3E<CURSOR>,points%3E50&hitsPerPage=50" \
  | jq -c '.hits[] | {id: .objectID, title, url, points, comments: .num_comments, t: .created_at_i}'
```

For Show HN specifically, use `tags=show_hn` and drop the points filter — Show HN posts the owner
cares about are worth seeing before they have points.

**Each watchlist entry:**

```
curl -s "https://hn.algolia.com/api/v1/items/<ID>" \
  | jq -c '{id, title, url, points, top_level_replies: (.children | length)}'
```

Note: this endpoint's `children` is only top-level replies, not the full thread — it won't match
the `num_comments` figure from the search endpoints (e.g. 29 vs 98 on the same thread). Track
points for watchlist movement; treat `top_level_replies` as a rough signal, not the same number.

The thread link for a story is `https://news.ycombinator.com/item?id=<objectID (or id)>`.

## 3. Filter

Apply the taste profile in [`guide.md`](../guide.md) as written. Interrupt-worthy first. Tally
every skipped story under the rule that skipped it — the tally is part of the brief, not internal
bookkeeping.

## 4. Brief

Three parts, in the format the guide defines: **worth your time** (≤5, one line of why-by-which-
rule each, with links), **watching** (entries that moved, old numbers → new), **the bill** (skip
counts by rule, one line).

Every count and title comes from this session's responses. If a fetch failed, say which part of
the brief is missing — never fill the gap from memory.

## 5. Offer to log

Reactions go through [`log-the-read.md`](log-the-read.md). Don't log silently; the owner's verdict
is theirs to give.
