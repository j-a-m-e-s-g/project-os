# THINKING — disciplines 1 to 5

The sequence that turns "I want to build a thing" into a specification a model can execute without guessing.

They are in this order for a reason. You cannot pick features (4) before you know how you win (3), and you cannot know how you win before you know what game you are playing (2), and you cannot know the game before you know what you are actually trying to make true (1). Every project that goes wrong went wrong by starting at 4.

Each discipline below has the same five parts: **what it is for**, **the questions**, **pass criteria**, **pushbacks**, and **the shape** — the slots a good answer fills, what each slot prevents going wrong downstream, and the move that turns a thin answer into a usable one.

The shape sections are deliberately domain-free, because the point is the structure and not the story. Fill the slots with the user's actual project in the user's actual words; never carry the `<placeholder>` phrasing into a file you write.

---

## 1 — Thinking

> Clarity of purpose, vision, intention, outcomes, product.

### What it is for

To establish what the user is actually trying to make true in the world. Not the thing they want to build — the change they want to exist once it is built. Everything downstream inherits from this, which is why a woolly answer here produces a woolly project no amount of good engineering can rescue.

This produces `north-star.md`, and it is the document you will point every future session back to.

### The questions

- **(free text)** In one sentence: what is different in the world once this exists? Describe the *change*, not the software.
- **(free text)** Who is it for? Name a real person if you can — not a segment. "the person who rebuilds this from memory every Monday" beats "internal stakeholders" — and a real name beats both.
- **(MC)** Why now? What is the clock? *Options drawn from the domain: an external deadline · a recurring pain that has got too expensive · an opportunity with a window · a decision that needs evidence · no clock, it's an itch (and that is a fine answer if true).*
- **(free text)** How will you know it worked? A number, a described state of the world, or a specific reaction from a specific person.
- **(free text)** Name two or three things this is explicitly **not**. What would you refuse to build even if asked nicely?
- **(MC)** If you could only have one of these, which? *Options: it exists at all, this week · it is genuinely good · it is comprehensive · someone else can run it without you.*

That last one is the highest-signal question in the entire skill. Ask it.

### Pass criteria

Advance when **all** of these hold:

- The outcome is stated as a change in the world, not a feature list.
- A stranger reading it could tell whether it had happened.
- There is at least one named non-goal.
- The "who" is specific enough that you could imagine them being annoyed by a bad version.

### Pushbacks

| They say | You ask |
|---|---|
| "Make it better / more efficient" | "Better than what, measured how? What is the current thing you'd be beating?" |
| "For the team" | "Which person opens it first on a Monday morning? Start with them." |
| "It should be comprehensive" | "Comprehensive costs you the launch. What is the smallest version that would still be worth using?" |
| "I'll know it when I see it" | "Then describe the moment you see it. Who is looking at what, and what do they say?" |
| Non-goals left empty | "Every project needs cuts to stay sharp. Name two things this is not, so we don't accidentally build them." |

Push **once**. If the second answer is still thin, write it down, mark the discipline `~`, and name it in the load-bearing assumptions at the end.

### The shape

A north star fills six slots. Each exists because of a specific failure downstream.

| Slot | What it is | The failure it prevents |
|---|---|---|
| The change | A sentence whose subject is a person, doing something they cannot do today | A feature list wearing a mission statement |
| The person | One name, and what they do *instead*, today | Building for the average of several people, which suits none of them |
| The clock | What is different about now | A project with no forcing function, which never finishes |
| The test | An observable event a stranger could confirm | "Better", which can never be shown to have happened |
| The non-goals | At least one thing a reasonable person would assume is in scope | Scope that doubles quietly, one sensible addition at a time |
| The one thing | Which of *exists at all* / *genuinely good* / *comprehensive* / *runs without you* wins when they conflict | Every trade-off downstream being re-argued from scratch |

**Sharpening a thin answer.** The move is always the same — swap a category for an instance, and an adjective for an event:

> Thin: *"A tool to help the team keep track of everything."*
> Sharp: *"<person> can answer <the question they currently ask someone else> in under <a bound>, without asking <the person they ask today>."*

Four things changed and each is load-bearing: the subject became a person rather than the software; the verb became something they demonstrably cannot do today; the bound made it falsifiable; and *"without asking"* named the dependency being removed — which, most of the time, is what the project is actually for.

**The diagnostic.** If you deleted the north star and rebuilt it from the feature list, would you get the same sentence back? If yes, you wrote a feature list.

---

## 2 — Logical

> What is the game?

### What it is for

To describe the system the project sits inside. Who the players are, what moves exist, what the win condition is, what constraints are fixed and which are chosen. The north star says what should be true; this says *what kind of problem that is*.

Cheap and often skipped, and skipping it is why projects solve the wrong problem beautifully.

### The questions

- **(free text)** Describe the situation as it stands today, with no software in it. What happens now, who does it, and where does it hurt?
- **(free text)** Who are the players? Everyone who touches this: users, the people whose work it changes, whoever has to approve it, whoever could kill it.
- **(MC)** What kind of game is it? *Options: a thing that did not exist · replacing a manual process · replacing a bad existing tool · joining up things that already exist but don't talk · a one-off answer to a question, not a lasting thing.*
- **(free text)** What is fixed and cannot move — deadlines, data you cannot get, systems you must live with, people who must sign off?
- **(free text)** What already exists that you could use, buy, or copy instead of building? Answer honestly. Sometimes the right build is nothing.
- **(MC)** Where does this live once it exists? *Options: on your machine · a URL your team uses · a URL the public uses · inside an existing tool · a file you send to people.*

### Pass criteria

- The today-state is described concretely enough that you could time it with a stopwatch.
- Every player is named or role-named, and you know which one can kill the project.
- At least one hard constraint is written down.
- The build-versus-buy question has been asked out loud, and answered.

### Pushbacks

| They say | You ask |
|---|---|
| "There's no current process" | "So what happens today when someone needs this? Even 'nothing, and it gets missed' is a process." |
| "Everyone will use it" | "Everyone is nobody. Who is the first user, on day one?" |
| "No constraints" | "None at all? Not time, not data access, not who has to approve it?" |
| A tool that already does this | "This exists as X. What's wrong with X that's worth a build?" Say it plainly and let them answer. |

### The shape

Five slots.

| Slot | What it is | The failure it prevents |
|---|---|---|
| Today | The current process in the present tense, with a duration and the point where it hurts | Building a replacement for a process nobody has actually described |
| The players | Everyone who touches it, and what each can *do to it* — use it, kill it, block it, never see it | Meeting the person who can veto it after it is built |
| The game | Which of the four this is | A build with no theory of what winning means |
| What is fixed | The data you must use, the system you cannot replace, the deadline, the budget | Constraints found late, which are rewrites rather than decisions |
| The near-miss | The existing tool that gets most of the way, and the exact thing it fails at | Rebuilding something that already exists, worse |

**The four games.** Nearly every project is one of these, and naming which constrains everything downstream: *replacing a manual process that already happens* · *making visible something that exists but cannot be seen* · *removing a dependency on one person* · *creating something that does not exist at all*. The last is the rarest and by far the most expensive. Be suspicious when it is claimed.

> Thin: *"It's a manual process at the moment and it's pretty inefficient."*
> Sharp: *"<person> does <the steps>, taking <duration>, and gets it wrong when <the specific condition>."*

"Inefficient" cannot be built against. A process you can time, with a named failure condition, can — and that failure condition often turns out to be the only requirement that mattered.

**The near-miss is the most skipped slot and the most valuable.** If nobody can name the thing that nearly works, nobody has looked. And the specific point where it fails is frequently the whole of what is worth building.

---

## 3 — Analytical

> Main objectives and how do I play? What do we need to build the product?

### What it is for

To convert the game into objectives and an inventory. Objectives are the two or three things that must be achieved for the north star to come true. The inventory is the honest list of what has to exist for that to happen — including the parts that are not code.

This is where scope gets set, so it is where scope gets cut.

### The questions

- **(free text)** What are the two or three objectives? Each one a thing that must be true, not a task.
- **(MC, per objective)** How do you play this one? *Options tailored to the objective — build it, buy it, do it by hand for now, ignore it for v1.*
- **(free text)** What has to exist that is not software? Data that needs gathering, a decision nobody has made, someone's permission, content that has to be written.
- **(MC)** What is the shape of v1? *Options: the smallest thing that is useful to one person (Recommended in most cases) · the version you'd show the team · the version you'd show outsiders · the whole thing.*
- **(free text)** What is deliberately deferred? Not "never" — "not now". Name it so it stops nagging.
- **(MC)** What is the riskiest part — the bit most likely to make this not work? *Options: the data doesn't exist or is bad · nobody uses it · it's harder to build than it looks · it goes stale · it never gets finished.*

### Pass criteria

- Two or three objectives. If there are six, they are tasks, not objectives — push back.
- Every objective has a chosen play, and at least one is "by hand for now" or "not in v1" unless the project is tiny.
- The non-software work is listed. It is almost always the thing that actually delays the project.
- The riskiest part is named, and there is one sentence on what would be done about it.

### Pushbacks

| They say | You ask |
|---|---|
| Six objectives | "Those are tasks. Which two or three, if achieved, make the rest either easy or unnecessary?" |
| Everything in v1 | "What's the version you could use yourself on Friday? Build that first — the rest gets better once you've used it." |
| "Nothing's risky" | "Then it's already done. What's the part you're quietly hoping works out?" |
| Deferred list empty | "If nothing's deferred, v1 is v3. Name two things that can wait." |

### The shape

| Slot | What it is | The failure it prevents |
|---|---|---|
| Objectives | Two or three, each a **capability someone gains**, never a component that exists | A parts list mistaken for a plan |
| Build or by hand | Per objective: built now, or done manually for now | Automating a judgement a person could simply make in a field |
| Not a software problem | The decision, agreement or judgement that no amount of building resolves | Building elaborately around a hole a five-minute conversation would fill |
| The v1 line | The smallest version genuinely useful to the named person | The smallest version that *runs*, which is not the same thing and helps nobody |
| Deferred | Named explicitly, so it stops being re-argued | Scope creep arriving disguised as clarification |
| The riskiest assumption | The one that wastes the whole build if wrong, and what makes it survivable | Finding it out last |

> Thin: *"A dashboard that shows everything in one place."*
> Sharp: *"<person> can see <the one thing that changes what they do next> and act on it without <the step they take today>."*

"Everything" is not an objective; it is the absence of one. An objective names what somebody can now **do**.

**Take the by-hand option seriously.** A large share of first-version objectives should be a field someone fills in rather than a system that computes it. Writing "by hand for now" is not a failure of ambition — it is the cheapest way to find out whether the computed version was ever needed.

---

## 4 — Computational

> How to fit the logic into a set of problems? How do I enforce those rules? Key features: what do we want to deliver?

### What it is for

To turn objectives into features, and features into **rules that can be checked**. This is the discipline most people skip and it is the one with the largest effect on whether the build lands.

A feature nobody can test is a feature nobody can finish. The whole point of "how do I enforce those rules" is that the rules have to be *enforceable* — written so that anyone, including a model, can look at the built thing and say yes or no.

### The questions

- **(free text)** List the key features. User-visible outcomes, not implementation. "See every record's status at a glance", not "build a table component".
- **(MC)** Priority for each: *must have for v1 · should have · later.* Force this. A list with no priority is not a spec.
- **(free text, per must-have)** Done when…? One sentence, plain English, that someone could check by looking. This is the acceptance test.
- **(free text)** What are the rules the thing must never break? Invariants: what must always be true, what must never happen.
- **(free text)** What happens at the edges? Empty state, one item, a thousand items, bad input, the network's down, two people at once.
- **(MC)** How does someone know it went wrong? *Options: it says so in plain language · it fails silently and that's acceptable · it must never fail (and here's why).*

### Pass criteria

- Every must-have feature has a **"done when…"** line that could be checked by a person who was not in the conversation.
- Must-haves number fewer than about seven. If there are twelve, the priority pass has not really happened.
- At least one invariant is written down.
- Empty state and error state are both specified. They are the two things always missed and always noticed.

### On acceptance tests — the craft

A good "done when" is **observable, specific, and singular**:

- ✅ "Done when: opening the page with no filters applied shows every record, grouped by status, in under two seconds."
- ✅ "Done when: a record with no status set appears in the 'unknown' group rather than being hidden."
- ❌ "Done when: the table works properly." — Not observable.
- ❌ "Done when: the interface is intuitive." — Not checkable by anyone, including whoever wrote it.
- ❌ "Done when: it handles records, filtering, sorting and export." — Four tests wearing a coat. Split it.

The two that pass share a structure: **an action, a stated condition, and an outcome someone could confirm without asking you.** If you would have to be in the room to judge it, it is not a test.

Write them in the PRD next to their feature. They are what a later Claude session verifies against before claiming the work is done, and they are how the user knows they are not being told a build works when it does not.

### Pushbacks

| They say | You ask |
|---|---|
| Twelve must-haves | "Half of those are 'should'. If you shipped only five, which five?" |
| "Done when it works" | "How would I check that without asking you? Give me the thing I'd look at." |
| "Intuitive / clean / simple" | "Describe what the user does, in order, in three steps. That's the actual requirement." |
| No thought on the empty state | "Day one there's no data in it. What does the screen say?" |

### The shape

| Slot | What it is | The failure it prevents |
|---|---|---|
| Must-haves | Prioritised, each with a *Done when* a stranger could check | A flat list where everything is essential, so nothing is |
| Invariants | What must **never** be true, stated as a negative | Rules that live only in whoever happened to build it |
| The three states | Empty, error, loading — specified individually | The three screens every build ships badly, because none of them was specced |

**Invariants are where the enforcement lives.** *"Never writes to <the source of truth>."* *"Never displays a value that is not in <the source>."* A negative is checkable in a way an aspiration is not, and it is the sentence a later session needs when it is deciding whether a shortcut is allowed.

**Write the empty state first.** On day one there is no data in it, so the empty state is what the user is actually looking at on the day they form their opinion of the thing.

> Thin: *"It should be fast and feel easy to use."*
> Sharp: *"<the action> completes in under <a bound>, at <the realistic data size>."*

---

## 5 — Procedural

> How do I excel in the game? Adding as much detail as possible: what data do we need? What are the variables?

### What it is for

To close every remaining gap where a model would otherwise have to guess. This is the difference between a build that comes back right first time and one that comes back plausible and wrong.

Every unspecified detail is a decision you have delegated to an autocomplete. Some of those are fine. The ones that are not fine live here.

### The questions

- **(free text)** What data does this need? Where does each piece come from, who owns it, how fresh does it need to be?
- **(free text)** What are the entities and their fields? For each thing the system knows about, list what it knows. Types matter: text, number, date, one-of-a-list, yes/no, a link to another thing.
- **(free text)** What are the variables — the things that will change, that someone will want to tune? Thresholds, limits, labels, categories, date ranges.
- **(MC)** How does data get in? *Options: read-only from an existing source (Recommended where possible) · typed in by a person · imported from a file · pulled from an API on a schedule.*
- **(free text)** Who sees what? Anything private, anything that must not leak, anything that changes by user.
- **(free text)** What does it look like? Point at something that exists — a page, a tool, a screenshot, a sketch. A reference is worth a thousand words of description.
- **(MC)** How often does it change, and who maintains it? *Options: set and forget · someone updates it weekly · it reads live and maintains itself (Recommended) · unclear, and that's a risk.*

### Pass criteria

- Every piece of data has a named source and a named owner.
- The core entities have their fields listed with types.
- There is at least one visual reference, or an explicit "I don't care what it looks like".
- The maintenance answer is not "unclear". If it is, that is a `~` and it goes in the load-bearing assumptions.

### Pushbacks

| They say | You ask |
|---|---|
| "We'll figure the data out later" | "Later is the build. If the data isn't there, the build is a mockup. Where does it come from?" |
| "Just make it look nice" | "Nice how? Send me one page you'd be happy for this to resemble." |
| Fields with no types | "Is 'position' free text or one of a fixed list? That single choice changes the whole interface." |
| No owner for the data | "If it's wrong in three weeks, who notices and who fixes it?" |

### The shape

| Slot | What it is | The failure it prevents |
|---|---|---|
| Sources | Every one, with its **owner**, and read-live or copied | An outage with nobody to call, and a copy nobody refreshes |
| Entities and fields | Each field's type, and enumerations **written out in full** | The builder inventing the permitted values — then inventing different ones somewhere else |
| The variables | The things that will change later, named and kept in one place | A label change becoming a search-and-replace across the codebase |
| Human input | What gets typed in and by whom — or explicitly nothing | A tool that quietly becomes somebody's data-entry job |
| Visibility | Who sees what, and what must not leave the building | Discovering the permission model after launch |
| The reference | One existing thing it should look or behave like | A page of adjectives that no two people read the same way |
| Maintenance | Who does what, how often — or "nobody, it reads live" | A recurring job nobody agreed to and everybody stops doing |

> Thin: *"It'll pull the data from the existing system."*
> Sharp: *"<entity> from <source>, owned by <person>, read live. <field>: one of <a · b · c>."*

**Copying is where staleness enters.** Read live and staleness is the source's problem; take a copy and it becomes yours permanently, along with the question of who refreshes it. If a copy is unavoidable, write down who and how often in the same breath.

**A field without its permitted values gets invented by whoever builds it** — and the next person to need that list invents a different one. Enumerations are the most-copied lines in any PRD. Write them out.

---

## What makes a PRD good

The PRD is the assembled output of disciplines 4, 5 and 6. It is not a document to admire; it is the thing a model reads to build without asking. Judge it on that.

**A good PRD:**

- Opens with **one sentence** of purpose. If that sentence needs a comma-spliced list, the project is not clear yet.
- Names the **user as a person**, and says what they are doing when they reach for it.
- Describes features as **user-visible outcomes**, never as implementation. The how is the builder's job; the what is the user's.
- Gives every must-have an **acceptance test** in plain English.
- States **non-goals explicitly**. What this is not is as load-bearing as what it is.
- Spells out **data, entities, fields and types**, with sources and owners.
- Names the **stack** — not to constrain the builder, but because an unsignposted model picks whatever was most common in its training data.
- Specifies **empty, error and edge states**. Every real build spends a third of its time here.
- Marks **assumptions visibly**, so the reader knows which parts are the user's vision and which are a guess.
- Is **short enough to be read in full** before building. Ten tight pages beat forty comprehensive ones nobody opens.

**A PRD fails when it:**

- Is a feature list with no priority — everything is a must-have, so nothing is.
- Requires "an intuitive interface", "clean design", "good UX". Unbuildable and untestable.
- Describes implementation instead of outcome, freezing the wrong decisions early.
- Leaves the data question to the build, at which point the build becomes a mockup.
- Contains no non-goals, and so quietly grows to twice its size.
- Buries assumptions in prose so nobody can tell what was decided and what was invented.
- Was written once and never touched again. When a decision changes, **the PRD changes** — edit the spec, not just the code. A spec that has drifted from the build is worse than no spec, because it will be believed.
