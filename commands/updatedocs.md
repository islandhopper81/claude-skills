# /updatedocs — Infer and Draft Documentation Updates

Review code changes in the current branch and draft updates to affected documentation.

## Instructions

1. **Identify what changed** by running:
   ```
   git diff {base-branch}...HEAD --name-only
   ```
   Where `{base-branch}` is the default base branch defined in `CLAUDE.md` (e.g., `develop`).
   Fall back to `main` only if CLAUDE.md does not specify a base branch.
   Review the list of changed files to understand the scope of the implementation.

2. **Examine the diff** for changed files to understand what was added, modified, or removed:
   ```
   git diff {base-branch}...HEAD
   ```

3. **Infer which documents are affected** using these rules:
   - Changes to public-facing features, UI, or user-visible behavior → `README.md` likely needs updating
   - Changes to system architecture, data flow, module structure, or major dependencies → `/docs/architecture.md` likely needs updating
   - New API endpoints, functions, or configuration options → relevant `/docs` reference files
   - Changes to setup, installation, or environment requirements → `README.md` setup/installation section
   - Internal refactors with no user-visible change → documentation likely unchanged; flag and confirm

   Cross-reference against the **Documentation Impact** section of the original plan (if available
   in this session) to validate your inference.

4. **Draft the updates** for each affected document:
   - Show the **current content** of the relevant section (brief excerpt)
   - Show the **proposed updated content**
   - Explain in one sentence **why** this update is needed

   Format each proposed change clearly so the user can review and approve or edit it.

5. **Ask for approval** before writing anything to disk:
   "Here are the proposed documentation updates. Reply 'approve' to write them,
   or tell me what to change."

6. **Only after approval**, write the updates to the actual files in the repo.

7. **Confirm** what was written and prompt:
   "Documentation updated. Run `/commit` to stage and commit all changes."

If no documentation changes are needed, say so explicitly and explain why, then
prompt the user to run `/commit` directly.
