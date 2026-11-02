---
name: Log the read
description: >
  The write-back — record the owner's verdicts in the reading log, settle watchlist entries,
  commit by path, and when the log contradicts the taste profile, propose the edit with
  evidence. Use after a brief when the owner reacts, or when they say to watch or drop a
  thread.
---

# Log the Read

> Read on the way in, written on the way out.

## 1. Record verdicts

Append to [`reading-log.md`](../../reading-log.md), one line per story the owner reacted to, under
a dated heading:

```markdown
## 2026-08-26
- 49433292 · Apple introduces M6 and M5 Ultra — read; the memory-bandwidth thread was the value
- 49449648 · Show HN: How much of Hacker News is AI? — skipped; method too thin to trust
```

Id, title, verdict, and — when the owner said one — the reason. The reason is what makes the log
useful for tuning later. The dated heading is the next brief's cursor, so date it honestly.

## 2. Settle the watchlist

New watches get a line in [`watchlist.md`](../../watchlist.md) with today's numbers, so movement
is measurable later:

```markdown
- 49449679 · Show HN: Shelf Protocol — waiting: does it get traction? · added 2026-08-26 at 12 points, 4 comments
```

A watch the owner is done with moves to the log with its outcome and comes off the list.

## 3. Commit

By explicit path, so nothing else in the tree rides along:

```
git add reading-log.md watchlist.md && git commit -m "Log the read — <date>" -- reading-log.md watchlist.md
```

## 4. Unattended runs: advance the cursor only

When a run had no person to react, record no verdicts and no watches. Append one dated heading to
[`reading-log.md`](../../reading-log.md) with the single line `briefed, no verdicts yet`, so the
next brief's cursor moves past what was already shown. Commit it by path as in step 3.

## 5. Propose tuning when the evidence says so

Before finishing, check the pattern: three or more verdicts that contradict the taste profile —
skips on a claimed interest, reads the profile would have hidden — is worth raising. Cite the
entries and propose the specific edit to the taste profile in [the Agreement](../agreement.md).
Apply it only on an explicit yes, committed separately so the taste change has its own date. An
unattended run only reports the pattern in its output; it never edits the profile.
