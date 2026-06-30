# /commit — Stage and Commit All Changes with a Conventional Commit Message

Stage all current changes and commit them with an auto-generated conventional commit message.

## Instructions

1. **Check for uncommitted changes**:
   ```
   git status
   ```
   If there is nothing to commit, report that and stop.

2. **Review what will be committed**:
   ```
   git diff --staged --stat
   git diff --stat
   ```
   Understand the scope of changes: code files, documentation, config, etc.

3. **Generate a conventional commit message** based on the changes:

   Format: `{type}({scope}): {short description}`

   Types:
   - `feat` — new feature or user-visible capability
   - `fix` — bug fix
   - `docs` — documentation-only changes
   - `test` — adding or updating tests
   - `chore` — build, config, or tooling changes
   - `refactor` — code restructuring with no behavior change

   Rules:
   - Use lowercase
   - Keep the subject line under 72 characters
   - If both code and docs changed, use the primary type (e.g., `feat`) — the docs
     update is implied when bundled in the same commit
   - Derive `scope` from the module, component, or feature area changed
     (e.g., `feat(meal-planner): add ingredient substitution suggestions`)

   If the changes span multiple unrelated concerns, suggest splitting into multiple
   commits and ask the user how to proceed before committing.

4. **Stage all changes and commit**:

   For single-line messages:
   ```
   git add -A
   git commit -m "type(scope): short description"
   ```

   For multi-line messages on **Windows (PowerShell)**, use a here-string (`@'...'@`).
   Never use bash `<<'EOF'` heredoc syntax — it is not valid PowerShell:
   ```powershell
   git add -A
   git commit -m @'
   type(scope): short description

   Longer body explaining the why.

   Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
   '@
   ```

   On **macOS / Linux (bash)**:
   ```bash
   git add -A
   git commit -m "$(cat <<'EOF'
   type(scope): short description

   Longer body explaining the why.

   Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
   EOF
   )"
   ```

5. **Push the commit** to the remote tracking branch:
   ```
   git push
   ```

6. **Report back** with:
   - The commit hash (short)
   - The commit message used
   - Confirmation the push succeeded
   - Prompt: "Commit pushed. Run `/pr` when ready to open the pull request, or continue
     iterating with `/test` if more changes are needed."
