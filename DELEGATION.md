# DELEGATION — agents, subagents, and when not to

## The default answer is no

Delegation is expensive and the cost is not obvious, so here are the numbers before anything else.

- An agent uses roughly **4× the tokens** of a chat turn.
- A multi-agent system uses roughly **15×**.
- Token usage alone explains about **80% of performance variance** in Anthropic's own research system. Tool calls and model choice are the secondary factors.

That means a multi-agent system is only viable where **the value of the task is high enough to pay for the tokens**. Most of what gets built is not.

The governing principle, from Anthropic's *Building Effective Agents*:

> Success in the LLM space isn't about building the most sophisticated system. It's about building the *right* system for your needs.

And the connection back to this skill, which matters more than any technique below:

> **Reaching for an agent is usually a symptom of skipping discipline 4.** If you can write the steps down, it is a workflow, not an agent. If you cannot write them down, the problem is that the computational thinking has not been done — not that you need a fleet.

---

## The escalation ladder

Climb a rung only when the rung below it **demonstrably failed**. Not when you suspect it might.

| Rung | What it is | Cost |
|---|---|---|
| 1 | One good prompt | 1× |
| 2 | A prompt with tools and retrieval | ~1× |
| 3 | A **workflow** — LLM calls orchestrated through a predefined code path | ~2–3× |
| 4 | An **agent** — the model directs its own process and tool use | ~4× |
| 5 | **Multi-agent** — an orchestrator delegating to parallel workers | ~15× |

The distinction between rungs 3 and 4 is the one that matters: in a workflow **you** decide the sequence; in an agent **the model** decides. If you know the sequence, encoding it is cheaper, faster, more debuggable and more reliable than paying a model to rediscover it every run.

---

# Part A — delegating your own build work

Claude Code subagents. This is the common case and the one worth getting right.

## The three legitimate triggers

A subagent is earned when **one of these is true**. Nothing else counts.

1. **Context isolation.** A side task would flood the conversation with search results, logs or file contents you will never reference again. The subagent does that work in its own window and returns only the conclusion.
2. **A repeated worker.** You keep spawning the same kind of helper with the same instructions. Write it down once.
3. **An enforced restriction.** You want a genuinely read-only reviewer, or a worker that cannot reach a particular tool. `permissionMode: plan` and a `tools` allowlist make that structural rather than hoped-for.

Not triggers: it sounds sophisticated; the task is large; you want it to feel parallel; you read an article about agents.

## Use the built-ins before writing one

Most delegation needs no custom agent at all.

| Agent | For | Note |
|---|---|---|
| `Explore` | Broad read-only search across many files when you want the conclusion, not the file dumps | Omits `CLAUDE.md` and git status — do not use it where project conventions matter |
| `Plan` | Read-only research feeding an implementation plan | Same omission |
| `general-purpose` | Multi-step work needing the full toolset | The catch-all |
| `fork` | Work that needs everything you already have in context | Inherits the parent conversation and reuses the prompt cache; cannot fork again |

## The delegation brief

This is the largest single failure mode, and it is a prompting failure, not an architecture one. From the research post: instructions as vague as *"research the semiconductor shortage"* left subagents duplicating each other's searches and misreading the task.

**Every delegation states four things:**

1. **The objective** — what specifically to find out or do.
2. **The output format** — what comes back, in what shape, at what length.
3. **Tools and sources** — where to look, and where not to.
4. **The boundaries** — what this agent owns and what belongs to another.

If you are spawning several, the boundaries have to be mutually exclusive or they will do each other's work. Say what each one is *not* covering.

## Writing a subagent

`.claude/agents/<name>.md` for a project (commit it), `~/.claude/agents/<name>.md` for everywhere. Project wins over user.

```markdown
---
name: spec-checker
description: Checks a built feature against its acceptance tests in plan/prd.md. Use after any feature is claimed done.
tools: Read, Grep, Glob, Bash
model: sonnet
permissionMode: plan
---

You verify, you do not fix. For each must-have feature in plan/prd.md,
find its "Done when" line and check it against the actual build.
Return a table: feature, pass/fail, and the evidence you checked.
Say "cannot verify" rather than guessing.
```

The fields that earn their place:

| Field | Why |
|---|---|
| `description` | Drives automatic delegation. Vague here means it never fires, or fires wrongly |
| `tools` / `disallowedTools` | Allowlist beats denylist. Grant the minimum |
| `model` | `haiku` for mechanical work, `inherit` by default |
| `permissionMode: plan` | Read-only. The cheapest way to make a reviewer safe |
| `skills` | Preloads skill content at launch, avoiding discovery overhead |
| `isolation: worktree` | Only when parallel agents would otherwise fight over the same files |
| `memory` | Persistent learning across sessions. Rarely needed; curate it if used |

**What loads in a subagent's context:** its own system prompt, the task you passed, `CLAUDE.md` files, git status, and any preloaded skills. **Not** the main conversation history. So anything it needs to know, you have to tell it — that is exactly why the delegation brief matters.

## Scale the effort to the task

Put the scaling in the instruction, or you get fifty agents for a lookup.

| Task | Agents | Tool calls each |
|---|---|---|
| Simple fact-finding | 1 | 3–10 |
| A direct comparison | 2–4 | 10–15 |
| Genuinely complex research | 10+, with clearly divided responsibilities | more |

Where work is genuinely parallel, running 3–5 subagents at once rather than in sequence cut research time by up to 90% in Anthropic's system. That gain is real — but only for work that was actually independent.

## When delegation does not work

- **Coding tasks.** Stated plainly in the research post: fewer truly parallelisable subtasks than research. Most builds are a dependency chain, and a chain does not fan out.
- **Work needing shared context.** Subagents cannot see each other's windows. If they all need the same evolving picture, you are paying 15× to reconstruct it repeatedly.
- **Heavy inter-agent dependencies.** Coordination between agents is weak. If B needs A's answer to start, that is a sequence, not a fan-out.
- **Low-value tasks.** The multiplier has to be worth something.
- **Small deterministic problems.** A script is better than an agent at anything a script can do.

---

# Part B — architecture: designing an agentic system

A different problem from Part A: not delegating your build, but designing a system where models call models. This is the part that separates something that works from a pile of prompts calling each other.

**Most of it applies to Part A too.** The moment you fan out more than one subagent, you are making architectural decisions — about boundaries, contracts, and what happens when two workers disagree. The seams section below is the same either way.

## Workflows versus agents

- A **workflow** orchestrates LLM calls and tools through **predefined code paths**.
- An **agent** has the model **dynamically direct its own process** and tool use.

Start with workflows. Agents trade latency and cost for the ability to handle what you could not predict — which is only worth it when there genuinely is something you could not predict.

## Rule zero — architecture is not the first step

**Build the single call first.** One well-prompted call with the right tools and a couple of examples, measured against real cases. Only when you can point at *how* it fails do you add a layer — and you add exactly one, then measure again.

Architecture designed before that measurement is a guess about a failure you have not seen. It is also nearly impossible to unwind, because every later decision assumes it. Most systems that need to be rebuilt were architected on day one.

## Step 1 — Name the failure you are routing around

Every pattern exists to fix one specific failure of a single call. Find yours and the architecture falls out. If you cannot name the failure, you do not need a pattern yet.

| The single call fails because… | The shape you need |
|---|---|
| It rushes a task with genuinely distinct stages, doing all of them shallowly | **Chain** — sequence with gates |
| One prompt has to serve very different inputs, so it is mediocre at all of them | **Route** — classify, then specialise |
| Independent parts are done serially and it is too slow, or one attempt is not trustworthy | **Parallelise** — section, or vote |
| You cannot write the list of subtasks in advance; it depends on the input | **Orchestrator–workers** |
| The first output is never right, but you can articulate *why* | **Evaluator–optimiser** loop |
| It is right but you cannot tell when it is wrong | **Not an architecture problem.** Build evaluation first |

That last row catches more projects than the others combined.

## Step 2 — Decompose, with five questions

Ask these of the task, in order. The answers *are* the architecture.

1. **What are the stages?** Anything you would describe with "first… then…" is a sequence.
2. **Which stages are independent?** Independent means neither needs the other's output. Those can run at once. Most things that look parallel are not.
3. **Which stage decides whether a later stage exists at all?** If the answer is "the model, at runtime", that is your dynamic boundary — everything downstream of it is orchestrated rather than scripted. Everything upstream should be plain code.
4. **Where is there a checkable criterion?** Anywhere you can write a pass/fail test, put a gate.
5. **What must a human see before it continues?** Those are approval gates, and they are architecture, not UX.

## Step 3 — The topologies

```
CHAIN            A ──▶ gate ──▶ B ──▶ gate ──▶ C
                 Fixed stages. The gate is the point; without it, it is one long prompt.

ROUTE                     ┌──▶ handler A
                 classify ─┼──▶ handler B
                          └──▶ handler C
                 The classifier is small, cheap and does nothing else.

FAN-OUT / FAN-IN     ┌─▶ worker ─┐
                 split ├─▶ worker ─┤ synthesise
                     └─▶ worker ─┘
                 Synthesis is a real step, not a concatenation.

ORCHESTRATOR     orchestrator ⇄ workers (count and roles decided at runtime)
                       │
                       └─▶ synthesise
                 Use only when the split genuinely cannot be written in advance.

LOOP             generate ──▶ evaluate ──┐
                     ▲                   │
                     └──── if fail ──────┘   (bounded — always)
```

**Real systems compose these.** A route whose branches are chains, one of which ends in an evaluator loop. Draw the whole thing on one page before building any of it; if it does not fit on a page, it is too big to debug.

The tell for orchestrator over fan-out: in fan-out **you** wrote the split; in orchestrator the split is decided at runtime. Prefer writing the split. It is cheaper, deterministic, and you can read it.

## Step 4 — Get the seams right

The patterns are easy. The joins are where systems fail.

- **Contracts between stages.** Every stage declares what it emits — structured output at the seams, prose only at the edges where a human reads it. Prose passed between stages is re-parsed by the next model and quietly mutates. This is the single highest-value decision in the whole design.
- **Gates should be code, not judgement.** Wherever a check can be programmatic, make it programmatic. A gate that a model evaluates is one more thing that can be wrong, in a position where being wrong is invisible.
- **State lives outside the conversation.** Workers write their output to the filesystem or a store and return a **reference**, not the content. This avoids the telephone game, and it stops the orchestrator's context filling with material it is only passing along.
- **Fan-in is a real step with a real job.** Workers *will* contradict each other. Synthesis has to reconcile, not staple. Say explicitly what happens on disagreement — prefer one source, escalate, or re-run.
- **Boundaries must be mutually exclusive.** Tell each worker what it does *and what it is not covering*. Overlapping briefs produce three copies of the same search, which is the most common and most expensive failure in the whole field.
- **Every loop is bounded.** A maximum iteration count and a stopping condition, always. An unbounded evaluator loop is a bill.

## Step 5 — Size it

| Task | Shape |
|---|---|
| Simple fact-finding | 1 agent, 3–10 tool calls |
| A direct comparison | 2–4 workers, 10–15 calls each |
| Genuinely complex research | 10+, with clearly divided responsibilities |

Put the scaling in the orchestrator's prompt. Without it you get fifty workers for a lookup.

Depth matters more than width: **two layers is nearly always enough.** Three is a system nobody can debug, because the failure is four hops from the symptom.

## Step 6 — Design the failure path

Agents are stateful, so errors compound: a small wrong turn at step three is catastrophic by step thirty. Decide in advance, per stage:

- **Retry** — transient, cheap, safe to repeat.
- **Degrade** — carry on with a worse answer and say so.
- **Checkpoint and resume** — long or expensive work. Resume from the last good state; never restart.
- **Escalate to a human** — anything irreversible, or the third consecutive failure of the same stage.

**Tell the agent when a tool failed.** They adapt better than you expect. Silent failure is what actually kills runs.

## Step 7 — You cannot debug what you cannot see

Trace every stage: what went in, what came out, which tools were called, how long it took. Agentic systems are non-deterministic — the same input produces different paths — so without tracing you are guessing whether the problem is the prompt, the tool, the source, or the join.

Add this before you add the second layer, not after the first outage.

## The architecture review

Before building, answer all eight. A no anywhere means go back a step.

1. Can I name the specific failure of a single call that this exists to fix?
2. Have I actually measured that failure on real cases?
3. Is every stage boundary a place where output shape genuinely changes?
4. Is every gate that could be code, code?
5. Are worker briefs mutually exclusive and individually complete?
6. Is every loop bounded?
7. Is there a defined behaviour for each stage failing?
8. Could I explain the whole thing on one page to someone who has not seen it?

---

## The five patterns — reference

| Pattern | What it does | Use when |
|---|---|---|
| **Prompt chaining** | Sequential steps, each acting on the last, with programmatic gates between | The task decomposes cleanly into fixed subtasks and you'll trade latency for accuracy |
| **Routing** | Classify the input, send it to a specialised handler | There are distinct categories genuinely better handled separately — including routing easy cases to a cheaper model |
| **Parallelisation** | *Sectioning*: independent subtasks at once. *Voting*: the same task several times | Speed from independence, or confidence from multiple attempts |
| **Orchestrator–workers** | A central model breaks the task down, delegates, synthesises | You **cannot predict the subtasks** — they depend on the input |
| **Evaluator–optimiser** | One model produces, another critiques, loop | There are clear evaluation criteria and iteration measurably helps |

## When a real agent is warranted

All three should hold:

- The problem is **open-ended** and you cannot predict the number of steps.
- The agent gets **ground truth from the environment at each step** — tool results, code execution, test output. Without a feedback signal it is guessing with extra steps.
- It runs **sandboxed, with guardrails**, and there is a stopping condition.

## Tool design — the agent-computer interface

> Think about how much effort goes into human-computer interfaces, and plan to invest just as much in the agent-computer interface.

A wrong tool choice is a failed run, and **a bad tool description sends an agent down entirely the wrong path**. Anthropic built a tool-testing agent that rewrote flawed descriptions and cut completion time by 40%.

- Parameter names and docs should read like **a good docstring written for a junior developer**.
- Keep formats close to what appears naturally in text. Avoid exact line counts and heavy escaping.
- Leave the model room to think before it commits to output.
- **Poka-yoke the arguments** — design them so the mistake is hard to make. The SWE-bench agent needed *absolute* filepaths because it made errors with relative ones once it had moved out of the root directory.
- Each tool needs a distinct purpose. Overlapping tools produce coin-flips.
- Test them, watch where the model trips, and fix the description rather than the prompt.

## Prompting an orchestrator

- **Think like your agent.** Watch it step by step. The failure modes are mundane: continuing past sufficient results, over-long queries, picking the wrong tool.
- **Teach it to delegate** — the four elements above.
- **Scale effort to complexity**, explicitly, in the prompt.
- **Start wide, then narrow.** Models default to over-specific queries that return nothing. Tell it to open broad.
- **Guide the thinking.** Extended thinking works as a controllable scratchpad: plan before acting, assess after each tool result.
- **Call tools in parallel** where they are independent.
- Prefer **heuristics over rigid rules** — encode how a good human does it, not a decision tree.

## Evaluate it, or you are guessing

- **Start small and early.** About **20 queries** representing real usage is enough to see change. Early effect sizes are large; do not wait for a big dataset.
- **LLM-as-judge.** For free-form output, a single call scoring against a rubric — factual accuracy, citation accuracy, completeness, source quality, tool efficiency — emitting **0.0–1.0 plus a pass/fail** proved the most consistent and the closest to human judgement.
- **Humans catch what automation misses.** Early agents preferred SEO content farms over authoritative but lower-ranked academic sources. Only a person noticed.
- **Judge the end state, not the process.** Agents take valid alternative paths; grade whether they arrived.

## State, failure and the long run

- **Agents are stateful and errors compound.** A minor failure at step three is catastrophic by step thirty.
- **Resume from a checkpoint** rather than restarting. This is discipline 7 again, one layer down.
- **Tell the agent when a tool failed.** They adapt better than you would expect. Silent failure is what kills runs.
- **Combine model judgement with deterministic safeguards** — retries, limits, checkpoints.
- **Have workers write outputs to the filesystem and return references**, rather than passing everything back through the orchestrator. It avoids the telephone game and the token cost of copying through conversation history.
- **Assume your harness assumptions go stale.** Everything you build to compensate for a current model limitation becomes dead weight when the limitation lifts. Design for replaceability.

---

## Anti-patterns

- A subagent because it sounds sophisticated.
- Multi-agent for a coding task. The post says it directly: fewer parallelisable subtasks.
- An agent where a script would do.
- Fanning out work whose parts depend on each other, then wondering why the results contradict.
- Spawning agents with vague briefs and getting three copies of the same search.
- A fleet with no evaluation — you cannot tell whether it works, only that it is expensive.
- Building the orchestration before establishing that a single well-prompted call fails.

**The test, once more:** if you can write the steps down, it is a workflow. If you cannot, go back to discipline 4 before you go forward to an agent.

---

## Sources

Everything above is drawn from these. Go to them for the detail and the worked examples; this file is the compressed, opinionated version.

- **[Building effective agents](https://www.anthropic.com/engineering/building-effective-agents)** — the workflow/agent distinction, all five patterns, tool design and the agent-computer interface, and the case for simplicity. The foundation for Part B.
- **[How we built our multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system)** — the token multipliers, orchestrator-worker architecture, the delegation-brief failure modes, effort scaling, evaluation with LLM judges, and the production lessons on state and compounding errors.
- **[Managed agents](https://www.anthropic.com/engineering/managed-agents)** — mostly hosted-infrastructure engineering and out of scope here, but the transferable principle is real: separate the brain from the hands from the session, keep credentials out of the execution environment, and assume every assumption you encode about current model limitations will go stale.
- **[Claude Code subagents](https://code.claude.com/docs/en/sub-agents)** — the frontmatter reference, scope and precedence, what loads at startup, and the built-in agents.

The two Anthropic engineering posts are stable thinking. **The Claude Code docs are living** — frontmatter fields and defaults change between versions. Verify against the docs before relying on a specific field, rather than trusting this file's snapshot.
