# /breakdown — Backlog Shaping and Implementation Sequencing

Translate the PRD into a sequenced, implementation-ready backlog organized into
milestones. This is the bridge between requirements and actual development tickets.

Usage: `/breakdown`

---

## Instructions

### Step 1 — Read the Foundation Documents

Read both:
- `/docs/requirements.md` — the PRD (what needs to be built)
- `/docs/architecture.md` — the architecture (how it will be built)

If either is missing, tell the user which one is absent and which command to run first.

---

### Step 2 — Identify Implementation Units

Analyze the must-have requirements and architecture together to identify natural
implementation units — groups of work that logically belong together.

Apply these principles when grouping:

**Group things that must be built together**
- If you can't test A without B, they belong in the same unit
- If A and B touch the same data model or component, consider grouping them

**Separate things that are independently deliverable**
- If a feature can be built, tested, and demoed without another feature being present,
  it should be its own unit

**Respect hard dependencies**
- Identify which units block others
- A unit that is blocked by nothing and blocks many things should be built first
- Never sequence a unit before its dependencies are complete

**Size units appropriately**
- A unit should be completable in one focused development session (hours, not days)
- If a unit feels too large, look for a natural split point
- If two units are tiny and always done together, merge them

---

### Step 3 — Propose the Strategic Build Approach

Before presenting the full backlog, explicitly address the build strategy question
for Milestone 1:

**Option A — Walking Skeleton (end-to-end thin slice)**
Build the thinnest possible version of the full stack first: a minimal but complete
path from user input to data persistence and back. Nothing is feature-complete, but
everything is connected. Best when:
- Integration risk is high (unknown if the stack pieces connect cleanly)
- You want something runnable quickly to validate the architecture
- The system has multiple layers that need to work together

**Option B — Foundation First (layered)**
Build the core infrastructure layer by layer before adding features: data model,
then API, then UI. Best when:
- The data model is complex and expensive to change later
- The architecture is well-understood and low-risk
- There are hard dependencies that make a thin slice impractical

Recommend one approach with a one-paragraph rationale based on this specific project.
Then ask: "Does this sequencing strategy make sense, or would you prefer the other approach?"

Adjust based on the user's response before proceeding.

---

### Step 4 — Present the Full Sequenced Backlog

Organize all implementation units into milestones. Use this structure:

```
## Milestone 1: {name} — {one-line description of what milestone 1 delivers}

These items should be built in order:

1. {Unit name}
   Requirements covered: {list requirement numbers from PRD}
   Depends on: nothing / {other unit}
   Rationale: {why this comes first or at this position}

2. {Unit name}
   ...

---

## Milestone 2: {name} — {what milestone 2 delivers}

...

---

## Backlog (Nice-to-Have)
Items from the nice-to-have requirements, unsequenced until milestone 2 is complete.
- {item}
- {item}
```

After presenting, ask:
"Does this sequence make sense? Are there any items you'd reorder, merge, split,
or move to the backlog before I create the Jira structure?"

Do not proceed until the user approves the backlog shape.

---

### Step 5 — Create the Jira Structure

Using the approved backlog, create the following in Jira:

1. **One Epic** per milestone
   - Epic name: `{Project Name} — {Milestone Name}`
   - Description: what this milestone delivers and why it comes at this point

2. **One Story or Task per implementation unit** (do not create tickets yet for
   backlog/nice-to-have items — those get planned when their milestone begins)
   - Summary: action-oriented title
   - Description: requirements covered, dependencies, rationale from the breakdown
   - Link each ticket to its parent Epic

3. **Report back** with:
   - A summary of epics and tickets created
   - The Jira links for each epic

---

### Step 6 — Write the Breakdown Document

Save the approved backlog to `/docs/breakdown.md` so it persists across sessions:

```markdown
# Implementation Breakdown

**Project**: {project name}
**Date**: {today's date}
**Build Strategy**: Walking Skeleton / Foundation First (with rationale)

---

## Milestone 1: {name}
{Description of what this milestone delivers}

| # | Unit | Requirements | Depends On | Jira Ticket |
|---|------|-------------|------------|-------------|
| 1 | ...  | REQ-1, REQ-2 | — | {KEY-1} |
| 2 | ...  | REQ-3 | Unit 1 | {KEY-2} |

## Milestone 2: {name}
...

## Backlog
- {item} (REQ-N)
```

---

### Step 7 — Hand Off

Report:
- That `/docs/breakdown.md` has been saved
- The Jira epics and tickets created
- What the first implementation unit is

Then prompt:
"Backlog is shaped. Run `/scaffold` to initialize the repository, then use
`/plan {first unit name}` to begin implementation on the first ticket."
