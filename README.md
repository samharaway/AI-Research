# UX Research System

An AI-assisted research system for UX practitioners at Valtech. This repo contains everything you need to run structured research projects with Claude Code — from planning through synthesis and deliverables.

---

## What This Is

This system gives you:
- A structured research vault (your notes, transcripts, and artifacts in one place)
- Claude Code connected to your vault so it can read and write research notes
- Skills and agents that support the full research workflow — interview planning, qualitative coding, thematic analysis, and more

---

## Current Capabilities

### `/import` — Import transcripts and context documents

Imports interview transcripts (DOCX, PDF, Teams HTML) or context documents (research plans, interview guides) into the correct study folder.

For transcripts, the pipeline:
- Converts DOCX to markdown via pandoc
- Collapses timestamp-fragmented lines from Teams exports
- Standardizes speaker labels (interviewer and participant pseudonyms)
- Removes interviewer backchannel interjections mid-participant-turn (e.g. "Yeah.", "Mm.", "OK.") — preserves them only at genuine turn boundaries
- Corrects clear transcription errors; flags uncertain terms with `[?]`
- Normalizes annotation markers (`[laughs]`, `[pause]`, `[inaudible]`, `[crosstalk]`, `[??]`)
- Strips system preamble/postamble; preserves pre-interview small talk with a section marker
- Presents a review checkpoint (corrections, flags, questions) before saving — requires explicit approval

### `/new-study` — Create a new study folder

Sets up a new study in `Client/Studies/` with the standard folder structure and placeholder files.

---

---

## Before You Start

You'll need:
- A **Mac** (Windows is not currently supported)
- A **GitHub account** (free at github.com)
- **Claude Code** installed (the terminal app)

If you don't have Claude Code, ask a colleague or check with your team lead.

---

## Getting Started

**Step 1 — Open Terminal**

Press **Cmd+Space**, type **Terminal**, and press **Enter**.

**Step 2 — Install prerequisites**

You'll need Xcode Command Line Tools, Homebrew, and the GitHub CLI. Run each command below and follow any prompts.

Check for Xcode Command Line Tools (required by Homebrew):

```bash
xcode-select --install
```

If a dialog appears asking to install, click **Install**. If the terminal prints "already installed", move on.

Install Homebrew (a Mac package manager):

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

This will ask for your Mac password — the characters won't show as you type, that's normal. If it prints instructions to add Homebrew to your PATH at the end (lines starting with `echo` and `eval`), run those too.

Install the GitHub CLI:

```bash
brew install gh
```

**Step 3 — Clone this repo**

```bash
gh repo clone samharaway/AI-Research
```

**Step 4 — Open Claude Code from the repo folder**

```bash
cd AI-Research
claude
```

**Step 5 — Run the setup guide**

Once Claude Code is open, type:

```
/ux-research-setup
```

Claude will walk you through the rest — installing tools, setting up Obsidian, connecting everything, and getting your first client vault ready. The setup takes about 15–20 minutes.

---

## Already Set Up?

If you've completed setup and are returning to the system, open Claude Code from your vault folder and check the planning doc:

```
open research-system-plan.md
```

Or ask Claude: *"Where did we leave off on the research system?"*

---

## Questions or Issues

Reach out to Sam Haraway on Slack or open an issue in this repo.
