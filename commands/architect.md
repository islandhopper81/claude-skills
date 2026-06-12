# /architect — Tech Stack and System Design

Read the PRD and lead the user through tech stack selection and high-level system design.
Produce an architecture document and finalize `CLAUDE.md` for the project.

Usage: `/architect`

---

## Instructions

### Step 1 — Read the PRD

Read `/docs/requirements.md`. If it does not exist, tell the user:
"No PRD found. Run `/discover` first to capture requirements before making
architecture decisions."

Extract from the PRD:
- The type of system being built (web app, API, CLI, data pipeline, etc.)
- Must-have requirements that carry architectural implications
- Constraints (language preferences, platform, compliance, integrations)
- Scale expectations
- Open questions that affect architecture

---

### Step 2 — Assess What the User Already Knows

Before proposing anything, ask one calibration question:
"Are there any technologies you're already committed to, comfortable with, or want
to avoid? Or would you prefer I recommend a stack from scratch based on the requirements?"

Use the answer to shape how opinionated your proposal is:
- If they have strong preferences → work within them, flag any concerns
- If they're open → make a clear recommendation with rationale, present 1–2 alternatives
  only where the tradeoff is genuinely significant
- If they're somewhere in between → anchor on their preferences and fill in the gaps

---

### Step 3 — Propose the Architecture

Present a complete architecture proposal covering:

**Tech Stack**
For each layer relevant to this project type, recommend a specific technology and
explain why it fits this project's requirements and constraints. Be concrete — name
the actual library or framework, not just the category.

Common layers to cover (include only what's relevant):
- Language / runtime
- Framework (web, API, or otherwise)
- Database / storage
- Authentication
- Frontend (if applicable)
- Testing framework
- Deployment / hosting
- Key third-party integrations

For each choice, include:
- The recommendation
- Why it fits this project specifically (1–2 sentences — not generic praise)
- The main tradeoff or risk to be aware of

**System Design**
- High-level component diagram or description of how the pieces connect
- Data flow: how data moves through the system for the primary use cases
- Key boundaries: what is inside this system vs. what is external

**Folder / Module Structure**
Propose an initial directory structure appropriate for the chosen stack.
Show it as a tree. Annotate key directories with their purpose.

**Testing Approach**
- What will be unit tested vs. integration tested
- Recommended test runner and any key testing libraries
- Where tests will live in the folder structure

---

### Step 4 — Discuss and Decide

After presenting the proposal, ask:
"Does this architecture work for you, or are there things you'd like to change
before I lock it in?"

Be willing to adjust any part of the proposal. If the user wants a different choice,
adopt it without resistance — but flag any meaningful risk or incompatibility if one exists.

Do not proceed to Step 5 until the user explicitly approves the architecture.

---

### Step 5 — Write the Architecture Document

Save the finalized architecture to `/docs/architecture.md` using this structure:

```markdown
# Architecture

**Project**: {project name}
**Date**: {today's date}
**Status**: Decided

---

## Tech Stack

| Layer | Choice | Rationale |
|-------|--------|-----------|
| ...   | ...    | ...       |

## System Design
{Component overview and data flow description}

## Directory Structure
{Annotated folder tree}

## Testing Approach
{What is tested, how, and where tests live}

## Key Dependencies
{List of primary third-party libraries with version pins where relevant}

## Architecture Decisions Log
{Record significant decisions and the reasoning behind them, especially where
alternatives were considered and rejected. This is useful when revisiting decisions later.}
```

---

### Step 6 — Update CLAUDE.md

Write or update `CLAUDE.md` in the project root with the finalized values:
- Project name and description
- Jira project key (ask if not yet established)
- Branch naming convention
- Commit convention
- Test commands (using the chosen test runner)
- Documentation locations
- Any Claude-specific notes about the stack (e.g., "use pnpm not npm",
  "do not modify /src/generated")

---

### Step 7 — Hand Off

Report:
- That `/docs/architecture.md` and `CLAUDE.md` have been written
- Any open architecture questions still unresolved

Then prompt:
"Architecture is locked. Run `/breakdown` next to shape the implementation backlog
and decide your build sequence."
