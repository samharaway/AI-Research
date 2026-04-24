---
name: analyze-stakeholder-v2
description: "Deductive analysis of stakeholder interview transcripts, with support for multi-participant interviews. Extracts each stakeholder's perspective against configurable dimensions (goals, success metrics, scope, timelines, risks, etc.), captures cross-dialogue dynamics when multiple stakeholders are co-interviewed, produces a cross-stakeholder alignment matrix, and synthesizes a consolidated stakeholder brief. Use after transcripts have been imported via /import and are ready for analysis. Triggers: analyze stakeholder interviews, stakeholder analysis, alignment matrix, stakeholder brief, who said what, where do stakeholders agree, stakeholder synthesis."
---

# Analyze Stakeholder (V2)

You are conducting deductive analysis of stakeholder interview transcripts within a study in the Obsidian research vault. This skill extracts each stakeholder's perspective against researcher-confirmed dimensions, identifies alignment and divergence across stakeholders, and produces a consolidated brief for the project team.

This version supports **multi-participant interviews** — transcripts where more than one stakeholder was present. When multi-participant transcripts exist, the skill captures interaction dynamics between co-interviewed stakeholders alongside individual perspective extraction. When all transcripts are 1-1, the multi-participant logic is inert.

**Input:** Cleaned transcripts in a study's `01-data/` folder (produced by `/import`).
**Output:** Extraction table, alignment matrix, and stakeholder brief in `02-analysis/`.

**When to use this skill vs. other analysis skills:** This skill is designed for stakeholder interviews — conversations with people who have a vested interest in a project, conducted to understand goals, constraints, success criteria, and risks. The analytic approach is deductive: dimensions are defined before analysis begins, and the skill maps each stakeholder's perspective against those dimensions. If the research goal is exploratory or generative — understanding user behaviors, discovering unmet needs, identifying emergent patterns — a different analytic approach (inductive coding and thematic analysis) is more appropriate.

---

## Analytic Principles

These principles are standing instructions. Apply them throughout every pass.

### Perspectives are reported, not adjudicated

The skill documents what stakeholders said. It does not determine who is "right." When stakeholders disagree, the analysis presents each perspective with its evidence. Resolution is for the project team, not the analyst.

- Frame divergence as different perspectives, not as one side being correct
- Resist the pull of the most senior or most articulate stakeholder — seniority does not make a perspective more valid for the purposes of analysis
- The researcher's review evaluates your framing choices, not just your extraction accuracy

### Gaps are data

A stakeholder not addressing a dimension is analytically meaningful. A dimension with only one perspective is worth noting. Do not infer, fill from context, or treat silence as agreement.

- Record `[Not addressed]` explicitly — do not skip
- In the alignment matrix, gaps are a first-class finding alongside consensus and divergence
- A gap may indicate assumption, irrelevance, avoidance, or simply that the interview didn't cover it — note the ambiguity

### Context before compression

When extracting discrete observations from flowing conversation, context is necessarily lost. Be aware of the cost:

- Silence, hesitation, and tone shifts are not captured in transcript text but may signal significance
- A qualifier after a strong statement changes the meaning — if the observation is split from the qualifier, meaning shifts
- Power dynamics (deference to leadership, political caution) may cause stakeholders to soften or amplify their actual position
- Organizational subtext (who is in the room, recent events) shapes what stakeholders say

When compression risks distorting meaning, note the risk in the analytic memo. The researcher decides whether the compression is acceptable.

### Edge and variance scan

At every stage, actively check: whose perspective is absent, and whose position would complicate the picture?

- Which roles or departments are not represented in the stakeholder set? Note the absence.
- If all stakeholders hold similar views, assess whether this reflects genuine consensus or groupthink / political alignment
- A minority perspective from a single stakeholder is not noise — it may reflect ground-level knowledge that leadership lacks

### Care check

Before finalizing the stakeholder brief, consider consequences:

- Could a stakeholder be identified despite role-level attribution? (Small teams make anonymity harder)
- Could the framing of a misalignment damage a working relationship if the brief is shared widely?
- Are any recommendations presented with more authority than the evidence warrants?

---

## Quotation Integrity — Hard Rule

**All quotations must be verbatim passages copied directly from source transcripts. This is non-negotiable.**

- Never invent, paraphrase, summarize, adjust, or clean up a quotation
- Every word, punctuation mark, and formatting element must match the source transcript exactly
- Truncated quotes must mark the omission with `[...]`
- Every quotation must include stakeholder identifier and turn/line reference so the researcher can verify it
- If the researcher looks up a quoted passage in the transcript, there must be a **100% match**

Quotation fabrication is a critical failure. Treat it as such in every pass.

---

## Review Checkpoint Format

All review checkpoints in this skill use a **terminal-based review** presented directly in the conversation. The review has two parts (three in Pass 1, when emergent items exist):

### Part A: At a Glance

A structured summary that gives the researcher the shape of the output without opening any files. The specific content varies by pass but the purpose is the same: quick comprehension of the analytical landscape.

### Part B: Substantive Issues for Researcher Input

Instead of listing every decision as a separate question, condense decisions into substantive issues, each presented as a mini-brief:

- State the issue in terms of the analytical choice being made
- Provide the actual evidence inline (stakeholder quote, what the analysis found)
- Explain why it matters for the analysis
- Offer a recommendation

Group related decisions under one issue when they involve the same type of judgment.

Aim for 2-4 substantive issues by grouping related decisions. **If genuinely independent issues exceed this range after grouping, present all of them.** Do not suppress issues to fit the target range. Analytical completeness takes priority over formatting.

### Part C: Emergent Items (Pass 1 only, when emergent items exist)

See Pass 1 for details on the emergent item review and promotion flow.

### What the terminal review does NOT replace

- The detailed output files are still written — they are the analytical backbone and evidence trail
- The researcher can still open them if something in the summary looks off
- The researcher's decisions produce the authoritative artifacts for the next pass

---

## Setup: Study Context & Dimension Configuration

Before any analysis, establish the study context and confirm the analytic dimensions with the researcher.

### Step 1: Identify the study

List available studies:

```bash
ls ~/AI-Research/Client/Studies/
```

Ask the researcher which study to analyze. Save the path as `{STUDY_PATH}`.

### Step 2: Read available context

Read from two levels:

**Project context** — Read all markdown files in `~/AI-Research/Client/_context/`:
```bash
ls ~/AI-Research/Client/_context/
```

**Study context** — Read all files in `{STUDY_PATH}/00-context/`:
```bash
ls {STUDY_PATH}/00-context/
```

Read the research plan and interview guide. Extract:
- Research objectives
- Interview guide questions and topic areas
- Business goals or project framing (from SOW, project brief, or research plan)
- Any references to specific concerns, constraints, regulations, or technical systems

### Step 3: Read transcripts & extract stakeholder metadata

List all transcripts in `{STUDY_PATH}/01-data/` (exclude README.md):
```bash
ls {STUDY_PATH}/01-data/
```

Read the header and opening of each transcript to extract:
- Stakeholder identifier (name or ID per study convention)
- Role / title
- Department or team (if stated)
- Relationship to project (decision-maker, subject matter expert, end user of system, etc.)

For each transcript, also identify all speakers and their roles:
- **Facilitators** — researchers asking questions
- **Observers** — researchers or team members present but not leading
- **Participants** — stakeholders being interviewed

**Count the participants per transcript.** Any transcript with more than one participant is a **multi-participant transcript**.

### Step 4: Confirm with the researcher

Present findings and confirm using AskUserQuestion:

**Question 1 — Research objectives:**
> "I found the following research objectives in your research plan:
> - [list each objective]
>
> Are these correct, or would you like to edit or add to them?"

**Question 2 — Transcripts and speaker roles:**

If all transcripts are 1-1 (single participant):

> "I found the following stakeholders to analyze:
> - [list each with identifier, role, department, and relationship to project]
>
> Should I include all of them, or exclude any?"

If any multi-participant transcripts exist:

> "I found the following transcripts and speaker roles:
>
> **1-1 interviews:**
> - {Transcript}: {Stakeholder} ({role, department, relationship})
> - ...
>
> **Multi-participant interviews:**
> - {Transcript}: Participants: {Stakeholder A} ({role}), {Stakeholder B} ({role})
>   Facilitator(s): {name(s)}; Observer(s): {name(s) or "none"}
> - ...
>
> I've identified {N} multi-participant transcript(s). For these, I'll extract individual perspectives per stakeholder and also capture interaction dynamics between co-interviewed stakeholders (agreement, disagreement, deference, position shifts).
>
> Should I include all transcripts, or exclude any? And are the speaker roles correct?"

Document any excluded transcripts and the reason.

**Question 3 — Dimension configuration:**

Scan the study context documents (research plan, interview guide, project context) for signals of relevant dimensions beyond the defaults. Present:

> **Default dimensions** (included unless you remove them):
> 1. Goals
> 2. Success metrics
> 3. Completion criteria
> 4. Scope
> 5. Timelines
> 6. Risks & concerns
>
> **Suggested additional dimensions** (detected from study context):
> - {Dimension name} — {brief rationale: what in the context documents suggested this}
> - {Dimension name} — {brief rationale}
> - ...
>
> Common additional dimensions for stakeholder analysis include:
> - Technical constraints
> - Regulatory considerations
> - Organizational change / workflow impact
> - User / audience definition
> - Resource requirements
> - Communication & engagement preferences
>
> **Which dimensions would you like to include?** You can accept the defaults, add any of the suggested dimensions, remove defaults, or add custom dimensions not listed here.

The researcher's response sets the confirmed dimension list.

### Step 5: Save configuration

Present the confirmed configuration for final approval:

```markdown
# Analysis Configuration

**Study:** {study name}
**Date started:** {today's date}
**Analysis type:** Stakeholder analysis (deductive)

## Research Objectives
{confirmed objectives}

## Stakeholders Included
| Identifier | Role | Department | Relationship to Project | Interview Format |
|---|---|---|---|---|
| {ID} | {role} | {dept} | {relationship} | 1-1 / Co-interviewed with {other stakeholder(s)} |

## Stakeholders Excluded
{list with reason, or "None"}

## Interview Format Summary
- 1-1 interviews: {N}
- Multi-participant interviews: {N} (involving {N} stakeholders total)
{If multi-participant: "Interaction extraction enabled for multi-participant transcripts."}

## Confirmed Dimensions
1. {Dimension name}
2. {Dimension name}
3. ...

## Business Context
{summary of business goals from project and study context}
```

Save to `{STUDY_PATH}/02-analysis/analysis-config.md`.

Confirm with the researcher:
> "Setup complete. I've saved the analysis configuration. Ready to begin Pass 1 (Perspective Extraction). Proceed?"

**Stop and wait for researcher approval before proceeding.**

---

## Pass 1: Perspective Extraction (parallel agents)

**Goal:** For each transcript, extract what each stakeholder said about each confirmed dimension. One agent per transcript, run in parallel. For multi-participant transcripts, agents also capture interaction dynamics between co-interviewed stakeholders.

### What is an extraction

An extraction is a discrete observation about what a stakeholder said regarding a specific dimension. Each extraction captures **one perspective on one dimension**. If a stakeholder made multiple distinct points about the same dimension (e.g., short-term goals vs. long-term goals), produce multiple extractions.

### Per extraction, record

| Field | Content |
|---|---|
| ID | `E-{NNN}` — unique across all transcripts (see Launching agents below) |
| Stakeholder | Stakeholder identifier |
| Dimension | Which confirmed dimension this maps to, or `EMERGENT: {brief label}` |
| Observation | 1-2 sentence summary of what the stakeholder expressed |
| Quote | Verbatim supporting quote from the transcript |
| Location | Stakeholder identifier + turn number or line range in source transcript |
| Valence | Stakeholder's stance on this point: **positive**, **neutral**, **concerned**, or **conflicted** |
| Constrained | *(Multi-participant transcripts only)* `Yes` or omit. Flag `Yes` when the response appeared influenced by another stakeholder's presence — e.g., hedging after a senior stakeholder spoke, explicitly deferring ("I agree with what [name] said"), softening a position, or visibly changing stance. Add a brief note on the observed constraint. |

### Interaction extractions (multi-participant transcripts only)

When multiple stakeholders are co-interviewed, cross-dialogue produces a distinct type of data. **Interaction extractions** capture dynamics between stakeholders that would not exist in 1-1 interviews.

Record interaction extractions in a **separate table** from individual extractions.

| Field | Content |
|---|---|
| ID | `IX-{NNN}` — separate numbering sequence from individual extractions |
| Stakeholders | All stakeholder identifiers involved in the interaction |
| Dimension | Which confirmed dimension this relates to, or `EMERGENT: {brief label}` |
| Interaction type | **Agreement** (explicit endorsement of another's position), **Disagreement** (explicit challenge or contradiction), **Deference** (yielding to another's position, often with a power dynamic), **Negotiation** (real-time working toward a shared position), **Position shift** (one stakeholder visibly changing stance during the exchange), **Elaboration** (one stakeholder building substantively on another's point) |
| Description | 2-3 sentence summary of what happened in the interaction — who said what, how the other responded, and what it reveals |
| Quote | Verbatim exchange from the transcript (include both/all speakers' contributions) |
| Location | Turn numbers or line range covering the interaction |

**When to capture an interaction extraction:**

- Two or more stakeholders directly engage on a topic (not just sequential answers to the same question)
- A stakeholder explicitly references or responds to what another stakeholder said
- Observable dynamics — deference, disagreement, real-time negotiation — that reveal something about the relationship between perspectives
- A stakeholder's position visibly shifts in response to another's statement

**When NOT to capture an interaction extraction:**

- Stakeholders give independent, sequential answers to the same question without referencing each other — these are individual extractions, not interactions
- Phatic agreement ("Yeah," "Right," "Mm-hmm") without substantive endorsement
- Administrative coordination between participants

### Emergent items

Significant statements that do not map to any confirmed dimension are captured with the same extraction structure, but with the Dimension field set to `EMERGENT: {brief label}`. These surface during the review checkpoint for possible promotion.

Examples of emergent items:
- Political dynamics between departments that affect the project
- Organizational context not captured by any dimension (recent leadership changes, prior failed initiatives)
- Workflow concerns that fall outside the configured dimensions
- Stakeholder expectations about their own involvement that don't fit "Communication preferences" or similar

Interaction extractions can also be emergent — cross-dialogue may surface dynamics that don't map to any dimension.

### Additional per-transcript outputs

Beyond the extraction tables, each agent also records:

- **Stakeholder disposition:** One-line summary of each stakeholder's overall posture toward the project (e.g., "Strongly supportive but concerned about timeline," "Skeptical of scope, wants phased approach," "Engaged but deferring decisions to VP")
- **Referrals:** Anyone a stakeholder recommended speaking with (the "who else should I talk to?" data)
- *(Multi-participant transcripts only)* **Group dynamic summary:** 2-3 sentences on the overall dynamic between co-interviewed stakeholders — who dominated, who deferred, whether the conversation felt collaborative or hierarchical, and any notable moments where the group setting visibly shaped individual responses

### Rules

- **Verbatim quotation integrity** — all quotes must be exact matches to source transcript with location references. See the Quotation Integrity section.
- **Dimension gaps are data.** If a stakeholder did not address a confirmed dimension, the agent records it explicitly as `[Not addressed]` for that dimension. Do not infer or fill from context. The gap is analytically meaningful and will appear in the Pass 2 alignment matrix.
- **Multiple extractions per dimension allowed.** A stakeholder may express several distinct perspectives on a single dimension. Each gets its own extraction row.
- **No interpretation at this stage.** Observations summarize what was said, not what it means for the project. Interpretation happens in Pass 3.
- **Valence assignment should be conservative.** If the stakeholder's stance is genuinely ambiguous, mark `conflicted` rather than forcing a direction.
- **Constrained response flags require evidence.** Do not flag an extraction as constrained based on speculation about power dynamics. Flag only when the transcript shows observable evidence: hedging language, explicit deference, a visible change in position, or contradiction between an early independent statement and a later response given after hearing a senior stakeholder.
- **Skip administrative passages** (greetings, consent language, scheduling). Note skipped sections in the memo if they contained relevant asides.

### Launching parallel agents

Launch one agent per transcript using the Agent tool.

**Pre-assign extraction ID ranges** to avoid numbering collisions. Estimate ~15-25 individual extractions per transcript for 1-1 interviews, ~20-35 for multi-participant transcripts (more speakers, more extractions). Allocate ranges with buffer:
- Agent 1 (transcript 1): E-001 through E-030
- Agent 2 (transcript 2): E-031 through E-060
- Agent 3 (transcript 3): E-061 through E-090
- (Adjust ranges based on transcript length, participant count, and transcript count)

For interaction extractions, assign separate ranges:
- Agent handling multi-participant transcript 1: IX-001 through IX-020
- Agent handling multi-participant transcript 2: IX-021 through IX-040
- (Only agents handling multi-participant transcripts receive IX ranges)

If an agent exceeds its range, it continues numbering beyond the range — the coordination step handles renumbering.

Each agent receives:
- One transcript
- The analysis configuration (research objectives, confirmed dimensions, stakeholder metadata)
- These extraction instructions (extraction structure, emergent item handling, rules, quotation integrity)
- Its assigned extraction ID range(s)
- Whether the transcript is 1-1 or multi-participant (if multi-participant, include the interaction extraction instructions)

**Agent prompt template (1-1 transcript):**

> You are Agent {N} extracting stakeholder perspectives from an interview transcript. Read the transcript and analysis configuration provided below. For each confirmed dimension, extract what this stakeholder said — their perspective, with a verbatim supporting quote and location reference. If the stakeholder did not address a dimension, record `[Not addressed]`. For significant statements that don't map to any confirmed dimension, capture them as EMERGENT items with a brief label. Also record the stakeholder's overall disposition toward the project and any referrals they made.
>
> Apply the Quotation Integrity rule: copy supporting quotes exactly as they appear in the transcript. Use extraction IDs starting at E-{start} through E-{end}.
>
> Produce:
> 1. An extraction table with all extractions for this stakeholder
> 2. A stakeholder disposition summary (one line)
> 3. Referrals (if any)
> 4. An analytic memo documenting extraction decisions, ambiguous dimension assignments, and any context lost in compression
>
> {Include: transcript content, analysis config content, extraction ID range}

**Agent prompt template (multi-participant transcript):**

> You are Agent {N} extracting stakeholder perspectives from a multi-participant interview transcript. This transcript includes {M} stakeholders: {list with roles}. Facilitator(s): {names}. Observer(s): {names or "none"}.
>
> Read the transcript and analysis configuration provided below. For each confirmed dimension, extract what each individual stakeholder said — their perspective, with a verbatim supporting quote and location reference. Attribute every extraction to a specific stakeholder. If a stakeholder did not address a dimension, record `[Not addressed]` for that stakeholder. For significant statements that don't map to any confirmed dimension, capture them as EMERGENT items.
>
> **Constrained response flagging:** When a stakeholder's response appears influenced by another stakeholder's presence — hedging after a senior stakeholder spoke, explicitly deferring, softening a position, or visibly changing stance — flag the extraction as `Constrained: Yes` with a brief note on the observed evidence. Only flag when the transcript shows observable evidence, not based on speculation about power dynamics.
>
> **Interaction extractions:** In addition to individual extractions, capture cross-dialogue dynamics in a separate interaction extraction table. When two or more stakeholders directly engage on a topic — agreement, disagreement, deference, negotiation, position shifts, or substantive elaboration — record the interaction with both speakers' identifiers, the relevant dimension, a description of the dynamic, and a verbatim quote of the exchange. Do not capture phatic agreement or administrative coordination.
>
> Apply the Quotation Integrity rule: copy supporting quotes exactly as they appear in the transcript. Use individual extraction IDs starting at E-{start} through E-{end}. Use interaction extraction IDs starting at IX-{start} through IX-{end}.
>
> Produce:
> 1. An individual extraction table with extractions for each stakeholder (including Constrained column)
> 2. An interaction extraction table
> 3. A stakeholder disposition summary for each stakeholder (one line each)
> 4. A group dynamic summary (2-3 sentences on overall dynamic between co-interviewed stakeholders)
> 5. Referrals (if any)
> 6. An analytic memo documenting extraction decisions, ambiguous dimension assignments, constrained response flag rationale, interaction extraction decisions, and any context lost in compression
>
> {Include: transcript content, analysis config content, extraction ID ranges}

### Coordination step

After all agents complete:

1. Collect all individual extractions from all agents
2. Renumber individual extractions sequentially if any agent used fewer IDs than allocated (to close gaps)
3. Assemble the combined individual extraction file with all extractions in a single table, grouped by stakeholder, then by dimension within each stakeholder
4. If multi-participant transcripts exist: collect all interaction extractions, renumber IX IDs sequentially, and assemble the interaction extraction table grouped by transcript then by dimension
5. Compile stakeholder dispositions and referrals into a summary section
6. If multi-participant transcripts exist: compile group dynamic summaries
7. Merge agent memos into a single Pass 1 memo
8. Add summary counts: total individual extractions, extractions per stakeholder, extractions per dimension, emergent items count, constrained extractions count, total interaction extractions (if applicable)

### Output

Save the following files to `{STUDY_PATH}/02-analysis/`:

1. **Combined extraction file:** `pass-1_extractions_combined.md`
   - All individual extractions across all transcripts in a single table, grouped by stakeholder
   - *(If multi-participant transcripts exist)* Interaction extractions table, grouped by transcript then by dimension
   - Stakeholder dispositions summary
   - *(If multi-participant transcripts exist)* Group dynamic summaries
   - Referrals summary
   - Summary counts: total individual extractions, per stakeholder, per dimension, emergent items, constrained extractions (if any), interaction extractions (if any)

2. **Analytic memo:** `pass-1_memo.md`
   - Extraction decisions and ambiguous dimension assignments
   - Passages where context was lost in compression (per *Context before compression* principle)
   - Notable gaps — stakeholders who were thin on key dimensions
   - Emergent items flagged with rationale for why they didn't fit a dimension
   - *(If multi-participant transcripts exist)* Constrained response flag rationale — what evidence supported each flag
   - *(If multi-participant transcripts exist)* Interaction extraction decisions — what was captured and what was judged as below the threshold

### Review checkpoint

Present to the researcher using the terminal-based review format:

**Part A: Extraction Coverage**

Present a **stakeholder x dimension coverage matrix:**

```
                         | Goals | Metrics | Completion | Scope | Timeline | Risks | {custom} | Emergent
Stakeholder A (VP Prod)  |   3   |    2    |     1      |   2   |    1     |   3   |    1     |    1
Stakeholder B (PM)       |   2   |    1    |     -      |   3   |    2     |   1   |    -     |    2
Stakeholder C (Eng Lead) |   1   |    -    |     -      |   2   |    1     |   2   |    1     |    -
```

Numbers = extraction count per cell. `-` = not addressed.

Below the matrix:
- Total individual extractions: {N}
- Stakeholder with richest coverage: {name} ({N} extractions)
- Stakeholder with thinnest coverage: {name} ({N} extractions)
- Dimensions with universal coverage (addressed by all stakeholders): {list}
- Dimensions with gaps: {dimension} — not addressed by {stakeholder(s)}
- Emergent items: {count} items across {N} stakeholders

*(If multi-participant transcripts exist, add:)*
- Constrained extractions: {N} across {N} stakeholder(s) — {brief summary, e.g., "mostly deference to VP in group interview 2"}
- Interaction extractions: {N} across {N} multi-participant transcript(s)
- Interaction types: {breakdown, e.g., "4 agreement, 2 disagreement, 1 deference, 1 negotiation"}

**Stakeholder dispositions:**
- {Stakeholder A}: {one-line disposition}
- {Stakeholder B}: {one-line disposition}
- ...

*(If multi-participant transcripts exist, add:)*

**Group dynamics:**
- {Transcript name}: {2-3 sentence group dynamic summary}
- ...

**Part B: Substantive Issues**

2-4 issues, each structured as a mini-brief with evidence, stakes, and recommendation. Typical Part B issues for Pass 1:

- **Ambiguous dimension assignments** — a statement could map to two dimensions. Show the quote, which dimension it was assigned to, and why.
- **Thin transcripts** — a stakeholder gave minimal input on most dimensions. Assess whether this reflects the interview's scope or possible underextraction.
- **Valence calls** — cases where the stakeholder's stance was genuinely hard to read. Show the evidence and the assigned valence.
- **Compression risks** — passages where splitting or summarizing may have lost important nuance. Include the relevant quote and explain the risk.
- *(If multi-participant transcripts exist)* **Constrained response judgment calls** — cases where flagging a response as constrained was a close call. Show the evidence and explain the reasoning. The researcher decides whether the flag stands.
- *(If multi-participant transcripts exist)* **Group dynamic influence on coverage** — if a stakeholder was notably thinner in a group setting than expected for their role, flag whether the group dynamic may have suppressed their input.

**Part C: Emergent Items** (present only if emergent items exist)

List each emergent item:

```
**Emergent items ({N} total):**

1. **{Brief label}** — {Stakeholder} ({role})
   > "{verbatim quote}" — {location}
   {1-sentence context for why this was captured}

2. **{Brief label}** — {Stakeholder} ({role})
   > "{verbatim quote}" — {location}
   {1-sentence context}
```

Group emergent items that appear related across stakeholders. Emergent interaction extractions are listed with both stakeholders' identifiers and the interaction type.

Then ask:

> "Would you like to promote any emergent items to a full dimension?"

For each item the researcher promotes, present the choice:

> **{Promoted dimension name}** — {N} extractions already captured across {N} stakeholders.
>
> You can:
> - **Proceed with existing extractions.** The captured items will be reclassified under the new dimension and carried into Pass 2. This is typically sufficient when the emergent items already represent the substance of what stakeholders said.
> - **Re-extract for this dimension.** The agent will re-read all transcripts specifically looking for additional statements related to this dimension. This adds a targeted extraction pass and is worth doing if you believe other stakeholders likely discussed this topic but it wasn't flagged as emergent.

If the researcher chooses to proceed with existing extractions:
- Reclassify the promoted emergent extractions under the new dimension name
- Update the extraction table and coverage matrix
- Add the new dimension to the analysis configuration

If the researcher chooses re-extraction:
- Launch agents to re-read each transcript, searching specifically for statements related to the promoted dimension
- New extractions are added to the combined file with IDs continuing from the existing sequence
- Update the extraction table, coverage matrix, and analysis configuration
- Present a brief update on what the re-extraction found (new extractions count, which stakeholders had additional relevant material)

After the emergent item review is complete:

> "Output files are in `02-analysis/`. When you're satisfied with the extractions, say 'proceed to Pass 2' and I'll begin the alignment analysis."

**Stop and wait for researcher approval before proceeding.**

---

## Pass 2: Cross-Stakeholder Alignment Analysis (single agent)

**Goal:** Analyze the full extraction set to identify where stakeholders agree, diverge, and are silent — per dimension. Produce the alignment matrix as a primary deliverable artifact. When multi-participant data exists, incorporate interaction extractions and flag co-interview provenance.

### What is the alignment matrix

The alignment matrix is the central analytical output of stakeholder analysis. For each confirmed dimension, it answers: do stakeholders share a perspective, and if not, how do they differ? Unlike inductive thematic analysis, the categories are known before analysis begins — the analytical work is in mapping perspectives and detecting alignment patterns.

The alignment matrix is a **primary deliverable** of this skill — it is designed to be directly usable by the project team, not just an intermediate analysis artifact.

### Execution

Single agent. Reads `analysis-config.md` and `pass-1_extractions_combined.md`.

### Per dimension, produce

```markdown
### {Dimension Name}

**Alignment status:** {Consensus / Partial alignment / Divergent / Insufficient data}

**Shared perspective:** {What most stakeholders agree on — 1-3 sentences. Present only if alignment status is Consensus or Partial alignment.}

**Divergent perspectives:** {Where stakeholders disagree. Each divergent position stated with role-level attribution and verbatim quote. Present only if alignment status is Partial alignment or Divergent.}
- {Role A} perspective: "{verbatim quote}" — {stakeholder ID}, {location}
  {1-sentence summary of this position}
- {Role B} perspective: "{verbatim quote}" — {stakeholder ID}, {location}
  {1-sentence summary of this position}

**Gaps:** {Which stakeholders did not address this dimension. Note whether the gap likely reflects assumption, irrelevance, or avoidance — or if the reason is ambiguous.}

**Hidden assumptions:** {Anything a stakeholder stated as self-evident that others contradicted or didn't mention. These are often the most actionable findings — they reveal where the team thinks they agree but may not.}

**Key quotes:** {2-4 quotes that best capture the range of perspectives. Include consensus quotes and the sharpest divergence quotes.}
> "{quote}" — {role}, {stakeholder ID}, {location}
> "{quote}" — {role}, {stakeholder ID}, {location}
```

*(If multi-participant transcripts exist, add per dimension where relevant:)*

```markdown
**Methodological note:** {Present only when co-interviewed stakeholders contributed to this dimension. Note which stakeholders were co-interviewed, reference any constrained response flags (by extraction ID), and note any interaction extractions that bear on the alignment assessment. Keep it factual and brief.}
```

Example:
```
**Methodological note:** Stakeholders A and B were co-interviewed. Their apparent
consensus on this dimension included one constrained response (E-045) where B
deferred to A's position. Independent verification of B's view is not available.
Interaction IX-008 shows B initially expressing a different timeline before
aligning with A's stated preference.
```

*(If interaction extractions relate to this dimension, add:)*

```markdown
**Interaction evidence:** {Summarize relevant interaction extractions. These add a layer of evidence not available from 1-1 interviews — real-time agreement, disagreement, negotiation, or deference observed between stakeholders.}
- IX-{NNN}: {Interaction type} between {Role A} and {Role B} — {1-sentence summary}
```

### Alignment status definitions

- **Consensus**: All stakeholders who addressed this dimension expressed compatible perspectives. Compatible does not mean identical — different emphases are fine if they don't conflict.
- **Partial alignment**: A majority perspective exists, but one or more stakeholders hold a materially different view.
- **Divergent**: No clear majority perspective. Stakeholders hold two or more incompatible positions.
- **Insufficient data**: Fewer than two stakeholders addressed this dimension meaningfully.

### Cross-cutting analysis

After all dimensions are analyzed, produce a cross-cutting section:

**Stakeholder clusters:**
Do any stakeholders consistently align with each other across dimensions? (e.g., "VP Product and PM are aligned on 5/6 dimensions; Engineering Lead diverges on scope and timeline"). Clusters may reflect organizational alliances, shared context, or similar roles.

*(If multi-participant transcripts exist, add:)* Note which clusters include co-interviewed stakeholders, where alignment may reflect the group setting rather than independent agreement.

**Tension patterns:**
Are there dimensions where divergence on one creates downstream problems for another? (e.g., "Scope divergence makes timeline consensus fragile — if the team adds the admin portal that Stakeholder B wants, Stakeholder A's Q3 deadline is not achievable")

**Highest-risk misalignments:**
Which divergences, if unresolved, pose the greatest risk to project success? Rank by combination of:
- How fundamental the dimension is (goals and scope divergence is more dangerous than timeline disagreement)
- How wide the gap between perspectives
- How much organizational authority sits on each side

*(If multi-participant transcripts exist, add a cross-cutting section:)*

**Co-interview dynamics summary:**
Synthesize the group dynamic summaries and interaction extractions into an overall assessment:
- Which multi-participant interviews showed collaborative dynamics vs. hierarchical dynamics?
- Were there dimensions where co-interview dynamics notably shaped the data? (e.g., "Scope alignment between A and B may be overstated — interaction IX-012 shows B deferring to A's position rather than independently agreeing")
- What would be worth verifying in follow-up 1-1 conversations?

### Rules

- **Present misalignment neutrally.** No stakeholder's view is "right." The matrix documents what was said, not who is correct.
- **Distinguish language differences from substance differences.** Two stakeholders using different words for the same idea is not divergence. Two stakeholders using the same word to mean different things IS divergence — and particularly worth flagging.
- **Quote, don't paraphrase.** Alignment claims must be grounded in extraction evidence. Every shared/divergent perspective statement must reference specific extraction IDs.
- **Gaps get scrutiny.** If a key decision-maker didn't address a core dimension, call it out explicitly — it may indicate assumption rather than oversight.
- **Role-level attribution.** In the alignment matrix, attribute perspectives by role (e.g., "VP Product," "Engineering Lead"), not by name. This follows standard stakeholder reporting practice and encourages candor in future interviews.
- **Interaction evidence supplements, not overrides.** Interaction extractions add a layer of evidence to the alignment assessment. They do not replace or override individual extractions. An interaction showing real-time agreement is corroborating evidence for consensus; an interaction showing deference is a caveat on apparent consensus — but in both cases, the individual extractions remain the primary unit of analysis.

### Output

Save the following files to `{STUDY_PATH}/02-analysis/`:

1. **Alignment matrix:** `pass-2_alignment-matrix.md`
   **This is a primary deliverable.** Full alignment analysis: per-dimension sections with alignment status, shared/divergent perspectives, gaps, hidden assumptions, key quotes, and (where applicable) methodological notes and interaction evidence. Cross-cutting analysis section with stakeholder clusters, tension patterns, highest-risk misalignments, and (if applicable) co-interview dynamics summary.

2. **Analytic memo:** `pass-2_memo.md`
   - Judgment calls on alignment status assignments (especially Consensus vs. Partial alignment boundary decisions)
   - Patterns noticed across dimensions
   - Political dynamics detected or suspected
   - *(If multi-participant transcripts exist)* How co-interview data influenced alignment assessments — where interaction evidence strengthened or complicated the picture
   - *(If multi-participant transcripts exist)* Constrained responses that affected alignment status — cases where removing a constrained extraction would change the status assignment
   - Anything the analyst noticed that doesn't fit the matrix structure but may matter to the project team

### Review checkpoint

Present to the researcher using the terminal-based review format:

**Part A: Alignment at a Glance**

Summary table:

```
| Dimension            | Status            | Key Finding (1 line)                                              |
|----------------------|-------------------|-------------------------------------------------------------------|
| Goals                | Partial alignment | Shared growth goal; VP wants market expansion, PM wants retention  |
| Success metrics      | Divergent         | No agreed metric — three incompatible definitions of success       |
| Completion criteria  | Consensus         | All agree: launch of v2 to production                             |
| Scope                | Partial alignment | Core features agreed; dispute over admin portal inclusion          |
| Timelines            | Consensus         | Q3 target accepted by all                                         |
| Risks & concerns     | Divergent         | Engineering flags tech debt; leadership flags budget               |
| {custom dimension}   | {status}          | {key finding}                                                     |
```

Below the table:
- Dimensions in consensus: {N} of {total}
- Dimensions with divergence requiring resolution: {list}
- Highest-risk misalignment: {dimension} — {one line on why}
- Stakeholder clusters detected: {yes/no, brief description}
- Hidden assumptions flagged: {count} across {N} dimensions

*(If multi-participant transcripts exist, add:)*
- Dimensions with methodological notes: {list} — {brief reason, e.g., "co-interview provenance, constrained responses"}
- Interaction evidence incorporated: {N} interaction extractions referenced across {N} dimensions
- Co-interview dynamics: {1-2 sentence summary of overall assessment}

**Part B: Substantive Issues for Researcher Input**

Typical issues for Pass 2:

- **Alignment status judgment calls** — "Goals is marked Partial alignment rather than Consensus — here's why the VP's emphasis on market expansion constitutes a material difference, not just different phrasing: {quote from VP} vs. {quote from PM}"
- **Hidden assumptions flagged for verification** — "Stakeholder A stated {X} as obvious, but Stakeholder C's position contradicts it. The matrix flags this as a hidden assumption — does the team need to resolve this explicitly?"
- **Stakeholder cluster interpretation** — "Stakeholders A and B align on 5/6 dimensions. This could reflect genuine shared understanding or organizational echo (both report to the same VP). Your read?"
- **Dimensions with insufficient data** — "Only one stakeholder addressed {dimension}. Should this remain in the analysis or be flagged as a gap to explore in follow-up conversations?"
- *(If multi-participant transcripts exist)* **Co-interview influence on alignment status** — "Scope is marked Consensus, but Stakeholder B's agreement came in a group setting where B deferred to A (constrained extraction E-045, interaction IX-008). If B's constrained response were set aside, the status would shift to Partial alignment. Should the methodological note stand as-is, or should I adjust the status?"
- *(If multi-participant transcripts exist)* **Perspectives to verify in follow-up** — "Based on group dynamics, the following stakeholder positions may warrant 1-1 follow-up: {list with rationale}. Should this appear as a recommendation in the brief?"

After presenting the review:

> "The alignment matrix is saved to `02-analysis/`. When you're satisfied with the alignment analysis, say 'proceed to Pass 3' and I'll synthesize the stakeholder brief."

**Stop and wait for researcher approval before proceeding.**

---

## Pass 3: Stakeholder Brief Synthesis (single agent)

**Goal:** Produce a single, consolidated stakeholder brief suitable for sharing with the project team. This document translates the extraction and alignment data into an actionable reference document.

### What is the stakeholder brief

The stakeholder brief is the primary synthesis artifact. It integrates the per-stakeholder extractions (Pass 1) and the alignment analysis (Pass 2) into a document structured for project team use. It should be readable in 10-15 minutes for a typical 5-8 stakeholder study — concise enough to be actionable, detailed enough to be authoritative.

The stakeholder brief is a **primary deliverable** of this skill.

### Execution

Single agent. Reads `analysis-config.md`, `pass-1_extractions_combined.md`, and `pass-2_alignment-matrix.md`.

### Document structure

```markdown
# Stakeholder Brief: {Project / Study Name}

**Date:** {date}
**Analyst:** {researcher name or "AI-assisted analysis"}
**Stakeholders interviewed:** {count}

## Overview

{2-3 sentences: what the project is, why stakeholder interviews were conducted, and the scope of the analysis.}
```

*(If multi-participant transcripts exist, add to the Overview:)*

```markdown
{1-2 sentences noting that some stakeholders were co-interviewed, and that the analysis accounts for group dynamics where relevant. Keep it matter-of-fact — this is methodological context, not a caveat on the entire study.}
```

```markdown
## Stakeholders Interviewed

| Role | Department | Relationship to Project | Interview Format | Disposition |
|---|---|---|---|---|
| {role} | {dept} | {relationship} | 1-1 / Co-interviewed with {role(s)} | {one-line disposition from Pass 1} |

{Note: stakeholders identified by role, not by name, throughout this document.}

## {Dimension 1: e.g., Goals}

**Alignment status:** {from Pass 2}

{Consolidated perspective — 1-3 paragraphs. Where consensus exists, state it directly. Where divergence exists, present the competing perspectives with role-level attribution.}

**Supporting quotes:**
> "{verbatim quote}" — {role}, {location}
> "{verbatim quote}" — {role}, {location}

**Implications:** {What this means for the project team — concretely: what to act on, what to resolve, what to monitor.}
```

*(If this dimension has a methodological note from Pass 2, integrate it:)*

```markdown
**Note on evidence:** {Brief, factual statement about co-interview provenance — e.g., "The apparent consensus between [Role A] and [Role B] on this dimension was observed in a group setting. [Role B]'s position may warrant independent confirmation." Only include where the methodological note from Pass 2 flagged a substantive concern, not for every dimension that happened to involve co-interviewed stakeholders.}
```

```markdown
## {Dimension 2: e.g., Success Metrics}

{Same structure as above}

...

## {Dimension N}

{Same structure as above}

## Misalignment Summary

{Dedicated section — not buried within individual dimensions.}

| Dimension | Status | Competing Perspectives | Recommended Resolution |
|---|---|---|---|

{For each Partial alignment or Divergent dimension:}

### {Dimension Name}

**What the competing perspectives are:** {brief statement of each position with role attribution}

**Why it matters:** {what happens if this isn't resolved — downstream effects on other dimensions, project risk}

**Recommended resolution approach:** {e.g., "Requires explicit decision from project sponsor," "Suggest aligning in kickoff workshop," "May resolve naturally once technical constraints are documented," "Recommend follow-up conversation between {role A} and {role B}"}

## Emergent Findings

{Anything captured as EMERGENT in Pass 1 that the researcher chose to retain — observations that don't fit the predefined dimensions but are significant enough to document.}

{For each emergent finding:}
- **{Finding label}:** {1-2 sentence summary with supporting quote and role attribution}

{If no emergent findings were retained: "No emergent findings outside the configured dimensions."}

## Recommended Next Steps

{Concrete actions for the project team, derived from the analysis:}

1. **Decisions to make:** {list, each tied to a specific misalignment that requires resolution}
2. **Information gaps to fill:** {stakeholders not interviewed, dimensions with insufficient data, areas needing deeper exploration}
3. **Stakeholder engagement:** {suggested cadence going forward, based on stakeholder dispositions and the complexity of open misalignments}
4. **Referrals:** {aggregated "who else should I talk to" data from Pass 1, deduplicated}
```

*(If multi-participant transcripts exist, add to Recommended Next Steps:)*

```markdown
5. **Follow-up verification:** {Specific perspectives identified during analysis as potentially influenced by group dynamics. List the stakeholder role, the dimension, and why a 1-1 follow-up would add clarity. Only include where the analysis flagged a substantive concern — do not recommend follow-up for every co-interviewed stakeholder.}
```

### Rules

- **Every claim traceable.** Every perspective or quote in the brief must be traceable to a specific extraction ID from Pass 1. The brief is a synthesis layer, not new analysis.
- **Role-level attribution only.** Standard practice for stakeholder briefs — attribute by role, not by name, to encourage future candor. Exception: if the researcher explicitly requests name-level attribution.
- **Recommendations are clearly labeled.** The analyst's recommendations are visually and structurally distinct from stakeholder statements. Readers must be able to tell what was said vs. what the analyst recommends.
- **Misalignment framing is neutral.** Present competing perspectives as different positions, not as one side being wrong. The resolution approach is a recommendation, not a verdict.
- **Co-interview context is proportionate.** Acknowledge the methodological reality without over-qualifying every finding. If co-interview dynamics didn't substantively affect a dimension, don't mention them for that dimension. The goal is a usable brief, not a methods paper.
- **Concise.** Target 3-6 pages of content depending on number of dimensions and complexity of misalignments. Depth goes into the alignment matrix (Pass 2); the brief synthesizes for action.

### Output

Save the following files to `{STUDY_PATH}/02-analysis/`:

1. **Stakeholder brief:** `pass-3_stakeholder-brief.md`
   **This is a primary deliverable.** Consolidated stakeholder brief for project team use.

2. **Analytic memo:** `pass-3_memo.md`
   - Synthesis decisions — what was emphasized or deemphasized and why
   - Framing choices — where two valid ways of presenting a finding existed
   - How competing perspectives were weighted
   - *(If multi-participant transcripts exist)* How co-interview dynamics were integrated into the brief — what was surfaced, what was kept in the memo, and the reasoning
   - What the care check surfaced

### Review checkpoint

Present to the researcher using the terminal-based review format:

**Part A: Brief at a Glance**

```
**Stakeholders:** {N} interviewed ({roles listed})
**Interview formats:** {N} 1-1, {N} multi-participant {omit this line if all interviews were 1-1}
**Dimensions analyzed:** {N}

**Consensus dimensions ({N}):**
- {Dimension}: {one-line key finding}
- ...

**Misalignments requiring resolution ({N}):**
- {Dimension}: {one-line summary of divergence}
- ...

**Emergent findings:** {N}
**Recommended next steps:** {N}
**Highest-priority action:** {the single most important thing for the project team to address}
```

*(If multi-participant transcripts exist, add:)*

```
**Co-interview notes surfaced in brief:** {N} dimensions flagged with evidence notes
**Follow-up verification recommended for:** {list of roles/dimensions, or "none"}
```

**Part B: Substantive Issues for Researcher Input**

Typical issues for Pass 3:

- **Framing choices** — "Scope divergence is framed as a prioritization question rather than a conflict. This softens the finding — here's the raw evidence on both sides. Is this framing appropriate, or should the brief present it more directly?"
- **Recommendation confidence** — "The recommended resolution for {dimension} is {approach}, but the evidence is ambiguous enough that {alternative approach} could also be warranted. Your judgment?"
- **Sensitivity** — "The brief notes {finding}, which could be read as critical of {role}'s position. Should this remain as written, be softened, or be moved to a restricted-access section?"
- **Synthesis judgment** — "The brief's {dimension} section synthesizes two stakeholder views into a shared perspective. Here's the reasoning — do you agree these are compatible, or should they be presented as distinct positions?"
- *(If multi-participant transcripts exist)* **Co-interview caveat calibration** — "The brief includes evidence notes on {N} dimensions. Here they are: {list}. Are these appropriately calibrated — too prominent, not prominent enough, or about right? The goal is methodological honesty without undermining the findings."

After presenting the review:

> "Please review the stakeholder brief for:
> - Does each dimension section accurately capture the range of stakeholder perspectives?
> - Is the misalignment summary complete — are any divergences missing or mischaracterized?
> - Are the recommended next steps actionable and appropriately scoped?
> - Is the brief suitable for sharing with the project team as written?"

**Stop and wait for researcher approval.**

---

## Quality Gates

Before reporting the analysis as complete, verify all of the following:

- [ ] All included transcripts processed — exclusions documented with reason
- [ ] All confirmed dimensions have at least one extraction from at least two stakeholders, or the gap is explicitly documented
- [ ] All quotes in the alignment matrix and brief are verbatim and traceable to extraction IDs
- [ ] Alignment status assignments are consistent with the extraction evidence
- [ ] The brief's misalignment summary accounts for every Partial alignment or Divergent dimension from the alignment matrix
- [ ] Emergent items are either promoted to dimensions, documented in the brief's Emergent Findings section, or explicitly excluded with rationale
- [ ] Role-level attribution throughout the brief and alignment matrix (no names unless researcher explicitly requested otherwise)
- [ ] Recommendations are clearly labeled as analyst interpretation, not stakeholder statements
- [ ] Analytic memos exist for all three passes
- [ ] Stakeholder dispositions and referrals are documented
- [ ] All output files saved to `02-analysis/`

*(If multi-participant transcripts exist, also verify:)*

- [ ] All multi-participant transcripts have interaction extraction tables
- [ ] Constrained response flags have documented evidence (no speculative flags)
- [ ] Group dynamic summaries exist for all multi-participant transcripts
- [ ] Methodological notes appear in the alignment matrix for dimensions where co-interview provenance is relevant
- [ ] The brief's co-interview context is proportionate — present where substantive, absent where not
- [ ] Follow-up verification recommendations (if any) are specific and evidence-based

Run through this checklist after Pass 3. Report any unmet gates to the researcher.

---

## Output File Summary

All files saved to `{STUDY_PATH}/02-analysis/`:

| Pass | Files |
|---|---|
| Setup | `analysis-config.md` |
| Pass 1 | `pass-1_extractions_combined.md`, `pass-1_memo.md` |
| Pass 2 | `pass-2_alignment-matrix.md` **(primary deliverable)**, `pass-2_memo.md` |
| Pass 3 | `pass-3_stakeholder-brief.md` **(primary deliverable)**, `pass-3_memo.md` |

Seven files total. The alignment matrix and stakeholder brief are the two primary deliverables designed for project team use. The extraction file and memos form the evidence trail and audit log.
