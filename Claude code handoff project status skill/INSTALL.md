# Install: code-handoff + project-status

Two skills, one loop.

| | Runs in | Produces |
|---|---|---|
| `code-handoff` | Claude.ai chat | `HANDOFF.md` |
| `project-status` | Claude Code | `SR-<slug>-NNN.md` status report |

Only `project-status` is typed as a slash command. `code-handoff` fires in chat when you ask for a handoff. Skill names cannot contain "claude" or "anthropic", which is why neither name mentions its surface.

## Which install path covers what

| Install location | Chat | Claude Code web / cloud / Cowork | Claude Code CLI (local) |
|---|---|---|---|
| claude.ai account (Customize > Skills) | yes | yes | **no** |
| `~/.claude/skills/` | no | **no** | yes |
| repo `.claude/skills/` (committed) | no | yes | yes |

Account upload is the one that reaches cloud sessions, which is why `/code-handoff` appeared in Claude Code web after you saved it here. It does not reach your local terminal.

## 1. Remove the old skill

`cl-code-handoff` will shadow or duplicate the new one.

- Claude.ai: **Customize > Skills**, delete it.
- Local: `rm -rf ~/.claude/skills/cl-code-handoff`
- Any repo that has it committed under `.claude/skills/`.

## 2. code-handoff

Click **Save skill** on the file card, or **Customize > Skills > + > Create skill** and upload `code-handoff.skill`. Toggle it on. Nothing else needed; it only ever runs in chat.

## 3. project-status

**For chat, Claude Code web, and Cowork:** same as above, upload `project-status.skill` to your account.

**For your local terminal, additionally:**
```bash
unzip project-status.skill -d ~/.claude/skills/
ls ~/.claude/skills/project-status/SKILL.md
```
A `.skill` file is a zip with the folder at root, so the same artifact serves both paths. Claude Code picks up new skill directories mid-session; if `~/.claude/skills/` did not exist before, restart it. `/project-status` does not collide with the built-in `/status`.

**Per repo (optional):** commit a copy to pin a version to a project, or to reach cloud sessions without account sync.
```bash
mkdir -p .claude/skills && cp -r ~/.claude/skills/project-status .claude/skills/
git check-ignore -q .claude && echo "FIX GITIGNORE FIRST" || echo ok
git add .claude/skills/project-status && git commit -m "chore: add project-status skill"
```

If the gitignore check fails, replace the blanket `.claude/` rule with:
```
.claude/*
!.claude/context/
!.claude/skills/
```
Ignoring the contents rather than the directory is what makes the negations work; git cannot re-include anything under an excluded directory. This matters even if you never commit the skill, because `.claude/context/` has to reach cloud sessions.

## 4. Two consequences of the account upload

The upload schema rejects `disable-model-invocation` and `argument-hint`, so both protections now live in the skill body instead:

- **Claude can invoke `project-status` on its own.** The body opens with an instruction to stop and ask if it loaded without an explicit request. Softer than a frontmatter block, so if you ever see it fire uninvited, tell me and I will tighten the description.
- **It also appears in plain chat**, where there is no repo. The body checks for git and stops with an explanation rather than inventing a report.

## 5. First run on an existing project

`/project-status` in a repo with no `.claude/context/LOG.md` bootstraps itself: reconstructs history from git, HANDOFF.md, CLAUDE.md, and the code, tags everything reconstructed as `[inferred]`, seeds the log, and asks you for the reasons only you know. Run it once per active project for a baseline.

## 6. Daily use

- Chat to Claude Code: ask for a handoff. You get `HANDOFF.md`, a resume prompt, and setup steps split into what you do versus what the agent does. Commit it before starting a cloud session, since those clone from GitHub.
- End of a Claude Code session: `/project-status`, optionally with a note (`/project-status finished the auth refactor`). It writes the report, appends to the log, commits, and prints the TLDR.
- Back in chat: upload the report. Its first line tells the assistant to update memory from Section 6 and answer your open questions.

## Design notes

- **The log is the source of truth, the report is a view.** `.claude/context/LOG.md` is append-only, one entry per session. Reports derive from it plus live git output, so dates and iteration counts cannot be misremembered.
- **`project-status` injects real git output** into its own prompt before the model reads it: project start is the first commit, iterations are commits and active days.
- **Every state claim in a report is tagged** verified, logged, inferred, or unverified. Untagged claims are a defect.
- **Reports are standalone.** Section 2 always carries full state, so a report you never delivered costs you nothing.

## Constraints if you edit these

- Names: lowercase, hyphens, max 64 chars, no "claude" or "anthropic".
- Description: max 1024 characters, or packaging fails.
- `disable-model-invocation`, `argument-hint`, `context`, `paths`, and the other Claude Code-only frontmatter fields are rejected by the upload schema. Keep behaviour in the body if the skill needs to be uploadable.
- Exactly one `SKILL.md` per skill folder. Nested ones load only on the Claude Code filesystem, never on upload.
