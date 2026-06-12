# claude-skills

Personal Claude Code commands, skills, and agents for `islandhopper81`.

---

## Structure

```
claude-skills/
├── commands/   # Slash commands — invoked as /command-name
├── skills/     # Structured skills with reference docs (SKILL.md pattern)
└── agents/     # Custom subagent definitions
```

---

## Commands

| Command | Description |
|---------|-------------|
| `architect` | Tech stack and system design |
| `branch` | Create a git branch for a ticket |
| `breakdown` | Backlog shaping and implementation sequencing |
| `close-ticket` | Add implementation summary and move ticket to Done |
| `commit` | Stage and commit with a conventional commit message |
| `create-ticket` | Create a ticket and transition to In Progress |
| `discover` | Greenfield requirements elicitation |
| `implement-ticket` | Implement a ticket end-to-end |
| `merge` | Merge a pull request |
| `plan` | Feature and task planning |
| `pr` | Open a pull request |
| `refine-ticket` | Refine a ticket for AI agent implementation |
| `scaffold` | Initialize a repository |
| `ship` | **Full cycle**: branch → implement → test → docs → commit → PR → close |
| `ss` | Screenshot visual input |
| `test` | Run tests and summarize results |
| `updatedocs` | Infer and draft documentation updates |

---

## Agents

| Agent | Description |
|-------|-------------|
| `code-simplifier` | Refactor and simplify code — eliminate duplication, reduce verbosity |

> **Note:** `agents/code-simplifier.md` contains a hardcoded memory path
> (`C:\Users\scott\.claude\agent-memory\code-simplifier\`). Update this path
> when installing on a new machine.

---

## Installation

### New machine setup

```bash
git clone https://github.com/islandhopper81/claude-skills.git ~/projects/claude-skills
```

Then copy or symlink the directories into place:

**Option A — Copy (simple):**
```bash
cp ~/projects/claude-skills/commands/* ~/.claude/commands/
cp ~/projects/claude-skills/agents/* ~/.claude/agents/
```

**Option B — Symlink (stays in sync with git pulls):**
```bash
# Back up existing directories first if they exist
ln -s ~/projects/claude-skills/commands ~/.claude/commands
ln -s ~/projects/claude-skills/agents ~/.claude/agents
```

### Keeping up to date

```bash
cd ~/projects/claude-skills && git pull
# If using Option A, re-copy the files after pulling
```

---

## Adding a new command

1. Create `commands/my-command.md` with the instruction content
2. Commit and push
3. Copy to `~/.claude/commands/` (or pull if symlinked)

Commands become available immediately as `/my-command` in any Claude Code session.
