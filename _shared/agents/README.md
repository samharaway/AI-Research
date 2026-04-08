# Agents

Symlinks to Claude Code agent files (`~/.claude/agents/*/AGENT.md`).

These files are not stored here — they live in `~/.claude/agents/` where Claude Code can find them. The symlinks make them visible and reviewable inside Obsidian without leaving the vault.

To add a new symlink after creating an agent:
```
ln -s ~/.claude/agents/<agent-name>/AGENT.md ~/AI-Research/_shared/agents/<agent-name>.md
```
