<!--
  cl-code-handoff TEMPLATE
  Fill every section. Delete the HTML-comment guidance (like this) before delivering.
  Keep the Section 0 audit block VERBATIM — it is the load-bearing part.
  Replace [bracketed placeholders]. If something is genuinely unknown, write
  [NEEDS USER INPUT: question] rather than guessing. Never invent a rationale.
-->

# Handoff: [Project name]

> Generated [YYYY-MM-DD] from a Claude.ai thread. Handoff seq: [N — omit if this is the first].
> **This document is a hypothesis, not ground truth.** Treat every claim about the code as something to verify against the actual files, not a fact to trust. Run the Section 0 audit before doing any work.

---

## 0. Read me first — MANDATORY AUDIT (Claude Code: do this before anything else)

Before you write, edit, plan, or scaffold a single line, perform this audit and report what you find. **If your environment has a plan mode, run the entire audit in plan mode** — the audit is read-only by design; nothing may be modified before the report in step 8 is approved. Do not skip it. Everything below this section was written by an agent that could not see the code — your first job is to confirm reality matches it.

1. **Read this entire handoff**, top to bottom.
2. **Read `CLAUDE.md` / `AGENTS.md`** if they exist. This handoff supplements them; where they conflict, the existing config wins. Do not restate their contents.

   > **Greenfield shortcut:** if Section 3 says *no code exists yet*, steps 3–6 reduce to: confirm the working directory matches the described state (usually empty), and confirm the intended toolchain is available (runtime versions, package manager). Then go to step 7.

3. **Open every file listed in Section 3.** Actually read them — do not rely on the descriptions here. The descriptions may be stale or wrong.
4. **Verify the stack.** Check that the dependencies and versions in Section 3 are actually installed (inspect `package.json` / `requirements.txt` / lockfiles / etc.). Note any mismatch.
5. **Confirm the stated state is real.** Run the build and/or dev command from Section 3 and run the test suite and linter if they exist. Record pass/fail. If there is no test command, say so.
6. **Check git.** Run `git status`, current branch, and recent `git log`. Confirm they match what Section 3 claims.
7. **Treat every claim above as a hypothesis.** Flag every drift you find in the form: *"Doc says X; reality is Y."*
8. **Report, then stop.** Post a short audit summary — what matched, what drifted, what's missing, and any blockers — and then **wait for the user's go-ahead.** Do not silently start building. (Only proceed straight into the Section 4 task if the resume prompt explicitly told you to.)

---

## 1. What we're building

- **One sentence:** [what it is, in a single line]
- **Description:** [2–4 sentences: what it does, who it's for]
- **Definition of done / what success looks like:** [the state at which this project is "working"]

---

## 2. Why — motivation, reasoning, and constraints

<!-- This section is here because rationale makes the next agent follow the plan
     more reliably and reason well when reality diverges. Be concrete about WHY. -->

- **Why this project / why now:** [the motivation — the actual reason this is being built]
- **Why the key choices:** [why this stack / approach over the obvious alternatives]
- **Hard constraints:** [platform, target OS/runtime, performance, deadline, must-haves]
- **Non-goals — explicitly out of scope:** [what this must NOT become or include. This is the guardrail against scope creep on the other side. List the temptations to resist.]

### Decision log
<!-- One row per consequential decision. The "rejected" column is what stops re-litigation. -->

| Decision | Reason | Alternatives rejected (and why) |
|---|---|---|
| [e.g. Isometric 2D canvas, not 3D] | [perf + faithful to original look] | [WebGL/three.js — overkill for the art style] |
| [ ] | [ ] | [ ] |

---

## 3. Current state — where the project is

- **Stage:** [Discussion/design only — no code yet | Scaffolded | Partial build | Feature-complete-ish]
- **Stack:** [languages, frameworks, libraries + versions, package manager]
- **Target environment:** [OS/runtime the agent will run in — e.g. Windows + Git Bash, WSL, macOS, Linux. A local agent needs this; commands and paths differ.]

### How to get it running
<!-- Exact commands from clone/cwd to a running state. If nothing runs yet, say so. -->
```
[e.g. npm install   →   npm run dev   →   open http://localhost:5173]
```
- **Env vars / secrets required (names only — never values):** [e.g. `OPENAI_API_KEY` in `.env`]

### File inventory
<!-- For each file: path — (new/modified) approx size — what it does — entry points — what is stubbed/broken.
     If the build has NOT started, replace this whole list with: "No code yet. Intended structure below." -->

- `[path]` — [(new/modified), ~N lines] — [purpose; key function/entry point; what's stubbed or broken]
- `[path]` — [...]

<!-- If no code yet, describe the intended directory layout instead: -->
**Intended structure (not yet created):**
```
[project/
  src/
  ...]
```

### Status
- **Works (verified):** [what's confirmed functioning]
- **In progress:** [what's partially done]
- **Broken / not working:** [what's failing]

### Known errors, gotchas, and dead ends
<!-- Verbatim error text beats paraphrase. Dead ends save the most time. -->
- **Verbatim errors:** [paste the actual error message + where it occurs, e.g. `TypeError: ... at renderer.js:42`]
- **Gotchas:** [non-obvious things learned, e.g. "library X needs input pre-normalized to Y"]
- **Dead ends (do not retry):** [Tried [approach], it failed because [reason]. Don't repeat it.]

---

## 4. Pick up here — next steps

- **The immediate next task (do this one thing first):** [a single concrete action]
- **Acceptance criteria for it:** [how to know it's done — expected outputs, the exact command/level/input to test with. "Expect 200 with {status:'ok'}", not "verify it works".]
- **Then, in order:** [ordered next steps or phases after that]
- **Open questions / decisions still pending:** [things the user has not decided yet — flag, don't assume]

---

## 5. Working agreements & preferences

<!-- Only what is NOT already in CLAUDE.md. Otherwise reference it and move on. -->

- **Code conventions / libraries to prefer or avoid:** [e.g. functional components only; no new deps without asking]
- **How the user wants the agent to work:** [e.g. chunk work into small steps; show a plan before large changes; direct, no-filler feedback; ask before refactors]
- **Tooling the next session needs:** [skills to install or invoke — note scope: project `.claude/skills/` vs personal `~/.claude/skills/`; MCP servers the project depends on (`.mcp.json` entry or `claude mcp add …`); required CLIs. Credential *names* only, never values.]

---

## 6. References (don't duplicate — link out)

- [PRD / design doc / issue / commit / external doc — by path or URL]
- [ ]

---

## Resume prompt — paste this into a fresh Claude Code session

<!-- Keep ONLY the variant matching the project stage. Delete the other. -->

**If code exists:**
```
Read HANDOFF.md in this repo in full. Start in plan mode if available. Run the
Section 0 audit (open every listed file, verify the stack, run the build/tests,
check git) and report any drift between the doc and reality as "doc says X;
reality is Y". Treat the document as context to verify, not fact. Then [explore
the codebase and confirm your understanding | execute the Section 4 immediate next
task] — and wait for my go-ahead before making changes.
```

**If no code exists yet (greenfield):**
```
Read HANDOFF.md in this directory in full. Start in plan mode if available. No code
exists yet — run the Section 0 greenfield check (confirm directory state + toolchain),
then propose a scaffold plan matching the Section 3 intended structure and the
Section 4 first task. Wait for my go-ahead before creating anything.
```

---

**After the handoff lands:** once the audit passes and the first working session is done, fold durable facts (run commands, gotchas, conventions) into `CLAUDE.md` and delete or archive this file. A stale handoff misleads future sessions — it is worse than none.
