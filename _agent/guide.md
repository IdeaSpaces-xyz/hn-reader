---
name: Guide — HN Reader
summary: How a brief goes, and the taste profile that decides it — the file the owner edits
  to make this reader theirs. Starter profile included and deliberately not yours.
---

# HN Reader — Guide

> The fetch is a skill. The filter is this file. Edit the filter.

## The taste profile — edit this

This is the starter profile. It is opinionated so the filter visibly works on first run, and it is
not yours. Rewrite the lists; the same model becomes a different reader.

**Care about:**
- Local-first software, sync, storage and database internals
- Agents, LLM tooling, and how people actually run them
- Postmortems, outage writeups, "how X actually works" deep dives
- Small teams shipping unreasonable things

**Skip:**
- Token and coin prices, funding-round news with no technical content
- Political flamewars, culture-war threads
- "X is dead" takes, hot takes on companies with no new information

**Interrupt-worthy** (lead the brief with these, whatever else is happening):
- Movement on anything in [`watchlist.md`](../watchlist.md)
- A care-about topic past 500 points
- A Show HN squarely inside a care-about topic

## How a brief goes

Run [`skills/check-hn.md`](skills/check-hn.md). The brief has three parts, in order:

1. **Worth your time** — at most five stories. One line each on *why, by which rule* — "storage
   internals, 340 points" — with the HN link. Never a bare list of titles.
2. **Watching** — each watchlist entry that moved since its recorded numbers, and by how much.
3. **The bill** — what was skipped, as counts by rule: "skipped 24: 9 price talk, 7 flamewar,
   8 outside your interests." One line. This is what makes the filter inspectable.

Then offer to log reactions — [`skills/log-the-read.md`](skills/log-the-read.md).

## Tuning

The log is evidence about the profile. When it disagrees — repeated skips on a claimed interest,
repeated reads of something the profile ignores — say so, with the entries as proof, and propose
the specific edit to this file. Apply it only on an explicit yes, and commit it so the change has
a date. Taste under version control is the point of keeping it here.
