---
name: new-study
description: Initialize a new study folder with the standard structure inside the Obsidian vault. Use when starting a new research study, setting up a study folder, or beginning a new research engagement.
---

# New Study

You are initializing a new study folder inside the Obsidian vault. By the end, the researcher will have a fully structured study folder with placeholder files ready to receive documents and transcripts via `/import`.

## Step 1: Gather Study Information

Ask the user:

1. **Study name** — "What is the name of this study?" (e.g. "Patient Journey Discovery")
2. **Date** — "What month and year is this study? (MM-YYYY)" — default to the current month and year if not specified

Format the folder name: lowercase the study name, replace spaces with hyphens, remove special characters, append `-MM-YYYY`.

Example: "Patient Journey Discovery", 04-2026 → `patient-journey-discovery-04-2026`

Confirm before creating anything:

> "I'll create a study folder called `patient-journey-discovery-04-2026` inside `Client/Studies/`. Does that look right?"

If the user wants to adjust the name, do so before proceeding.

---

## Step 2: Check for Conflicts

Before creating anything, check whether the folder already exists:

```bash
ls ~/AI-Research/Client/Studies/{folder-name} 2>/dev/null && echo "EXISTS" || echo "OK"
```

If it already exists, tell the user and ask whether to proceed anyway or choose a different name.

---

## Step 3: Create the Folder Structure

Use the Obsidian MCP tools to create the following files. Creating each file implicitly creates its parent folders.

**Study README** at `Client/Studies/{folder-name}/README.md`:

```markdown
# {Study Name}

**Created:** {MM-YYYY}
**Status:** In progress

## Folders

- `00-context/` — research plan, interview guide, and other study context documents
- `01-data/` — cleaned interview transcripts and raw data
- `02-analysis/` — coding passes, synthesis working notes, and agent outputs
- `03-outputs/` — draft deliverables and final artifacts
```

**Research plan placeholder** at `Client/Studies/{folder-name}/00-context/research-plan.md`:

```markdown
# Research Plan

*Placeholder. To populate, drag your research plan document into Claude Code and run `/import`.*
```

**Interview guide placeholder** at `Client/Studies/{folder-name}/00-context/interview-guide.md`:

```markdown
# Interview Guide

*Placeholder. To populate, drag your interview guide document into Claude Code and run `/import`.*
```

**Data folder README** at `Client/Studies/{folder-name}/01-data/README.md`:

```markdown
# Data

Cleaned and structured interview transcripts. Populated via `/import`.
```

**Analysis folder README** at `Client/Studies/{folder-name}/02-analysis/README.md`:

```markdown
# Analysis

Qualitative coding passes, thematic analysis, and synthesis working notes. Populated by synthesis agents.
```

**Outputs folder README** at `Client/Studies/{folder-name}/03-outputs/README.md`:

```markdown
# Outputs

Draft and final research deliverables. Populated once synthesis is complete.
```

---

## Step 4: Confirm

Tell the user what was created:

> "Your study folder is ready at `Client/Studies/{folder-name}/`. Here's what's inside:
>
> - `00-context/` — contains placeholder files for your research plan and interview guide. Run `/import` to populate them.
> - `01-data/` — ready for transcripts via `/import`
> - `02-analysis/` and `03-outputs/` — ready for synthesis and deliverables
>
> To add documents to this study, run `/import`."
