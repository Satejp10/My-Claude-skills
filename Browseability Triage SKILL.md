---
name: browseability-triage
description: Triage URLs before fetching so Claude doesn't waste tool calls on sites that block, paywall, login-gate, or degrade automated retrieval. Use whenever a task involves web_search, web_fetch, opening a URL the user shared, competitive research, summarizing an article, looking up a Reddit / X / LinkedIn / Instagram / Facebook / TikTok / Quora post, a paywalled news article (NYT, WSJ, FT, Bloomberg, Atlantic, Wired, etc.), a retail page, a forum thread, or any "go look this up" request. Also use when the user pastes multiple URLs, asks for a competitive scan, or asks Claude to read a Substack / Medium / Patreon / Discord / Slack / Notion / Figma / Google Drive link. Skip restricted URLs proactively, don't retry on failure, and add a short closing footer naming what was skipped and the official reason. Do NOT use for non-web tasks, for user-pasted text content, or when the user has said "try anyway" / "just attempt the fetch".
---

# Browseability Triage

A pre-fetch guard that stops Claude from burning tool calls on URLs that were never going to work — Reddit, X, LinkedIn, paywalled news, login-gated apps, anti-bot retail, etc.

## When to consult the reference file

Load `restricted_domains.md` from this skill folder **once per task** when any of the following is true:

- A user message contains one or more URLs
- The task implies fetching content from the web (research, summary, lookup, monitor, "what does this article say")
- A `web_search` has just returned results and you're about to `web_fetch` one of them

Don't load it for tasks with no web component (pure coding, math, writing assistance, etc.).

## Procedure

### Before any `web_fetch`

1. Extract the host from the URL (strip `www.`, lowercase, drop the path).
2. Match against `restricted_domains.md`. Match on the bare host *and* on the parent domain (so `docs.google.com` matches `google.com` if listed).
3. Decide:

| Match | Action |
|---|---|
| In **High-risk** list | Skip. Don't fetch. Add to skip-log. |
| In **Medium-risk** list | Try **once**. If it fails or returns partial content, stop and add to skip-log. |
| In **Known-good** list | Fetch normally. |
| Not listed | Try **once**. If it fails, stop and add to skip-log. |

### When `web_search` returns results

- Don't filter the result list silently — the snippets themselves are useful even for high-risk domains (often the search snippet *is* the answer).
- But **don't `web_fetch` the high-risk results**. Synthesize from the snippet if possible. If the snippet isn't enough, add the URL to the skip-log and tell the user what's missing.

### Skip-log and footer

Track everything you skip during the task. At the end of the response, add a single short footer block:

```
*Skipped: reddit.com (restricted by robots.txt / ToS), wsj.com (paywall). Paste the content if you want me to work with it.*
```

Rules for the footer:
- One line, italicised, at the very bottom of the response.
- Group skipped domains by reason. Two-to-four-word reason per group.
- Never apologise. Don't say "I couldn't" or "I'm unable" — these sites have made an explicit choice; Claude is honoring it.
- If you skipped nothing, omit the footer entirely.
- If you skipped exactly one site and the user clearly already knew it would be blocked (they said "even though Reddit will probably block this…"), omit the footer.

### Overrides

Skip this skill's restrictions when the user says any of:
- "try anyway" / "just try it" / "force fetch"
- "I know it'll probably fail, but…"
- "ignore the blocklist"

In override mode, still attempt the fetch once, but report the actual result honestly (don't pretend it worked if it didn't).

## What this skill is NOT

- Not a censorship layer. Restricted sites are restricted *by the site*, not by Claude policy. The footer should make that clear.
- Not a hard blocklist. The reference file is probabilistic — domains can change behavior.
- Not for user-pasted content. If the user pastes the text of a Reddit thread or a WSJ article, work with it normally. The skill governs *fetching*, not analysis.
- Not for evaluating link quality, credibility, or bias. Only fetch-reliability.

## Maintenance

The domain list lives in `restricted_domains.md` and tracks the [Claude-Browseability-Dataset](https://github.com/Satejp10/Claude-Browseability-Dataset). When updating: keep the file compact (the goal is fast scanning by Claude, not a complete taxonomy — the full dataset is in the linked repo).
