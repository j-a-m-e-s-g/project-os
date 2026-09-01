# MACHINERY — what the folder OS actually runs on

A folder is an OS for Claude. These are its system calls. Get them wrong and a perfectly-specced project quietly fails to steer anything.

The distinction that runs through this whole file:

> **`CLAUDE.md` and skills are context. Hooks and permissions are enforcement.** Context shapes what Claude decides to do. Enforcement decides what Claude is able to do. An instruction that must hold every single time belongs in the second category, not the first.

---

## Which mechanism for which need

| You need | Use | Loads |
|---|---|---|
| A rule every session must know | `CLAUDE.md` | Every session, full content |
| A rule that only applies to some files | `.claude/rules/` with `paths:` | When Claude touches a matching file |
| Reference material or a repeatable workflow | A skill | On demand, when invoked or judged relevant |
| Work isolated from the main conversation | A subagent | Its own context window |
| Something that must happen every time, without asking | A hook | Runs externally, zero context cost |
| Access to a system Claude cannot see | An MCP server | Tool names at start, schemas on demand |
| A hard block on a tool, command or path | `settings.json` → `permissions.deny` | Enforced by the client |
| The same setup across several repos | A plugin | — |

### The trigger ladder

Do not install any of it up front. Each has a recognisable moment.

| Trigger | Add |
|---|---|
| Claude gets a convention or command wrong **twice** | A line in `CLAUDE.md` |
| You type the same prompt to start a task | A user-invocable skill |
| You paste the same multi-step playbook a **third** time | A skill |
| You keep copying from a system Claude cannot see | An MCP server |
| A side task floods the conversation with output you will never reread | A subagent |
| You want something to happen **every time**, without asking | A hook |
| A second repo needs the same setup | A plugin |

---

## `CLAUDE.md` — the mechanics people get wrong

**Size.** Target **under 200 lines**. This is not tidiness: longer files consume more context *and measurably reduce adherence*. A 600-line `CLAUDE.md` is followed less reliably than a 150-line one. Files over 4 MiB are skipped entirely.

**Load order.** Files from the working directory **upward** load at launch, concatenated root-first, so the file closest to where Claude started is read last. Everything is additive — nothing overrides anything.

**Subdirectory files load on demand.** This is the mechanism behind the whole folder OS, and it is worth stating plainly:

> A `CLAUDE.md` in a subdirectory is **not** loaded at launch. It loads when Claude reads a file in that directory.

So the per-folder hat is real, and it has a caveat: **the hat goes on when Claude touches the folder, not when it starts thinking about the area.** Two consequences for how you write them:

- An area `CLAUDE.md` must not be the only place a project-wide rule appears. If it matters everywhere, it goes in the root file.
- The root `CLAUDE.md` should point at the areas explicitly, so a session that has not yet opened anything still knows where to go.

**Imports do not save context.** `@path/to/file` expands and loads **at launch**, alongside the file that references it. Imports are an organisation tool, not an economy one — the tokens are spent either way. Relative paths resolve against the importing file, four hops maximum, and a path in backticks is not imported. To actually reduce context, move the content to a skill or a path-scoped rule.

**HTML comments are free.** Block-level `<!-- ... -->` comments are stripped before the file enters context. Notes for human maintainers cost nothing. (They are still visible when the file is opened with the Read tool.)

**It is context, not configuration.** Claude reads `CLAUDE.md` and tries to follow it. There is no guarantee of compliance, especially for vague or conflicting instructions. Contradictions between files get resolved arbitrarily — so review for conflicts, and never rely on prose for something that must not happen.

### The smell test — what is in the wrong place

The dividing line: **`CLAUDE.md` is for facts Claude should hold all the time. Procedures belong in skills.**

Three things in a `CLAUDE.md` that are almost always misfiled:

| If it says… | It belongs in… |
|---|---|
| "Every time X, always do Y" | A **hook**. Deterministic behaviour should not depend on the model electing to comply |
| "Never do this" | A **hook** (exit 2) or `permissions.deny`. As prose it is a wish |
| A runbook, a checklist, a multi-step procedure | A **skill**. It is spending tokens every session to sit unused most of them |

A fourth, one level down: a rule in `.claude/rules/` **without** `paths:` frontmatter loads every session anyway — it is a `CLAUDE.md` in a different folder, and none of the saving you wanted.

**Compaction.** The root `CLAUDE.md` is re-read and re-injected after `/compact`. Nested files and path-scoped rules come back only when Claude next touches a matching file, and anything said only in conversation is gone. If an instruction has to survive a long session, it cannot live in the chat.

**Verifying it.** `/context` shows which memory files actually loaded this session — the first thing to check when an instruction is being ignored. `/doctor` proposes trims for an oversized checked-in `CLAUDE.md`, cutting what Claude could derive from the code and keeping the pitfalls and conventions.

---

## `.claude/rules/` — the precision instrument

For an instruction that only matters in part of the project. One topic per file, in `.claude/rules/`, with a `paths:` frontmatter block:

```markdown
---
paths:
  - "src/api/**/*.ts"
---

# API rules
- Every endpoint validates its input.
- Use the standard error envelope.
```

Rules **with** `paths:` load only when Claude works with a matching file. Rules **without** load every session at the same priority as `.claude/CLAUDE.md`.

This is the right home for anything that would otherwise bloat the root file with guidance relevant to a tenth of the work. Personal, cross-project rules go in `~/.claude/rules/`; a shared set can be symlinked into several projects.

---

## Enforcement — hooks

The distinction that matters, from Anthropic's own guidance:

> Put guardrails in hooks. An instruction like "never edit `.env`" in `CLAUDE.md` or a skill is a **request, not a guarantee**. A `PreToolUse` hook that blocks the edit is enforcement. If a rule must hold every time, make it a hook rather than a prompt instruction.

Put more sharply: **the model choosing to run a formatter is a different thing from the formatter running automatically.** One is a tendency, the other is a fact about the system.

Everything in this skill's restraint section is prose, which means it is a request. Where a project genuinely cannot tolerate stray files, install the hook.

### A working example — enforcing the restraint rule

`.claude/settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/guard-docs.sh"
          }
        ]
      }
    ]
  }
}
```

`.claude/hooks/guard-docs.sh` (make it executable):

```bash
#!/bin/bash
# Blocks new markdown outside the project's standing documents,
# and blocks any write into a context/ folder.
set -euo pipefail
INPUT=$(cat)
FILE=$(printf '%s' "$INPUT" | jq -r '.tool_input.file_path // empty')
[ -z "$FILE" ] && exit 0

deny() {
  jq -n --arg r "$1" '{hookSpecificOutput:{hookEventName:"PreToolUse",
    permissionDecision:"deny", permissionDecisionReason:$r}}'
  exit 0
}

case "$FILE" in
  */context/*) deny "context/ is the user's. Claude never writes there." ;;
esac

case "$FILE" in
  *.md)
    BASE=$(basename "$FILE")
    case "$BASE" in
      CLAUDE.md|north-star.md|project-overview.md|prd.md|workflow.md|README.md) ;;
      *) [ -e "$FILE" ] || deny "New markdown file '$BASE' is not one of this project's standing documents. Put the analysis in chat, or ask first." ;;
    esac
    ;;
esac
exit 0
```

Needs `jq` on the path (`brew install jq`). Tested against synthetic `PreToolUse` payloads: it denies a new options paper and any write into `context/`, and stays silent for the PRD, an existing document, a code file, and a call with no `file_path`.

**The contract:** exit `0` and the action proceeds through the normal permission flow. Exit `2` blocks it, with stderr as the message. Any other exit code is a non-blocking error. To block from a clean exit, emit the JSON above with `permissionDecision: "deny"`.

The hook receives the full tool call on stdin — `tool_name`, `tool_input`, `cwd`, `permission_mode` and more — so it can be as specific as you need.

**Adjust the allowlist per project.** A project with a `docs/` area should permit it. The point is that the list is a deliberate decision, not a habit.

### Other events worth knowing

`PostToolUse` (after a call succeeds — lint on edit, cannot block), `UserPromptSubmit` (can block), `SessionStart`, `SessionEnd`. Hook output that returns text lands in context; a hook that stays quiet costs nothing.

---

## `settings.json` permissions

The other enforcement layer, and simpler than a hook where a flat block will do:

```json
{
  "permissions": {
    "deny": ["Read(./.env)", "Bash(git push:*)", "Agent(Explore)"]
  }
}
```

Denials are applied by the client regardless of what Claude decides. `Agent` on its own disables subagents entirely.

---

## Skills

On-demand instructions. Descriptions load at session start so Claude can decide when to reach for one; the full content loads only when used — which is why a skill is the right home for reference material that would otherwise bloat `CLAUDE.md`.

- Project skills live in `.claude/skills/`, personal ones in `~/.claude/skills/`.
- Set `disable-model-invocation: true` on anything with side effects, so only a human can fire it. It also costs zero context until invoked.
- The `description` is the whole discovery mechanism. Vague descriptions mean the skill never loads, or the wrong one does.

**Area-local skills.** The folder OS says everything for an area lives in that area — but Claude Code only discovers skills in `.claude/skills/`. So an area-local skill is **invisible until symlinked**:

```bash
ln -s ../../marketing/skills/campaign-brief .claude/skills/campaign-brief
```

Source of truth in the area folder, discovery via the symlink. Same for `.claude/agents/`.

---

## MCP

For a system Claude cannot otherwise see — a database, a CRM, a browser. Tool names load at session start, full schemas on demand, so idle servers are cheap. Install one when you notice yourself copying data out of a browser tab for Claude to read.

An MCP server gives Claude the *tools*; a skill gives it the *judgement* about how to use them well. They pair.

---

## What actually earns installing on day one

For a new project, almost nothing:

- **A root `CLAUDE.md`.** Always.
- **`.gitignore`.** Before the first commit, covering data, secrets and caches.
- **`settings.json` permissions** — only if there is something genuinely dangerous to fence off, such as a push that deploys.

Everything else waits for its trigger. A brand-new project with four subagents, three hooks and a rules directory is not well-engineered; it is furnished with guesses about work nobody has done yet. Every one of those files is context or behaviour that has to be maintained, and each was written before anyone knew what the project needed.

**Add machinery in response to a problem you have actually had.** That is the same rule as the maturity ladder in [HIERARCHY.md](HIERARCHY.md#project-local-skills--the-maturity-ladder), and the same rule as the restraint section in [SKILL.md](SKILL.md#restraint--the-urge-to-write-a-file-is-a-signal). It keeps being the rule.

---

## Sources

- **[Extend Claude Code](https://code.claude.com/docs/en/features-overview)** — the mechanism comparison tables, the trigger ladder, and the context cost of each feature. Where the "a request, not a guarantee" line comes from.
- **[How Claude remembers your project](https://code.claude.com/docs/en/memory)** — `CLAUDE.md` load order and precedence, the 200-line guidance, `@path` imports, `.claude/rules/` and path scoping, and the comment-stripping behaviour.
- **[Hooks](https://code.claude.com/docs/en/hooks)** — every lifecycle event, the stdin payload, exit-code semantics, and the JSON decision format.
- **[Subagents](https://code.claude.com/docs/en/sub-agents)** — scope, precedence, and what loads in a subagent's context.
- **[Steering Claude Code](https://claude.com/blog/steering-claude-code-skills-hooks-rules-subagents-and-more)** — the selection matrix across load timing, compaction and context cost; the facts-versus-procedures line; and the misfiling anti-patterns in the smell test above.

**These are living documents.** Fields, defaults and behaviour change between Claude Code versions. Where a specific detail matters, check the doc rather than trusting this file — and if you find a difference, fix this file.
