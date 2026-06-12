# /scaffold — Initialize the Repository

Set up the project repository structure based on the approved architecture.
After this command completes, the incremental development workflow begins.

Usage: `/scaffold`

---

## Instructions

### Step 1 — Read Foundation Documents

Read all three:
- `/docs/requirements.md` — project name and constraints
- `/docs/architecture.md` — stack choices and directory structure
- `/docs/breakdown.md` — milestone and ticket structure
- `CLAUDE.md` — project conventions

If any are missing, identify which command generates them and tell the user to run it first.

---

### Step 2 — Assess the Current State

Check the current directory:
```bash
ls -la
git status
```

Determine the starting state:
- **Empty directory, no git**: full initialization needed
- **Empty directory, git already initialized**: skip git init, proceed with structure
- **Existing files present**: pause and ask the user how to proceed —
  do not overwrite existing files without explicit confirmation

---

### Step 3 — Plan the Setup Steps

Based on the architecture, determine what needs to happen. Common steps include:

- `git init` (if needed)
- Framework initialization (e.g., `create-next-app`, `npm init`, `cargo init`)
- Dependency installation
- Directory structure creation
- Config file creation (`.gitignore`, `eslint`, `prettier`, `tsconfig`, etc.)
- Test framework setup
- Environment file setup (`.env.example`)

**Before running any commands that install dependencies or modify the filesystem**,
present the full list of planned steps to the user:

"Here is what I plan to do to initialize this repository:
1. ...
2. ...
3. ...

Shall I proceed?"

Wait for explicit confirmation before running anything.

---

### Step 4 — Execute the Scaffold

After confirmation, run the setup steps in order.

For each step:
- Run the command
- Report success or failure
- If a step fails, stop and report the error — do not skip ahead

**Directory structure**: Create all directories and stub files as defined in
`/docs/architecture.md`. For each stub file, include a brief comment indicating
its purpose but leave implementation empty.

**`.gitignore`**: Generate an appropriate `.gitignore` for the chosen stack.
Include node_modules, build artifacts, `.env`, IDE files, and OS files.

**`.env.example`**: If the project requires environment variables, create a
`.env.example` with all expected keys documented but values empty. Never create
a `.env` file with real values.

**Test setup**: Initialize the test framework and create one passing placeholder
test (e.g., `it('should pass', () => expect(true).toBe(true))`) so the test
command works immediately and CI has something to run.

---

### Step 5 — Verify the Scaffold

After setup, run:
1. The test command from `CLAUDE.md` — confirm the placeholder test passes
2. The app start command (if applicable) — confirm it starts without errors

If either fails, diagnose and fix before proceeding.

---

### Step 6 — Create the Initial Commit

Stage everything and make the initial commit:
```bash
git add -A
git commit -m "chore: initialize project scaffold"
```

Then push to the remote if one is configured:
```bash
git remote -v
git push -u origin main
```

If no remote is configured, note this and suggest:
"No remote configured. Create a repository on GitHub and run:
`git remote add origin {url} && git push -u origin main`"

---

### Step 7 — Update Jira

Transition the first ticket in the breakdown (Milestone 1, Unit 1) to In Progress,
since the repo is now ready for active development.

---

### Step 8 — Hand Off

Report:
- What was created (directory tree, key config files)
- Test command output (confirming placeholder test passes)
- The initial commit hash
- Which Jira ticket is now In Progress

Then prompt:
"Repository is initialized and ready. Start with:
`/plan {first unit name from breakdown}`
This maps to {TICKET-ID} which is now In Progress."
