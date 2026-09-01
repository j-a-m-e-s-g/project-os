<!--
  Template for the root CLAUDE.md. Read on every session in this project.
  It POINTS. It does not store. Nothing in here should be able to go stale without someone noticing.
  Delete any section that doesn't apply — an empty heading is worse than no heading.
-->

# <Project> — root

<!-- One line on what this is. Then: -->
Read `north-star.md` first. It is short and it is the point.

<!--
  ===== THE OVERRIDING RULE =====
  Only if there is one — the rule most likely to be broken, usually the one the user has had to say twice.
  Abstract rules get reasoned around. Attach three concrete consequences and they don't.
  Delete this whole block if the project doesn't have one. Do not invent one.
-->

## The rule that overrides everything

**<The rule, in bold, in one line.>**

<!-- Two sentences on why it keeps getting broken. Then the concrete consequences: -->

- <A specific decision that follows from it.>
- <Another. With a number in it if possible.>
- <A thing that is dead and must not come back. "It was considered and cut.">

## The areas

| Folder | What lives there |
|---|---|
| [<area>/](<area>/) | <one line> |
| [plan/](plan/) | The thinking. Start here to understand why anything is the way it is. |
| `.claude/` | Machinery — settings, hooks, agents. Delete this row if there is none. |

Each area has its own `CLAUDE.md`, a `context/` for the user's own knowledge, and a `research/`.

## Where to look first

<!-- The highest-value section in the file. The three or four questions a new session actually has,
     each pointing at the file that answers it. -->

- **Why is this the way it is?** → [plan/project-overview.md](plan/project-overview.md)
- **What are we building, exactly?** → [plan/prd.md](plan/prd.md)
- **How do we work, and what needs approval?** → [plan/workflow.md](plan/workflow.md)
- **<A question specific to this project>** → <file>

<!--
  ===== COPY THE NEXT SECTION VERBATIM =====
  Not a placeholder. It goes into every project unchanged, because it governs sessions
  that will never invoke /project-os. It is the single highest-value block in this file.
-->

## Nothing gets written unasked

Before creating **or editing** any file here, three tests:

1. Did I get asked for this file, in this turn?
2. Is it one of this project's standing documents — the north star, the overview, the PRD, the workflow, a `CLAUDE.md`?
3. Is it recording something that actually happened, after it happened?

**Three noes means it goes in chat, and nowhere else.** Summaries of my own reasoning, options papers, comparisons, backlogs, ranked next-steps, notes for later, plans nobody asked for — all chat, permanently. An un-actioned idea on disk is not neutral: it gets read back into every later session as a live option, and each session re-argues a menu nobody chose.

The urge to write something down mid-task is not a filing instinct. **It is a decision point.** Put the decision in one or two sentences and let a human make it.

Editing follows the same rule. A document changes when a *decision* changes — not to reconcile it with a conclusion I reached on my own. Never rewrite what the user wrote. No silent appends. Change the thing that changed, not the paragraphs around it.

The same restraint applies beyond files: no folders that were not agreed, no features because one seemed implied, no refactoring adjacent code, no abstractions "for later". **If I would have to explain it afterwards, I should have asked beforehand.**

## Hard rules

<!-- The things that must not happen. Irreversible or outward-facing. Keep it short and absolute. -->

- **Never commit or push unless asked in that turn.** Unapproved work stays in the working tree.
- <Deployment trigger, if a push puts something live. Confirm every time.>
- <Commit author identity, if it matters here.>
- **Never put files in a `context/` folder.** Those are the user's, filled by hand.
