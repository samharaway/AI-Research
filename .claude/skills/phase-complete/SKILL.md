---
name: phase-complete
description: Run this skill when a build phase of the UX Research System is complete. Updates the planning doc, README, commits, and pushes to GitHub. Use when the user says a phase is done, asks to mark a phase complete, or wants to update and commit the research system docs.
---

# Phase Complete

You are wrapping up a completed build phase of the UX Research System. Your job is to update the planning doc and README to reflect what was built, then commit and push everything to GitHub.

## Step 1: Confirm What Was Completed

Ask the user:
1. "Which phase are we marking complete?" (e.g. Phase 2)
2. "What was built or added during this phase?" — ask them to list the key deliverables if not already clear from context

## Step 2: Update the Planning Doc in Obsidian

Update `research-system-plan.md` in the vault:

- In **Current State → Completed**: add the new items from this phase
- In **Current State → In Progress**: update to reflect the next phase
- In **Current State → Next Step**: update to the first task of the next phase
- In **Current State**: update the *Last updated* date to today
- In **Build Phases**: mark the completed phase's checklist items as `[x]`
- Update the **Status** date in the document header

## Step 3: Update the README

Update `README.md` in the vault root to reflect any new capabilities, skills, or instructions that are relevant to colleagues. Specifically:

- If new skills were added: mention them and how to invoke them
- If new methodology docs or templates were added: mention what research methods are now supported
- If the system's capabilities meaningfully changed: update the "What This Is" section
- If prerequisites or setup steps changed: update the Getting Started section

Do not rewrite the README wholesale — only update the parts that changed.

## Step 4: Commit and Push

Verify the correct GitHub account is active:

```bash
gh auth status
```

The active account must be `samharaway`. If it isn't, switch:

```bash
gh auth switch --user samharaway
```

Stage, commit, and push:

```bash
git add research-system-plan.md README.md
git -c user.email="sam.haraway@valtech.com" -c user.name="Sam Haraway" commit -m "Phase [N] complete: [one-line summary of what was built]"
```

Then push using the samharaway token:

```bash
GH_TOKEN=$(gh auth token --hostname github.com --user samharaway 2>/dev/null) && git remote set-url origin https://samharaway:$GH_TOKEN@github.com/samharaway/AI-Research.git && git push
```

## Step 5: Confirm

Tell the user:
- What was updated in the planning doc
- What was updated in the README (or that no README changes were needed)
- The commit message used
- That the changes are live on GitHub
