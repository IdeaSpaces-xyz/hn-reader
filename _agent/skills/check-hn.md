---
name: Check HN
description: >
  The brief, end to end — find the cursor in the reading log, fetch the front page and
  everything new since, refresh the watchlist, filter by the taste profile in the Agreement, and
  present four parts: worth your time, watching, the bill, cut for space. Every number from this
  session's API responses. Use whenever the owner asks what's on HN, or when a scheduled run
  calls for a brief with no person present.
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

Apply the taste profile in [the Agreement](../agreement.md) as written. Interrupt-worthy first. Tally
every skipped story under the rule that skipped it — the tally is part of the brief, not internal
bookkeeping.

## 4. Brief

Four parts, in this order, interrupt-worthy stories first whatever else is happening:

1. **Worth your time** — at most five stories, one line each on *why, by which rule* ("storage
   internals, 340 points"), with the HN link. Never a bare list of titles.
2. **Watching** — each watchlist entry that moved since its recorded numbers: old → new.
3. **The bill** — what was skipped, as counts by rule, one line: "skipped 24: 9 price talk,
   7 flamewar, 8 outside your interests."
4. **Cut for space, not taste** — care-about stories that lost on points to the top five, as a
   one-line list with their numbers.

Every count and title comes from this session's responses. If a fetch failed, say which part of
the brief is missing and why — never fill the gap from memory.

## 5. Hand over the brief

With the owner present, give it to them and offer to log reactions through
[`log-the-read.md`](log-the-read.md). Don't log silently; the verdict is theirs to give.

With no one present (a scheduled run), write the brief where the run said to put it, or print it as
the run's output if none was named. Then advance the cursor (step 4 of
[`log-the-read.md`](log-the-read.md)) so the next run doesn't repeat this one. Never invent
verdicts, edit the taste profile, or drop a watch.
