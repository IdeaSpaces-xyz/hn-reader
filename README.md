---
name: HN Reader
summary: An agent that reads Hacker News for you — your taste in a file you edit, your reading
  log in a file that survives the session. Fork the folder, launch an agent inside it, ask
  "what's worth my time?"
---

# HN Reader

> Checking the front page five times a day is a tax. The fetching was never the hard part —
> the filtering is, and filtering is judgment. This folder holds the judgment.

This is an agent you fork. It reads Hacker News through the public, keyless HN API and answers one
question — *what here is worth my time?* — using a taste profile that lives in this folder, in a
file you edit. What you read, skipped, and are waiting on accumulates in a log beside it. Close the
conversation; the folder remains. Next session starts from it.

## Get it

### 1 — Install the plugin

In any Claude Code conversation:

```
/plugin marketplace add IdeaSpaces-xyz/claude-code-plugin
/plugin install ideaspaces@ideaspaces-xyz
```

### 2 — Take your copy of this folder

If you are reading this on a git host, clone it. However you got it, what matters is a folder of
these files on your machine that is yours.

### 3 — Launch inside it

```
cd hn-reader && claude
```

Then ask: **"What's on HN worth my time?"**

## Make it yours

The starter taste profile is deliberately opinionated so you can see the filter working — and
deliberately not yours. Open [`_agent/guide.md`](_agent/guide.md), rewrite the three lists (care
about, skip, interrupt-worthy), and the same model becomes a different reader. That edit is the
whole point of forking.

## How it works

One skill, one public API, three files. The [`check-hn`](_agent/skills/check-hn.md) skill fetches
the front page and everything new since your last brief — the last dated entry in
[`reading-log.md`](reading-log.md) is the cursor, so the folder itself remembers where you left
off. Your taste profile decides what surfaces. The brief tells you what it skipped and why, so the
filter is inspectable, not magic. When you react — read it, skip it, watch it — the
[`log-the-read`](_agent/skills/log-the-read.md) skill writes that back and commits.

Over time the log starts disagreeing with the profile — you keep skipping a topic you claimed to
care about. The agent proposes the taste edit; you say yes; the profile improves under version
control. Your reader gets better the way software does.

## What this is not

- **Not connected to your HN account.** There is no account. It never posts, votes, or logs in.
- **Not a recommender system.** No model of you is inferred anywhere. The filter is rules you
  wrote, in a file you can read, applied openly.
- **Not a mirror of HN.** The log keeps ids, titles, and your verdicts — the stories stay on
  Hacker News, where they live.

MIT-shaped and local-first: remove the plugin and this is still a folder of Markdown under git.
