# /pr — Open a Pull Request

Create a pull request for the current branch targeting the default branch (main).

## Instructions

1. **Confirm the current branch** is not `main` or `master`. If it is, warn the user
   and stop — a PR cannot be opened from the default branch.

2. **Gather context** for the PR description:
   - Current branch name (to extract ticket ID)
   - The approved plan from this session (Summary and Acceptance Criteria sections)
   - The commit log since branching from main:
     ```
     git log main...HEAD --oneline
     ```

3. **Generate the PR description** using this structure:

   ```
   ## Summary
   {One paragraph from the plan summary, adapted to past tense to describe what was done}

   ## Changes
   {Bullet list derived from the commit log — what was actually implemented}

   ## Acceptance Criteria
   {Checklist copied from the plan, checked off based on what was implemented}
   - [x] ...
   - [x] ...

   ## Testing
   {Brief note on how this was tested — unit tests, manual steps, etc.}

   ## Documentation
   {Note any /docs or README changes included in this PR, or "No documentation changes"}

   Closes {TICKET-ID}
   ```

   For **GitHub** issues, the closing line must be `Closes #{number}` (e.g. `Closes #12`)
   so the merged PR auto-closes the linked issue. For Jira/Linear, use the plain ticket
   key as their GitHub integration expects.

4. **Create the pull request** using `gh pr create`. Write the body to a temp file
   and pass it via `--body-file` — do NOT pass multi-line content with `--body "..."`,
   which fails in PowerShell due to heredoc syntax incompatibility.

   ```powershell
   # Write body to a temp file first
   Set-Content -Path "$env:TEMP\pr-body.md" -Value @'
   ## Summary
   ...
   '@ -Encoding utf8

   gh pr create `
     --title "type(TICKET-ID): short description" `
     --base develop `
     --body-file "$env:TEMP\pr-body.md"
   ```

   Options:
   - Base branch: as defined in `CLAUDE.md` (default: `develop`)
   - Head branch: current branch (automatic)
   - Draft: false (ready for review unless user specifies otherwise)

5. **Report back** with:
   - The PR URL
   - PR number
   - Prompt: "PR is open. Run `/close-ticket` to add the implementation summary
     to the ticket and move it to Done."
