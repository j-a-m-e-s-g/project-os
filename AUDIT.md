# AUDIT — scoring and fixing an existing project

For a folder that already exists. Either it was never set up properly, or it has drifted, or a run of enthusiastic sessions has filled it with things nobody asked for.

**Nothing in this mode gets written to disk except fixes the user has approved.** The score is a chat table. It is not a document.

---

## Step 1 — Look

Before scoring anything:

```bash
find <root> -maxdepth 3 -not -path "*/node_modules*" -not -path "*/.git/*" | head -60
```

Then read, if they exist: the root `CLAUDE.md`, `north-star.md`, everything in `plan/`, and one area `CLAUDE.md` as a sample. Check `git log --oneline | head -20` for whether checkpointing is actually happening. List what is inside every `context/` folder — that one check catches more drift than any other.

Do not ask the user a single question until this is done.

---

## Step 2 — Score

Nine rows. `✓` solid · `~` there but thin · `○` absent. One line per row on **what is missing and what it costs at build time** — the cost clause is the whole point, because "you have no north star" means nothing and "there is no north star, so every session re-decides what this project is for" means something.

Render as a table in chat:

```
project-os audit · <folder>

  #  Discipline       Score  What it costs you
  1  Thinking           ○    No north star, so every session re-decides what this is for
  2  Logical            ~    Overview describes features, not the situation it changes
  3  Analytical         ✓    —
  …
```

### What each row is actually checking

| # | Discipline | Present when |
|---|---|---|
| 1 | **Thinking** | `north-star.md` exists, states a change in the world, and has at least one non-goal |
| 2 | **Logical** | Somewhere says what the situation is, who the players are, and what constraints are fixed |
| 3 | **Analytical** | Two or three objectives are written down, and something is explicitly deferred |
| 4 | **Computational** | Features exist, are prioritised, and the must-haves have acceptance tests |
| 5 | **Procedural** | Data sources, entities and fields are specified with owners |
| 6 | **Frameworks** | The stack is named somewhere, with reasons |
| 7 | **Checkpoints** | It is a git repo, commits are real and recent, `.gitignore` excludes data and secrets |
| 8 | **Debugging** | Someone could work out how to run it and how to tell when it is broken |
| 9 | **Context** | `CLAUDE.md` at root and per area, each pointing rather than storing, every `context/` empty |

---

## Step 3 — The drift checks

These are separate from the score and often matter more, because they are damage rather than absence. Report anything found, with the file path.

- **Polluted `context/`** — any file Claude put in a `context/` folder. Always a finding. Always.
- **Claude's thinking on disk** — the big one, and the reason this mode exists. Files the model wrote as a side effect of its own reasoning rather than because anyone asked. Two ways to find them:

  ```bash
  # documents nothing links to
  for f in $(find <root> -name "*.md" -not -path "*/node_modules/*" -not -path "*/.git/*"); do
    grep -rqF "$(basename $f)" --include="*.md" <root> --exclude="$(basename $f)" || echo "orphan: $f"
  done
  # the vocabulary of un-asked-for analysis
  grep -rilE "option (a|1)|approach (a|1)|pros and cons|we could (either|also)|recommendation:|summary of (my|the) (findings|analysis)|considerations|next steps" \
    --include="*.md" <root> | grep -v node_modules
  ```

  **A hit is a candidate, not a verdict — read it before you say anything.** A recorded decision is legitimate and stays: "the user chose option 1" is history, not a backlog. What you are looking for is a document that *argues* rather than decides, that nobody asked for. Report the path and one line on what it is; never delete on a grep hit alone.
- **A backlog by any name** — "ideas", "future", "next steps", "TODO", "considered", "parked", "open questions", "decisions needed". If it records something nobody actioned, it is skewing every subsequent piece of analysis in the project. This is true even inside an otherwise legitimate document: a brief with an "Open questions" section at the end is carrying a backlog.
- **Edit drift** — a document quietly reconciled to a conclusion the model reached, rather than to a decision someone made. `git log -p --follow <file>` on the standing documents; look for changes with no corresponding decision in the commit message or the conversation. Where the spec and the build disagree, one of them was changed unilaterally — find out which.
- **A storing `CLAUDE.md`** — one that has accumulated knowledge, history or explanation instead of pointing. Check whether what it says is still true; a stale `CLAUDE.md` is the most dangerous file in a project.
- **Contradiction** — two documents that disagree. Name both and ask which is true. Never guess.
- **Folders that fail the three-part test** in [HIERARCHY.md](HIERARCHY.md#what-earns-a-folder) — no distinct vocabulary, no distinct output, no different hat.
- **Loose files at the root** that belong in an area.
- **Data or secrets in git**, or absent from `.gitignore`.
- **Documents that have drifted from the build** — a spec describing something that was never built, or built differently. Worse than no spec, because it will be believed. Reconcile or delete.

---

## Step 3b — The machinery checks

Only where a `.claude/` exists, or where its absence is actually costing something. Absence is not a finding on its own — most projects need none of it.

- **An oversized `CLAUDE.md`.** `wc -l` every one. Past **200 lines** adherence measurably drops, so a long file is followed *less* reliably than a short one. Suggest `/doctor`, which proposes trims that cut what Claude could derive from the code and keep the pitfalls and conventions.
- **Misfiled instructions.** Run the smell test from [MACHINERY.md](MACHINERY.md#the-smell-test--what-is-in-the-wrong-place) over every `CLAUDE.md`: "every time X, always Y" and "never do this" want a hook; a runbook or checklist wants a skill. Flag the specific lines, not the concept.
- **A storing `CLAUDE.md` that is also long** — the two faults compound and this is the most common real problem.
- **Orphaned machinery.** Subagents in `.claude/agents/` nobody invokes, skills whose `description` is too vague to ever match, rules whose `paths:` match no files in the repo. Dead machinery is context cost with no return.
- **An area-local skill or agent with no symlink** into `.claude/`. It is invisible and will never load. Check both directions: broken symlinks too.
- **`.claude/` sprawl** — machinery installed before its trigger. A project with subagents but no north star has been furnished, not engineered.
- **Verify rather than assume.** `/context` in a session shows which memory files actually loaded. If an instruction is being ignored, that is the first thing to check — the file may simply not be loading.

---

## Step 4 — Offer the fixes

Present the fixes as a short numbered list in chat, worst first, each with what it takes. Then ask which they want — `AskUserQuestion`, with a `(Recommended)` bundle. Do not fix anything unasked, and do not fix everything because it is all technically wrong.

Three shapes of fix:

- **Fill a gap** — run that discipline's questions from [THINKING.md](THINKING.md) and write the artefact. Offer to run just that one discipline rather than the whole flow.
- **Restructure** — repropose the tree per [HIERARCHY.md](HIERARCHY.md), show it, get approval, then move things. Moving files is destructive; show the before and after.
- **Cull** — run [the cull sweep](HIERARCHY.md#the-cull-sweep). Deletions get listed before they happen.

If the project is a genuine mess, say so plainly in one sentence and recommend the order: **cull first, then north star, then structure, then the gaps.** Culling first is not cosmetic — every later step gets better answers once the misleading files are gone.

---

## Step 5 — Close

- Re-render the score table with the new state.
- Name anything still `○` that the user chose not to fix, in one line, without nagging.
- Propose a git checkpoint. Do not take it.
