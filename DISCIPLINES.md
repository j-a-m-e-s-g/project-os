# DISCIPLINES — 6 to 9

Not phases. Standing rules that apply for the whole life of the project. Six gets decided once and written into the PRD; seven, eight and nine are how the project is actually run, day to day.

---

## 6 — Frameworks

> LLMs are trained off pre-existing data. Direct the model to the correct framework that reflects what you want to build.

### Why this matters more than it looks

A model with no framework named will pick whatever was most common in its training data. That is not a neutral default — it is a vote by popularity, and it is frequently wrong for the project in front of you. Naming the stack is not micromanagement; it is **signposting**. It costs one line and it removes an entire category of wrong output.

The corollary: you do not need to be an expert to signpost well. You need to know enough to point.

### How to run this with the user

1. **Check the house stack first.** If the organisation has already settled this — a house-stack or house-style skill, a root `CLAUDE.md`, a sibling repo doing the same job — read it before offering anything. A new build should pick the same tools as the rest of the codebase unless there is a reason not to. Do not re-litigate a decision the organisation has already made.
2. **Offer a real menu, with a recommendation.** Three options maximum, each with one line on what it is good at and one on what it costs. Mark one `(Recommended)` and say why. Never present a shrug.
3. **Explain the shape, once, if they are not technical.** Two sentences, no more:
   > The **frontend** is what someone looks at in a browser. The **backend** is the part that stores things and does work out of sight. They talk over an API — the frontend asks, the backend answers. Small tools often need no backend at all.
4. **Ask what they want to learn.** Some users want the fastest path; some are building and learning at the same time and would rather use the thing they are trying to understand. That is a legitimate input into the choice — ask for it.
5. **Write the choice into the PRD** with the reason attached. A stack decision without its reason gets overturned by the next session that has a different favourite.

### Two stacks, not one

There is the stack the *product* is built with, and there is the machinery *Claude* is steered by. Both are framework choices and both need signposting; the second gets forgotten because it is invisible.

- **The product stack** — frontend, styling, data, hosting, the specialist libraries. Below.
- **The Claude Code machinery** — what goes in `CLAUDE.md` versus a path-scoped rule versus a skill; what must be a hook because it has to hold every time; whether any of it is earned yet. [MACHINERY.md](MACHINERY.md).
- **And whether to delegate at all** — subagents, and the case where the product itself is an agent. [DELEGATION.md](DELEGATION.md). The default is no; the numbers are there.

### The kinds of choice worth naming

Frontend framework · styling approach · backend and database, or explicitly none · hosting and how it deploys · the one or two specialist libraries the thing actually turns on (mapping, charts, drag and drop, auth, payments) · language and package manager for anything data-shaped.

If a project turns on one specialist capability — drag and drop, geospatial, real-time collaboration — **that is the choice that matters** and it should be made first. Everything else bends around it.

---

## 7 — Checkpoints

> Version control is key. Git is the version control software; GitHub stores repos in the cloud.

### The mental model

Git is a save point in a game. You are not backing work up; you are creating **a state you can return to**. That is what makes ambitious changes safe: the cost of a bad direction stops being "lose an afternoon" and becomes "type one command".

### The commands, and what they are for

| Command | What it does |
|---|---|
| `git status` | What has changed, and what is untracked — not being watched yet |
| `git add .` / `git add <file>` | Stage changes for the next save point |
| `git commit -m "message"` | Make the save point. The message explains what changed |
| `git log` | The list of save points |
| `git reset` | Go back. The escape hatch |
| `git remote add origin <url>` | Link this local folder to a GitHub repo |
| `git push -u origin main` | Send it to GitHub |

You can ask for any of this in plain language rather than typing it. "Save a checkpoint here", "what's changed?", "put this on GitHub" all work. Knowing the shape is what matters, not the syntax.

### When to checkpoint

- After each phase gate — the thinking is done, the structure exists, a feature works.
- **Before** any risky refactor, not after. The save point is only useful if it predates the damage.
- Before letting a long autonomous run loose on the codebase.
- At the end of a session, so tomorrow starts from a known state.

### The rules

- **Never commit or push unless the user asks in that turn.** Propose the checkpoint — "good point to save; want me to commit?" — and wait. Unapproved work stays in the working tree, and that is fine; the working tree is a legitimate place for things to live.
- **A push may be a deploy.** If pushing to `main` puts something live, that is a hard rule and it belongs at the top of the root `CLAUDE.md`. Confirm every single time.
- **Check the author identity** before the first commit on a new project. Different projects use different accounts, and a commit under the wrong name is annoying to unpick.
- **Data does not go in git.** Repos hold code and thinking. Datasets stay local or go to object storage, and `.gitignore` says so from commit one.

---

## 8 — Debugging

> Everything goes wrong at some point. Even the best get bugs. Learning this is key.

### The protocol

Three steps, in order. The discipline is not skipping to three.

**1. Where is the problem?**
Narrow before you theorise. Which layer — the data, the logic, the interface, the environment, the deploy? Which file? Which line? Did it ever work, and what changed since? Bisect: cut the search space in half, check, repeat. Most of debugging is this step, and most bad debugging is skipping it.

**2. What is the problem?**
Now say what is actually happening, precisely, and what should be happening instead. **Paste the error verbatim** — the whole thing, including the stack trace. A paraphrased error is a different error. If there is no error, describe the exact difference between expected and observed.

**3. What is the bespoke fix?**
Bespoke, because the generic fix for that error class is often not the fix for this instance. One change at a time. Verify each before making the next — two simultaneous fixes turn a solved bug into a mystery.

### Rules for this mode

- **Reproduce before fixing.** A bug you cannot trigger on demand is not a bug you can confirm you have fixed.
- **One change at a time.** Always.
- **Suspect the recent change first.** It is almost always the recent change.
- **Read the error properly.** Line numbers and file paths are in there. Most errors say what is wrong before anyone reads to the end.
- **When three attempts have failed, stop patching and go back to step one.** Three failed fixes means the diagnosis is wrong, not that the fourth fix will land.
- **`git reset` is not defeat.** If a session has dug a hole, going back to the last good checkpoint and starting the fix afresh is usually faster than excavating.
- **State what you actually know.** "The build fails at line 40 of X" is useful. "I think it might be a caching issue" without evidence sends everyone down a corridor.

### What the user should supply

The exact error text. What they did immediately before. What they expected. Whether it ever worked. What changed. Screenshots for anything visual. That list is short and it resolves most bugs faster than any amount of clever inference.

---

## 9 — Context

> The more information YOU provide, the better the chance of good results.

This one is the user's job, and it should be said out loud, because it is the highest-leverage thing they personally control.

### What to supply, and when

| Supply | When |
|---|---|
| The PRD, or the north star | At the start of any session on this project |
| A real example of the output you want | Whenever the output has a shape or a style |
| A reference — a link, a screenshot, a page you like | Any time appearance or structure matters |
| Sample data, real not invented | Anything that processes data |
| The environment — machine, versions, what's installed, what's already running | Anything that runs |
| Preferences, especially the negative ones | Early, once. "I hate X" is worth more than "I like Y" |
| The exact error, in full | Every time something breaks |
| Who this is for and what they will do with it | Any writing, any interface |

### The other half of the rule

More context is better **only while it is true and relevant**. Wrong context is worse than no context, because it is believed. Stale context is wrong context that used to be right.

So: supply generously into the conversation, and be miserly about what gets written to disk. The chat is cheap and disappears. A file persists, and gets read back into every future session as though it were still true.

The ledger in [HIERARCHY.md](HIERARCHY.md#context-hygiene--the-ledger) decides what earns a file, and the three tests in [SKILL.md](SKILL.md#restraint--the-urge-to-write-a-file-is-a-signal) decide whether to write one at all. Nearly everything belongs in the conversation and nowhere else.

The asymmetry is worth stating plainly, because it is counter-intuitive: **the user should be generous with context and the model should be miserly with files.** Those are not in tension. What the user says is theirs and is true; what the model writes down is its own reasoning wearing the same clothes.
