# Developer Workflows

Shared project-aware skills for Claude and Codex. Source instructions live in skills/<name>/SKILL.md; commands/ and agents/ are Claude compatibility entry points. Edit shared skills, not wrappers.

## Install in Claude Code

After this branch is merged:
```text
/plugin marketplace add islandhopper81/claude-skills
/plugin install developer-workflows@islandhopper-workflows
```
Enable auto-update for islandhopper-workflows in the marketplace settings. Reload plugins or start a new session after an update. Invoke the namespaced skill, for example /developer-workflows:ship FF-123. Avoid installing duplicate copied commands with the same names.

## Install in Codex

After merge:
```sh
codex plugin marketplace add islandhopper81/claude-skills --ref master
codex plugin marketplace upgrade islandhopper-workflows
```
Install the developer-workflows entry through the supported plugin browser. Before merge, use --ref feat/portable-workflow-plugin instead. Availability and update behavior depend on client version and workspace policy. Registering a source is not the same as installing its plugin.

## Cloud and local

Do not assume local installation syncs to every cloud coding environment. Account/workspace plugin support differs. Cloud marketplace distribution may need administrator configuration. Without plugin installation, an assistant can explicitly read a skill from this repository, but it will not automatically appear in its picker.

Screenshots require local filesystem access or attached images. Configure SCREENSHOTS_DIR or supply a directory; no personal Windows path is embedded. Claude agent memory is not shared with Codex.

## Project contract

Read references/project-context.md. Put tracker, Jira project, base branch, worktree setup, checks, schema validation and architecture in each project's AGENTS.md/CLAUDE.md. The plugin reads those files; it does not replace project policy.

Ship requires an implementation plan, acceptance criteria, applicable refined label and resolved blockers. It opens a PR, follows CI during the active task, and leaves the ticket In Progress until merge. Merge/deploy require authorization. Helpers return to ship without repeated testing or next-command pauses.

## Workflows

architect, branch, breakdown, close-ticket, commit, create-ticket, discover, implement-ticket, merge, plan, pr, refine-ticket, scaffold, ship, ss, test, updatedocs, code-simplifier.

## Development and releases

Keep plugin versions aligned in plugin.json, .claude-plugin/plugin.json and .claude-plugin/marketplace.json. Increment versions for published changes, merge them, then update installed clients. GitHub access alone does not install the plugin.

Validate JSON, frontmatter, relative references, wrapper targets and lifecycle rules. Smoke-test actual install/discovery in Claude and Codex, standalone helpers and a disposable ticket workflow. Do not use production tickets to test merge or closure. Platform installation has not yet been smoke-tested for this migration.
