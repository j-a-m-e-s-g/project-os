<!--
  Disciplines 7 (Checkpoints) and 8 (Debugging). Lives in plan/.
  How this project is run. Short. A new session reads this to know what it may and may not do.
-->

# <Project> — workflow

## What Claude may do unasked

<!-- Edit files in the working tree, run the dev server, read anything, run tests. Be specific. -->

## What needs asking first

<!-- Committing, pushing, deleting, installing dependencies, touching production, anything irreversible. -->

## Checkpoints

- **Repo:** <!-- URL, or "local only, no remote". -->
- **Author identity:** <!-- Which account commits here. -->
- **Deploy trigger:** <!-- What pushing where puts live, if anything. If a push deploys, that also belongs at the top of the root CLAUDE.md. -->
- **Checkpoint at:** each phase gate · before any risky refactor · before a long autonomous run · end of session.
- **Never commit or push unless asked in that turn.**
- **`.gitignore` covers:** <!-- Data, secrets, caches, build output. -->

## Running it

```bash
# the exact commands, including ports and gotchas
```

## Before saying done

<!-- The actual gate. Typecheck, build, tests, a visual check — whatever is real here.
     Plus: the acceptance tests in prd.md for whatever was built. -->

## When it breaks

1. **Where** — which layer, which file, which line. Did it ever work, what changed. Bisect.
2. **What** — exact error, verbatim, with the stack trace. Expected versus observed.
3. **Fix** — one change at a time. Verify each before the next.

Three failed fixes means the diagnosis is wrong. Go back to step one.
`git reset` to the last good checkpoint is usually faster than excavating.

## Delegating work here

<!-- Delete this section unless subagents are actually in use. The default is that they are not. -->

- **May be handed to a subagent:** <!-- e.g. searching the codebase, running the test suite and reporting only failures, verifying a built feature against its acceptance tests. Work whose output floods the conversation and is never reread. -->
- **Must not be:** <!-- anything needing the full conversation context, anything in a dependency chain, anything where two agents would touch the same files. -->
- A subagent gets no main-conversation history. Every delegation states the objective, the output format, the tools and sources, and the boundaries.

## Known gotchas

<!-- The things that have bitten before. Add to this as they happen — post hoc, never speculatively. -->
