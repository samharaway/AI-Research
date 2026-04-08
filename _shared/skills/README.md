# Skills

Symlinks to Claude Code skill files (`~/.claude/skills/*/SKILL.md`).

These files are not stored here — they live in `~/.claude/skills/` where Claude Code can find them. The symlinks make them visible and reviewable inside Obsidian without leaving the vault.

To add a new symlink after creating a skill:
```
ln -s ~/.claude/skills/<skill-name>/SKILL.md ~/AI-Research/_shared/skills/<skill-name>.md
```
