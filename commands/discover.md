# /discover — Greenfield Requirements Elicitation

Kick off a new project by eliciting requirements through a structured conversation,
then producing a Product Requirements Document (PRD).

Usage: `/discover` or `/discover {brief project description}`

---

## Instructions

### Step 0 — Calibrate the Interview Format

Before asking any questions, assess the scope of what is being built based on
$ARGUMENTS (if provided) or the user's opening description:

- **Simple** (a script, CLI tool, single-purpose utility, personal tool): Present all
  questions at once as a compact form. Keep it short — 6–8 questions max.
- **Moderate** (a feature-rich app, internal tool, API service): Use a hybrid approach —
  present questions by category, pause for answers per category before moving on.
- **Complex** (multi-user platform, system with integrations, compliance requirements,
  multiple stakeholders): Go conversational — one topic at a time, ask follow-up
  questions based on answers, dig into ambiguities before moving on.

If $ARGUMENTS was provided, use it as the seed description and calibrate immediately.
If no arguments were provided, open with:
"Tell me what you're trying to build in a sentence or two, and I'll take it from there."

---

### Step 1 — Ask the Right Questions

Cover all relevant areas below, skipping anything obviously irrelevant to the project type.
Never ask all questions at once for complex projects — read the answers and adapt.

**Problem & Purpose**
- What problem does this solve, and for whom?
- Who are the primary users? (just you, a small team, external customers)
- What does success look like in 30 days? In 90 days?

**Functionality**
- What are the must-have capabilities at launch?
- What would be nice to have but can wait?
- Are there any key workflows or user journeys you can walk me through?

**Data**
- What data does this create, store, or consume?
- Are there external data sources or APIs to integrate with?
- Any sensitive, regulated, or compliance-relevant data?
  (PHI, PII, HIPAA, CLIA, GDPR, financial data, etc.)

**Constraints**
- Technology preferences or hard constraints? (language, framework, platform)
- Timeline pressure or a specific milestone to hit?
- What environment does this run in? (local, cloud, browser, mobile)
- Existing systems this needs to connect to or coexist with?

**Scale & Operations**
- How many users or records at launch vs. future state?
- Does this need ongoing deployment and maintenance, or is it a one-time build?
- Who maintains it after it's built?

**Quality & Process**
- Are automated tests expected? Any coverage requirements?
- Logging, monitoring, or observability needs?
- Any specific deployment or CI/CD requirements?

---

### Step 2 — Clarify and Confirm

After gathering answers, summarize your understanding back to the user in 3–5 sentences.
Ask: "Does this capture what you're building, or is there anything to correct or add
before I write the PRD?"

Do not proceed to Step 3 until the user explicitly confirms the summary is accurate.

---

### Step 3 — Write the PRD

Produce a structured PRD and save it to `/docs/requirements.md`.
Create `/docs/` if it does not exist yet.

Use this structure:

```markdown
# Product Requirements Document

**Project**: {project name}
**Date**: {today's date}
**Status**: Draft

---

## Problem Statement
What problem this solves and for whom. 1–2 paragraphs.

## Users
Who will use this and in what context.

## Goals
What success looks like. Include measurable outcomes where possible.

## Must-Have Requirements
Numbered list. Written as capabilities, not implementation steps.
1. ...
2. ...

## Nice-to-Have Requirements
Numbered list of lower-priority capabilities.
1. ...

## Out of Scope
What this explicitly does NOT do at launch.

## Constraints
Technical, regulatory, timeline, or operational constraints.

## Open Questions
Anything still unresolved that will need a decision before or during implementation.
```

---

### Step 4 — Hand Off

After saving the PRD, report:
- Where the file was written (`/docs/requirements.md`)
- A count of must-have vs. nice-to-have requirements captured
- Any open questions that need resolution

Then prompt:
"PRD is saved. Run `/architect` next to decide the tech stack and system design,
or review `/docs/requirements.md` and make any edits first."
