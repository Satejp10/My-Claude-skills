---
name: cl-code-handoff
description: "Generate a Claude Code handoff document from the current Claude.ai chat thread, so a project that was discussed or started here can be continued in Claude Code (or any local coding agent) with full context. Use this skill whenever the user wants to hand off, port, transfer, or continue work in Claude Code: phrases like 'hand this off', 'make a handoff doc', 'context port', 'context transfer', 'continue this in CC', 'move this to Claude Code', 'pick this up in the terminal', or says they have hit the limits of building inside chat. Also use it proactively when a project being designed or built across the conversation is clearly ready to move to a real coding environment. The skill mines the thread for what is being built, WHY (motivation and constraints), the current stage and files, and where to pick up, then writes one portable Markdown file whose very first instruction makes Claude Code AUDIT the project before doing any work."
license: MIT
---

# Claude Code Handoff (`cl-code-handoff`)

Turn the current Claude.ai conversation into a single portable document that lets Claude Code continue the work as if it had been in the room the whole time.

This skill exists because building inside a chat thread and building in Claude Code are different surfaces. Here, you and the user explore, argue, and prototype. Claude Code has the filesystem, a terminal, git, and agentic loops. The handover between them is where context dies — the next agent starts from zero, re-derives decisions you already settled, and re-walks dead ends. A good handoff kills that loss.

There are **two audiences** for what this skill produces, and confusing them is the most common failure:

1. **You (Claude.ai), right now** — these SKILL.md instructions tell *you* how to mine the thread and write the document.
2. **Claude Code, later** — the *document you write* is an instruction set for a different agent reading it cold. Write every line for someone who has never seen this conversation.

## When to use / when not to use

**Use it** when a non-trivial project is moving from chat to a coding environment: a multi-file build, anything with a stack and a repo, anything where decisions and rationale have accumulated over the thread.

**Don't use it** for a single self-contained snippet ("write me a regex", "fix this function"). If the entire deliverable fits in one paste and carries no project context, just give the code. A handoff doc for a 30-line script is overhead, not help.

## Core principles

These three ideas shape every section. Internalize them before writing.

### 1. The handoff is a hypothesis, not a transcript
A chat thread is full of half-formed ideas, abandoned branches, and things that turned out to be wrong. Do **not** dump the conversation. Distill it into the *current best understanding*, and explicitly mark anything Claude Code must verify rather than trust. The document's first job is to make Claude Code check the doc against reality (see the audit block). Every claim about the code is a hypothesis until Claude Code confirms it against the actual files.

### 2. Always say WHY, not just WHAT
Telling an agent the reasoning behind an instruction makes it follow the instruction more reliably and lets it make sound calls when reality diverges from the plan. An instruction with no rationale is brittle: the moment the agent hits a wrinkle the plan didn't anticipate, it has nothing to reason from. So:
- Give motivation a top-level section (Section 2), AND
- attach a short "because…" to consequential instructions throughout. "Use Zustand, not Redux (because the state is tiny and we want zero boilerplate)" beats "Use Zustand."

### 3. Don't duplicate what the repo or CLAUDE.md already says
If the project has a `CLAUDE.md` / `AGENTS.md`, the handoff *supplements* it — it must not restate conventions, rules, or preferences already there. Reference other artifacts (PRDs, design files, issues, diffs) by path or URL instead of pasting them. Every sentence should be something the next session cannot get by reading the code and the existing config. Redundancy buries the signal.

## The process (what you do, in order)

### Step 1 — Mine the thread
Read the whole conversation (and if the project spans multiple chats, call `conversation_search` — or `recent_chats` when the anchor is temporal — to pull the earlier ones). Extract:
- what is being built and the one-sentence purpose,
- the *why*: motivation, constraints, what success looks like, what was explicitly ruled out,
- every decision made and the reason for it (alternatives that were rejected and why),
- the current stage and any files/stack the user has mentioned creating,
- dead ends: approaches tried and abandoned, with the reason,
- the user's working preferences as expressed in the thread (how they like to chunk work, what feedback style, libraries they prefer/avoid),
- anything still unresolved or being debated.

### Step 2 — Classify the project stage
This decides how the document is shaped. Pick one:

- **Discussion / design only — no code yet.** The build hasn't started. The handoff is mostly Sections 1, 2, and a *scaffolding* version of Section 4: intended stack, directory plan, and the concrete first build step. The file inventory says "None yet."
- **Scaffolded / partial build.** Files exist. Now the file inventory, what-works-vs-broken, line-level changes, verbatim errors, and dead ends all matter.

If you are unsure which, ask the user one short question rather than guessing — getting the stage wrong makes the whole document misleading.

### Step 3 — Capture the WHY deliberately
Before writing Section 2, make sure you can answer: *Why this project? Why now? Why these technical choices over the obvious alternatives? What must it NOT become?* If the thread didn't make a rationale explicit and you can't infer it confidently, leave a clearly marked `[NEEDS USER INPUT: why X over Y?]` rather than inventing a reason. A fabricated rationale is worse than an admitted gap.

### Step 4 — Draft from the template
Read `assets/handoff-template.md` and fill **every** section. Two hard rules:
- **Copy the Section 0 audit block verbatim** into the output. It is the load-bearing part — it is what forces Claude Code to verify before acting.
- Use specific, checkable detail. Examples of the bar:

  Bad file line: `auth.ts — handles authentication`
  Good file line: `src/auth/session.ts (new, ~90 lines) — issues + validates JWT in httpOnly cookie. validateSession() is the entry point. Refresh-token rotation is stubbed (throws NotImplemented).`

  Bad next step: `continue building the game`
  Good next step: `Implement height-climbing physics (Phase 2). A cube may roll onto an adjacent tile only if the height delta is 0 or +1. Acceptance: rolling into a +2 wall is rejected and the cube stays put; verify by loading levels/test-stairs.json and stepping through.`

### Step 5 — Fidelity self-check
Before delivering, run the Quality Bar below. The decisive question: *Could a competent agent that has never seen this thread continue the work using only this document plus the repo?* If not, name what's missing and fix it.

### Step 6 — Deliver
- **If you are in Claude.ai (this environment):** write the file to `/mnt/user-data/outputs/HANDOFF.md` (or `HANDOFF_<slug>.md`) and present it so the user can download it and drop it into their repo root. This works for every Claude Code surface (CLI, IDE, desktop app); for **Claude Code on the web**, remind the user the file must be *committed to the repo* first — that surface has no local filesystem.
- **If this skill is ever run inside Claude Code:** save `HANDOFF.md` to the project root.
- Then give the user the **ready-to-paste resume prompt** (it's the last block of the template) so starting the next session is one copy-paste.

## The mandatory audit block (Section 0 of every handoff)

Every document opens with an instruction block that makes Claude Code audit the project *before touching anything*. This is non-negotiable — it's the difference between an agent that builds on a verified foundation and one that confidently acts on a stale or wrong description. The full text lives in the template; in essence it tells Claude Code to: read the whole handoff and the existing `CLAUDE.md`; actually open every file listed (not trust the summaries); confirm the stack/versions are installed; run the build, tests, and `git status`/log to check the stated state is real; treat every claim as a hypothesis and **report any drift** (doc says X, reality is Y); and then stop and wait for the user's go-ahead — not silently start coding. The block also instructs Claude Code to run the audit **in plan mode** where available (the audit is read-only by definition), and contains a **greenfield shortcut**: when the handoff states no code exists yet, the file/build/git steps collapse into confirming the directory and toolchain match expectations.

## Output & safety rules

- **Redact secrets.** Never carry API keys, tokens, passwords, connection strings, or PII into the document. Reference secrets by *name* only (e.g. "needs `OPENAI_API_KEY` in `.env`"), never by value. If the thread contains a secret, scrub it.
- **Markdown only**, one file, repo-root friendly. No images, no attachments.
- **Date-stamp it** and, if it continues an earlier handoff, note the sequence number so a chain of handoffs stays ordered.
- **Keep it lean.** The document is consumed inside Claude Code's context window. Target the shortest doc that passes the Quality Bar — typically 1–2 pages. Past ~1,500 words you are almost certainly duplicating the repo or `CLAUDE.md`.
- **Plan its death.** A handoff is a bridge, not a permanent doc. The template's closing note tells the receiving session to fold durable facts into `CLAUDE.md` after the first successful working session and delete/archive `HANDOFF.md`. Keep that note in — a stale handoff misleading a later session is worse than no handoff.

## Quality bar (check before delivering)

- [ ] A fresh agent could continue using only this doc + the repo.
- [ ] Section 0 audit block is present, verbatim, at the top.
- [ ] The *why* is explicit — both the motivation section and "because…" on key instructions.
- [ ] Project stage is correct, and the doc is shaped to match it.
- [ ] File inventory is specific (paths, rough sizes, what each does, what's stubbed/broken) — or honestly says "no code yet."
- [ ] Non-goals / out-of-scope are stated (this is what stops scope creep on the other side).
- [ ] Dead ends and verbatim error messages are captured so they aren't repeated.
- [ ] The "pick up here" task is **one** concrete next action with acceptance criteria, not a vague direction.
- [ ] Nothing duplicates `CLAUDE.md`; other artifacts are referenced by path/URL.
- [ ] No secrets or PII anywhere in the document.
- [ ] Tooling needs are named (skills, MCP servers, CLIs) so the next session can self-provision.
- [ ] A copy-paste resume prompt is included at the end — the variant **matching the project stage** (existing-repo vs greenfield), with the unused variant deleted.

## Reference

`assets/handoff-template.md` — the full fill-in-the-blank handoff document, including the verbatim Section 0 audit block and inline guidance for each section. Read it and follow it exactly.
