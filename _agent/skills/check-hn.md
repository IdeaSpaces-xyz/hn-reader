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

**Front page, as ranked right now:**

```
curl -s "https://hn.algolia.com/api/v1/search?tags=front_page&hitsPerPage=30"
```

**New since the cursor** (epoch seconds). Comparison operators must be URL-encoded — a literal
`>` in `numericFilters` returns an error, `%3E` works:

```
curl -s "https://hn.algolia.com/api/v1/search_by_date?tags=story&numericFilters=created_at_i%3E<CURSOR>,points%3E50&hitsPerPage=50"
```

For Show HN specifically, use `tags=show_hn` and drop the points filter — Show HN posts the owner
cares about are worth seeing before they have points.

**Each watchlist entry:**

```
curl -s "https://hn.algolia.com/api/v1/items/<ID>"
```

Useful fields: `title`, `url`, `points`, `num_comments`, `objectID`, `created_at`. The thread link
for a story is `https://news.ycombinator.com/item?id=<objectID>`.

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
