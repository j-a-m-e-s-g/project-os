<!--
  Disciplines 4 (Computational), 5 (Procedural) and 6 (Frameworks). Lives in plan/.
  This is what a builder reads to build without asking. Judge it on that alone.
  Every must-have needs a "Done when" line that a stranger could check.
  Tag anything Claude assumed with [assumed]. When a decision changes, EDIT THIS FILE.
-->

# <Project> — PRD

**Purpose, in one sentence:** <!-- If this needs a comma-spliced list, the thinking isn't finished. -->

**User:** <!-- A person, and what they are doing when they reach for this. -->

**Non-goals:** <!-- Explicit. As load-bearing as the goals. -->

---

## Features

### Must have — v1

<!-- Fewer than about seven. User-visible outcomes, never implementation. -->

1. **<Feature>**
   *Done when:* <!-- Observable, specific, singular. Checkable by someone who wasn't in the conversation. -->
2. **<Feature>**
   *Done when:*

### Should have

<!-- Real, but not v1. -->

### Later

<!-- Named so it stops competing for attention. -->

## Invariants

<!-- What must ALWAYS be true. What must NEVER happen. -->

## Edge and failure states

| State | Behaviour |
|---|---|
| Empty — no data yet | <!-- Never a blank screen. --> |
| One item |  |
| Very many items |  |
| Bad input |  |
| A dependency is unreachable | <!-- Say so in plain language. Say when the data was last good. --> |

---

## Data

<!-- Discipline 5. Every piece: where it comes from, who owns it, how fresh it must be. -->

| Data | Source | Owner | Freshness |
|---|---|---|---|
|  |  |  |  |

**How data gets in:** <!-- Read-only from an existing source (preferred) / typed in / imported / pulled on a schedule. -->

## Entities

<!-- For each thing the system knows about: its fields, with types.
     text · number · date · one-of-a-list (list the options) · yes/no · link to another entity -->

**<Entity>**
- `field` — type, and the options if it's a fixed list

## Variables

<!-- The things someone will want to tune: thresholds, limits, labels, categories, ranges.
     Keep them in one place in the code. -->

## Who sees what

<!-- Anything private, anything that must not leak, anything that differs by user. -->

## What it looks like

<!-- Point at something real: a page, a tool, a screenshot, a sketch.
     One reference beats a thousand words. "I don't care" is a valid answer — write it down. -->

## Maintenance

<!-- Set and forget / someone updates it weekly / reads live and maintains itself / unclear (a risk — say so). -->

---

## Stack

<!-- Discipline 6. Named with reasons. A choice without its reason gets overturned by the next session. -->

| Layer | Choice | Why |
|---|---|---|
| Frontend |  |  |
| Styling |  |  |
| Backend / data |  |  |
| Hosting |  |  |
| Key libraries |  |  |

**The capability this turns on:** <!-- Mapping, charts, drag and drop, auth, real-time — the choice everything else bends around. -->

---

## Assumptions

<!-- Every [assumed] answer, listed, with what it would cost to be wrong.
     Only assumptions the build actually rests on. Not a general list of unknowns. -->
