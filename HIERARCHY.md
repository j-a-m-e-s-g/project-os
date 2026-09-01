# HIERARCHY — the folder OS

This is the part that makes the thinking survive. A well-specced project with a badly-shaped folder loses its specification within three sessions.

---

## The principle

**A folder is an operating system for Claude.** It tells the model what it is doing, what hat it is wearing, and what it is allowed to assume. Compartmentalise the work into folders and a session working in one area loads only that area's context — which is both cheaper and, far more importantly, *more accurate*, because it is not being pulled towards a decision that belongs somewhere else.

`CLAUDE.md` is the brain of each folder. It says what this place is and where things are.

**The mechanism, because it is worth knowing exactly:** files from the working directory *upward* load at launch. A `CLAUDE.md` in a *subdirectory* loads **on demand, when Claude reads a file in that directory**. That is literally how the per-folder hat works — and it means the hat goes on when Claude touches the folder, not when it starts thinking about the area. So a rule that matters everywhere belongs in the root file, and the root file must point at the areas explicitly. Full mechanics in [MACHINERY.md](MACHINERY.md#claudemd--the-mechanics-people-get-wrong).

**`CLAUDE.md` points. It does not store.** This is the single most violated rule in the whole system. The moment a `CLAUDE.md` starts holding knowledge — findings, history, a list of everything that happened — it goes stale, and stale context is worse than absent context, because it is believed.

---

## The canonical tree — a project

```
project/
├── CLAUDE.md              # the brain: what this is, the rules, signposts to each area
├── north-star.md          # short overview of the project in the user's own words
├── .claude/               # machinery, ONLY once earned — see MACHINERY.md
├── context/               # EMPTY — the user's own knowledge, added by hand
├── plan/                  # the thinking. Start here to understand why anything is the way it is
│   ├── CLAUDE.md
│   ├── project-overview.md   # disciplines 2 + 3: the game, the objectives
│   ├── prd.md                # disciplines 4 + 5 + 6: features, data, stack
│   ├── workflow.md           # disciplines 7 + 8: checkpoints, debugging, gates
│   ├── context/              # EMPTY
│   └── research/             # findings that informed the plan
└── <area>/                # one folder per area of work
    ├── CLAUDE.md          # what this is, the role Claude plays here, where things are
    ├── context/           # EMPTY
    ├── research/          # findings relevant to this area
    └── <the actual work>  # the repo, the scripts, the assets
```

## The canonical tree — a life or business area

```
root/
├── CLAUDE.md              # introduces the whole thing, points to each area
├── north-star.md          # short overview of the life or business, in their words
├── context/               # EMPTY
└── <area>/                # marketing, sales, finances, content, health, family…
    ├── CLAUDE.md
    ├── context/           # EMPTY
    └── research/
```

No `plan/`, no PRD. Same bones.

## The recursion rule

Any area folder can become a project root in its own right. When `marketing/` grows its own north star, its own plan and its own areas, it *is* a project OS — same shape, one level down:

```
marketing/
├── CLAUDE.md
├── context/
├── research/
├── skills/                # workflows that have earned automation
└── campaigns/
    ├── CLAUDE.md
    ├── context/
    └── research/
```

That is what `zoom` mode does. The shape repeats at every depth, which is why it holds.

---

## What earns a folder

An area earns a folder when **all three** are true:

1. It has its own vocabulary — the words used in there are not the words used elsewhere.
2. It has its own outputs — something comes out of it that does not come out of anywhere else.
3. Claude would wear a **different hat** in it. Working in `data/` you are a data engineer; working in `brand/` you are a designer. If the hat is the same, it is the same folder.

If it fails any of those, it is a **file**, not a folder.

**Suggest a subfolder only when it earns its place.** Two areas that overlap should be told about, not silently merged. An obvious missing area should be named. Do not build a folder because it looks tidy in a tree diagram.

Three to eight areas is the healthy range. Below three, the split is not doing any work. Above eight, the user cannot hold it in their head and will stop using it.

---

## `CLAUDE.md` craft

**Keep every `CLAUDE.md` under 200 lines.** Not a style preference: past that, adherence measurably drops, so a long file is followed *less* reliably than a short one. If it is growing, the overflow belongs in a path-scoped rule or a skill — not in an `@import`, which loads at launch and so saves nothing.

### The root one

In this order:

1. **One line** on what this project is, and a pointer to `north-star.md`.
2. **The rule that overrides everything** — see below. Only if there is one.
3. **The areas**, as a table: folder → what lives there. Links, not descriptions.
4. **Where to look first** — the three or four questions a new session most often has, each with a link to the file that answers it. This is the highest-value section in the file and the one most often left out.
5. **Hard rules** — the things that must not happen. Deployment triggers, commit rules, brands that must not be mixed, anything irreversible.

### The area one

1. **What this is**, in one line.
2. **`Your role here:`** — the hat, stated explicitly. "Frontend engineer." "Data analyst. Do not make design decisions here." This single line changes model behaviour more than any other in the file.
3. **Organisation** — what each subfolder holds. Two or three lines.
4. **How to run it**, if it runs. The exact commands, including the ports and the gotchas.
5. **Before saying done** — the checks. Typecheck, build, test, whatever the actual gate is.
6. **`context/` could later hold** — a short list of what the user might add. This is how you name context candidates *without creating them*.

### The overriding-rule block

If a project has one rule that is most likely to be broken — the one the user has had to say twice — it goes at the **very top of the root `CLAUDE.md`**, loudly, with concrete decisions underneath it. Not "keep things tight" but "the site is five sections; if you are proposing a seventh you have misunderstood the brief."

A rule stated abstractly gets reasoned around. A rule with three concrete consequences attached does not.

### What never goes in a `CLAUDE.md`

- Knowledge. That is `context/` (theirs) or `research/` (yours).
- History, changelogs, "what we tried". Git holds that.
- Anything that will be wrong in a month unless someone remembers to update it.
- Explanations of things the code already says.

Notes *for humans* are the exception: block `<!-- HTML comments -->` are stripped before the file enters context, so a maintainer note costs nothing.

---

## Context hygiene — the ledger

More context is better, right up until it isn't. The failure mode is not too little context; it is **the wrong context, believed**.

For any piece of information, exactly one of these is true:

| It is… | It goes… |
|---|---|
| A pointer, a rule, a role, a gate | `CLAUDE.md` |
| The user's own knowledge — their copy, their notes, their screenshots, their feedback | `context/` — **and only they put it there** |
| A finding: research, a reference, something you went and learned | `research/` |
| A decision that has been made and actioned | The doc that owns that subject, dated |
| A record of something that actually happened | An append-only log, post hoc |
| **Analysis, options, a menu, a ranking, a backlog, next-steps, an idea nobody actioned, the working behind a conclusion** | **Chat. Never a file.** |

That last row is the one that matters, and [the three tests in SKILL.md](SKILL.md#restraint--the-urge-to-write-a-file-is-a-signal) are how it gets enforced in the moment. An un-actioned idea written to disk is not neutral — it gets read back into every later session as though it were a live option, and each session re-argues a menu nobody chose. That is how a project's context gets poisoned. If you cannot say which row a paragraph belongs to, do not write the paragraph.

**Never write into a `context/` folder.** Not a starter file, not an example, not a README explaining what the folder is for. Empty means empty.

---

## The cull sweep

Run this at the end of every `project-os` session, and on demand when the user says some version of *"Claude has gone mental and made a load of folders."*

1. `ls -R` the project, excluding `node_modules`, `.git`, build output.
2. For every file and folder, ask one question: **does this serve the north star?**
3. Everything that does not, goes. Not "gets reviewed" — goes.
4. Specifically hunt for:
   - Folders nobody asked for, created "for later".
   - `.md` files that are analysis, options, or plans nobody actioned.
   - Anything inside a `context/` that Claude put there.
   - A `CLAUDE.md` that has started storing instead of pointing.
   - Duplicate documents where one is stale — the stale one is the dangerous one.
   - Empty folders with no purpose. An empty `context/` is deliberate; an empty `utils/` is debris.
5. Report what you removed in chat, in one line each. Do not write a deletion log.

**Kill by default.** When it is unclear whether something should survive, it should not. Anything that stays earns it out loud.

The exception, and it matters: this governs **scope decisions the user has delegated**. It does not license deleting more than was asked when the instruction is narrow. "Remove the link" means remove the link.

---

## Project-local skills — the maturity ladder

Things get more automated as they get more repeated. Do not skip rungs, and do not install a rung before its trigger has happened.

| Rung | Trigger | What |
|---|---|---|
| Say it in chat | Once | Nothing to write down |
| A line in the area's `CLAUDE.md` | Claude got it wrong **twice** | One sentence of standing instruction |
| A `.claude/rules/` file with `paths:` | The rule only applies to some files | A scoped rule that loads only when relevant |
| A section in `plan/workflow.md` | It has steps and gates | The procedure, written out |
| A skill | The same playbook pasted a **third** time | A real skill, loaded on demand |
| A subagent | A side task floods the conversation, or the same worker keeps being spawned | See [DELEGATION.md](DELEGATION.md) — the bar is higher than it looks |
| A hook | It must happen **every time**, without asking | Enforcement, not a request. See [MACHINERY.md](MACHINERY.md#enforcement--hooks) |

Every rung is earned by repetition, never anticipated by design. **Do not scaffold any of this into a new project.** A fresh folder with three subagents and a rules directory is not well-engineered; it is furnished with guesses about work nobody has done yet.

### Where a project-local skill or agent has to live

The folder OS says everything for an area lives in that area. Claude Code says skills are discovered in `.claude/skills/` and subagents in `.claude/agents/`. Both are right, and the join is a symlink:

```bash
ln -s ../../marketing/skills/campaign-brief .claude/skills/campaign-brief
```

Source of truth in the area folder where it belongs; discovery through the symlink. **An area-local skill with no symlink is invisible** — it will never load, and nobody will notice for weeks.
