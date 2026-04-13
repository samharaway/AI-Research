---
name: import
description: Import documents into the research vault. Handles interview transcripts (Teams HTML, DOCX, PDF), research plans, interview guides, and other study context files. Converts to markdown, cleans transcripts, and places files in the correct study folder. Use when someone drags a file or folder into Claude Code and wants to add it to a study.
---

# Import

You are importing one or more documents into the Obsidian research vault. Depending on the document type, you will either run the full transcript cleaning pipeline or the context document pipeline. All files are saved to the correct location within an existing study folder.

## Step 0: Check Prerequisites

Before doing anything else, check whether pandoc is installed — it is required for DOCX conversion:

```bash
pandoc --version 2>/dev/null || echo "NOT FOUND"
```

If not found, install it:

```bash
brew install pandoc
```

Verify:

```bash
pandoc --version | head -1
```

If brew is not available either, note this and fall back to `textutil` for DOCX files (see pipeline notes below).

---

## Step 1: Identify the Files

The user will have dragged one or more files into the Claude Code terminal. Their paths will appear in the conversation. Collect all file paths before proceeding.

If the user dragged a folder rather than individual files, list its contents:

```bash
ls "{folder-path}"
```

Confirm the full list of files with the user before proceeding:

> "I see the following files to import:
> - [list each file]
>
> Is that everything, or do you have more to add?"

---

## Step 2: Identify the Target Study

List available studies:

```bash
ls ~/AI-Research/Client/Studies/
```

Filter out README.md and show only folders. Ask the user:

> "Which study are these files for? Here are your current studies:
> - [list study folders]
>
> Or if this is for a new study, run `/new-study` first, then come back here."

Save the selected study path as `{STUDY_PATH}` (e.g. `~/AI-Research/Client/Studies/patient-journey-04-2026`).

---

## Step 3: Classify Each File

For each file, determine whether it is a **transcript** or a **context document**. Use the following signals:

**Likely a transcript:**
- Filename contains words like "transcript", "recording", "meeting", "interview", "session"
- File is an HTML export from Microsoft Teams
- Content shows a pattern of alternating speaker turns with timestamps

**Likely a context document:**
- Filename contains "research plan", "interview guide", "discussion guide", "brief", "SOW", "protocol"
- File is a PDF or DOCX that reads as a planning or reference document

If classification is unclear, ask the user:

> "Is `{filename}` a transcript or a context document (research plan, interview guide, etc.)?"

---

## Step 4A: Transcript Pipeline

Run this pipeline for each file classified as a transcript.

### 4A-1: Gather Session Metadata

Ask the user:

1. **Participant pseudonym** — "What pseudonym should I use for the participant? (e.g. P01, or a first name like 'Jordan')"
2. **Participant role** — "What is the participant's role or type? (e.g. 'Oncology Nurse', 'Stakeholder – Clinical Operations')"
3. **Interviewer name** — "Who conducted this interview?"
4. **Session date** — "What date was this session? (YYYY-MM-DD)"
5. **Session number** — "Is this session 1, 2, 3...? (used if there are multiple sessions with the same participant)"

Save these as: `{PSEUDONYM}`, `{ROLE}`, `{INTERVIEWER}`, `{DATE}`, `{SESSION_NUM}`.

Output filename will be: `{PSEUDONYM}-session-{SESSION_NUM}.md` (e.g. `p01-session-1.md`).

### 4A-2: Convert to Raw Text

**If HTML (Teams transcript):**
Read the file directly and extract the transcript content. Teams HTML files have a consistent structure: each speaker turn is a block with a speaker name, timestamp, and message text. Extract these in order.

**If DOCX:**
```bash
pandoc "{file-path}" -t markdown --wrap=none -o /tmp/import-raw.md
```

If pandoc is unavailable:
```bash
textutil -convert html "{file-path}" -output /tmp/import-raw.html
```
Then read and parse the HTML output.

**If PDF:**
Read the file directly using the Read tool (provide page ranges for large files).

### 4A-3: Apply Transcript Cleaning Pipeline

Process the raw content through all seven steps in order:

**Step 1 — Collapse timestamp fragments**
Merge lines that belong to the same speaker turn into a single continuous block. Teams transcripts often break a single utterance across multiple timestamped lines. Combine these before any other processing.

**Step 2 — Standardize speaker labels**
Normalize all speaker identifiers to a consistent format:
- The interviewer → `**{INTERVIEWER}:**`
- The participant → `**{PSEUDONYM}:**`
- Any other speakers (observers, co-facilitators) → ask the user how to label them

Remove email addresses, display names with inconsistent capitalization, and "Guest" labels. Every speaker turn must begin with one of the standardized labels.

**Step 3 — Correct transcription errors**
Read the full transcript for words that are clearly wrong — names of drugs, medical terms, company names, or common words that were misheard. Correct these where the intended word is unambiguous from context. Be conservative: only correct when confident. Do not paraphrase or rephrase. Change the minimum necessary.

**Step 4 — Flag low-confidence segments**
Where a word or phrase appears wrong but the correct version is unclear, mark it `[?]` immediately after the uncertain word. Do not guess. Example: `"We use the Veeva [?] system for that."`

**Step 5 — Normalize annotation markers**
Standardize all non-verbal and editorial markers to the following vocabulary:
- `[laughs]` — laughter
- `[pause]` — notable pause
- `[inaudible]` — speech that cannot be made out
- `[crosstalk]` — simultaneous speech
- `[??]` — extended passage that is unclear (more than a word or two)

Remove or consolidate any other notation formats found in the source file.

**Step 6 — Preserve filler words**
Do not remove "um", "uh", "like", "you know", or other filler words. These carry analytical signal about hesitation, uncertainty, and cognitive load.

**Step 7 — Strip preamble and postamble**
Remove system messages at the start and end of Teams transcripts (e.g. "Recording has started", "Recording has stopped", join/leave notifications). If there is pre-interview small talk that the user may want to keep, preserve it but mark the section clearly:

```markdown
---
*[Pre-interview — small talk, not part of the study protocol]*

...

---
```

### 4A-4: Assemble Final Transcript

Structure the cleaned transcript as follows:

```markdown
# {PSEUDONYM} — Session {SESSION_NUM}

**Study:** {Study Name}
**Date:** {DATE}
**Participant:** {PSEUDONYM} ({ROLE})
**Interviewer:** {INTERVIEWER}
**Source file:** {original filename}
**Imported:** {today's date}

---

{cleaned transcript content}
```

### 4A-5: Save to Vault

Use the Obsidian MCP tools to create the file at:

```
{STUDY_PATH}/01-data/{PSEUDONYM}-session-{SESSION_NUM}.md
```

Confirm with the user:

> "Transcript saved to `01-data/{PSEUDONYM}-session-{SESSION_NUM}.md`. Here's a summary of what was cleaned:
> - [N] timestamp-fragmented lines collapsed
> - [N] speaker labels standardized
> - [N] likely transcription errors corrected
> - [N] low-confidence segments flagged with [?]
> - Preamble/postamble: [removed / preserved with marker]"

---

## Step 4B: Context Document Pipeline

Run this pipeline for each file classified as a context document.

### 4B-1: Determine Document Type and Target File

Map the document to its target location:

| Document type | Target file |
|---|---|
| Research plan | `{STUDY_PATH}/00-context/research-plan.md` (replace placeholder) |
| Interview / discussion guide | `{STUDY_PATH}/00-context/interview-guide.md` (replace placeholder) |
| Anything else | `{STUDY_PATH}/00-context/{slugified-filename}.md` (new file) |

If the document type is ambiguous, ask the user.

### 4B-2: Convert to Markdown

**If DOCX:**
```bash
pandoc "{file-path}" -t markdown --wrap=none -o /tmp/import-context.md
```

If pandoc unavailable:
```bash
textutil -convert html "{file-path}" -output /tmp/import-context.html
```
Then read and convert the HTML to clean markdown.

**If PDF:**
Read directly using the Read tool. Reconstruct structure (headings, lists, tables) as markdown where apparent.

**If HTML or web page:**
Read the content and convert to clean markdown, stripping navigation, footers, and non-content elements.

### 4B-3: Clean Up Converted Markdown

After conversion:
- Remove conversion artifacts (excess blank lines, stray characters, garbled table formatting)
- Preserve the document's original heading structure
- Do not rewrite or summarize content — this is a faithful conversion only

### 4B-4: Add Metadata Header

Prepend the following header to the document:

```markdown
# {Document Title}

**Study:** {Study Name}
**Document type:** {Research Plan / Interview Guide / etc.}
**Source file:** {original filename}
**Imported:** {today's date}

---

{document content}
```

### 4B-5: Save to Vault

Use the Obsidian MCP tools to write to the target file.

- If writing to an existing placeholder (`research-plan.md` or `interview-guide.md`): **replace** the placeholder content entirely with the imported content.
- If creating a new file: create it at the target path.

Confirm with the user:

> "Saved to `00-context/{filename}.md`."

---

## Step 5: Batch Confirmation

If multiple files were imported in the same session, provide a summary when all are complete:

> "All files imported:
> - [filename] → [destination]
> - [filename] → [destination]
>
> You can find everything in `Client/Studies/{study-folder}/`."
