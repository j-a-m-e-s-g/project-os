# project-os

A Claude Code skill that turns a vague idea into a properly-thought-through project — and a folder Claude can actually work out of.

It exists because coding agents fail in two predictable ways, and neither is a modelling problem:

1. **You were not clear.** A model cannot be clearer about a project than the person who asked for it. Clarity of purpose is the highest-marginal-return input in the whole process — higher than the model, the stack, or the prompt.
2. **The folder drifted.** Not one bad build, but the quiet accumulation of the model's own thinking on disk: a summary here, an analysis there, a plan nobody asked for. Three weeks later the folder is mostly Claude talking to itself — and every new session reads it back as though it were your decisions.

`project-os` interviews you through nine disciplines, then installs the answers as a folder that steers every future session: a `CLAUDE.md` per area, a north star, a PRD with acceptance tests, and a workflow. And it holds a hard line on writing anything else.

---

## Install

```bash
git clone https://github.com/j-a-m-e-s-g/project-os.git ~/projects/project-os
ln -s ~/projects/project-os ~/.claude/skills/project-os
```

Claude Code discovers skills as directories containing a `SKILL.md`; the symlink keeps the source of truth in your projects folder and the discovery in `~/.claude/skills/`. Drop it in a repo's `.claude/skills/` instead if you want it project-local.

Verify with `/project-os` in any session.

---

## Use it

```
/project-os new a tool that tracks who has replied to my emails
/project-os area my consultancy
/project-os audit ~/projects/my-site
/project-os zoom marketing
/project-os debug the build keeps failing
```

| Mode | For | What happens |
|---|---|---|
| `new` | Building a thing | Think (1–5) → pick the stack → **vision-fidelity pass** → propose the tree → install it |
| `area` | Organising a part of life or a business, no software | Two questions, then the same bones without the PRD |
| `audit` | An existing folder that never got set up, or has drifted | Scores nine rows in chat, names what each gap costs you, fixes what you pick |
| `zoom` | An area folder that has outgrown itself | Treats that subfolder as a root and runs `new` inside it |
| `debug` | Something is broken | Where → what → the bespoke fix, one change at a time |

At the start of `new`, `area` and `zoom` it offers three depths: **Full** (you answer, it pushes back — ~15 minutes), **Fast** (it drafts, you react, and everything invented is tagged `[assumed]`), or **Resume** (reads the folder, finds the first gap, picks up there). There is no state file — the artefacts on disk *are* the state.

---

## The nine disciplines

Each produces exactly one artefact. Nothing else gets written.

| # | Discipline | The question it answers | Lands in |
|---|---|---|---|
| 1 | Thinking | What am I actually trying to make true? | `north-star.md` |
| 2 | Logical | What is the game? | `plan/project-overview.md` |
| 3 | Analytical | What are the objectives, and how do I play? | `plan/project-overview.md` |
| 4 | Computational | What are the key features, and what rules enforce them? | `plan/prd.md` |
| 5 | Procedural | What data, what variables, what detail? | `plan/prd.md` |
| 6 | Frameworks | What should this be built with? | `plan/prd.md` |
| 7 | Checkpoints | How do we not lose work? | `plan/workflow.md` |
| 8 | Debugging | What do we do when it breaks? | `plan/workflow.md` |
| 9 | Context | Who knows what, and where does it live? | every `CLAUDE.md` + empty `context/` |

1–5 are a sequence: you cannot pick features before you know how you win, and you cannot know that before you know what game you are playing. **Every project that goes wrong went wrong by starting at 4.** 6–9 are standing rules for the life of the project, not phases.

---

## What you end up with

```
project/
├── CLAUDE.md              # the brain: what this is, the rules, signposts to each area
├── north-star.md          # the change in the world, in your words. One screen.
├── context/               # EMPTY — yours, filled by hand
├── plan/
│   ├── CLAUDE.md
│   ├── project-overview.md   # the situation, the players, the objectives
│   ├── prd.md                # features with acceptance tests, data, stack
│   ├── workflow.md           # what Claude may do unasked, checkpoints, how to debug
│   ├── context/              # EMPTY
│   └── research/
└── <area>/                # one per area of work
    ├── CLAUDE.md          # what this is, the hat Claude wears here, where things are
    ├── context/           # EMPTY
    ├── research/
    └── <the actual work>
```

**A folder is an operating system for Claude.** Files from the working directory upward load at launch; a `CLAUDE.md` in a subdirectory loads on demand, when Claude reads a file in that directory. That is literally how the per-folder hat works. An area earns a folder when it has its own vocabulary, its own outputs, and Claude would wear a different hat in it — otherwise it is a file. Three to eight areas is the healthy range.

Two rules do most of the work: **`CLAUDE.md` points, it does not store** (stale context is worse than absent context, because it is believed), and **every `CLAUDE.md` stays under 200 lines** (past that, adherence measurably drops, so a long file is followed *less* reliably than a short one).

---

## The rule that makes it different

Before creating **or editing** any file:

1. Did the user ask for this file, in this turn?
2. Is it one of the standing artefacts above?
3. Is it recording something that actually happened, after it happened?

Three noes means it goes in chat. Not "in chat for now, file later" — in chat, and nowhere else.

The reframe underneath it: when a model wants to write something down mid-task — a summary, a comparison, a note for later — that is not a filing instinct, **it is the model arriving at a decision point**. Writing it to disk is how it takes the decision itself and then hides it somewhere you will mistake it for yours.

The asymmetry, stated plainly: **be generous with context in the conversation, and miserly about what reaches disk.** Those are not in tension. What you say is yours and is true; what the model writes down is its own reasoning wearing the same clothes.

All of that is prose, which makes it a *request*. Where a project genuinely cannot tolerate stray files, [MACHINERY.md](MACHINERY.md#enforcement--hooks) has a working `PreToolUse` hook that blocks the write outright. The skill offers it; it never installs it unasked.

---

## Repo map

`SKILL.md` is the only file loaded up front. Everything else is progressive disclosure — read when the run reaches the phase that needs it.

| File | What it holds | Loaded |
|---|---|---|
| [SKILL.md](SKILL.md) | Routing, the depth choice, the interaction contract, the hard rules, the install sequence | Always |
| [THINKING.md](THINKING.md) | Disciplines 1–5 in full — questions, pass criteria, pushbacks, worked examples | THINK phase |
| [DISCIPLINES.md](DISCIPLINES.md) | 6–9: choosing a stack, checkpointing, the debugging protocol, what context to supply | FRAME, and `debug` mode |
| [HIERARCHY.md](HIERARCHY.md) | The canonical trees, what earns a folder, `CLAUDE.md` craft, the cull sweep, the automation maturity ladder | ARCHITECT phase |
| [MACHINERY.md](MACHINERY.md) | `CLAUDE.md` mechanics, `.claude/rules/`, hooks, permissions, skills, MCP — and what actually earns installing on day one | INSTALL phase |
| [DELEGATION.md](DELEGATION.md) | Whether to use a subagent (default: no, and the token maths for why), and how to architect a system that is genuinely agentic | HANDOFF, or an agentic build |
| [AUDIT.md](AUDIT.md) | The nine-row score, the drift checks, the fix menu | `audit` mode |
| [templates/](templates/) | The six documents that get written, with `<!-- guidance -->` comments stripped on the way out | INSTALL phase |

---

## Conventions, if you are editing this

- **One idea, one home.** If a rule appears in two files, one of them should point at the other. The reference files are split so a run loads only what it needs; duplication defeats that.
- **Every rule carries its reason.** "No stray files" is a preference. "Unasked-for files get read back into every future session as though they were your decisions" is an argument, and arguments survive being paraphrased by a model.
- **Templates instruct, they do not fill.** Guidance lives in HTML comments and gets stripped before the file lands. Never ship placeholder prose — an unknown answer is tagged `[assumed]` with a real draft, or the section is deleted and the gap named out loud.
- **Kill by default.** When it is unclear whether a section should exist, it does not. Anything that stays earns it out loud. This governs *delegated scope decisions* — it never licenses deleting more than was asked when the instruction is narrow.
- Claude Code's own docs are living documents. Where a specific mechanism matters, check the doc rather than trusting the file here — and if you find a difference, fix the file. Sources are listed at the foot of [MACHINERY.md](MACHINERY.md#sources) and [DELEGATION.md](DELEGATION.md#sources).

---

## Related

This skill builds the structure a project runs on. Two neighbouring jobs it deliberately does not do: thinking through a strategy or a decision that has no folder at the end of it, and handing a live project from one session to the next. If you have skills for those, they compose — `project-os` makes the folder they work in.
