---
name: analyze
description: "Qualitative analysis of interview transcripts using Structured Affinity Coding — a five-pass technique combining affinity mapping and thematic coding. Extracts evidence, codes data, forms themes, tests with negative cases, and synthesizes insights. Works across methodologies (stakeholder interviews, user interviews, etc.). Use after transcripts have been imported via /import and are ready for analysis. Triggers: analyze transcripts, code the data, thematic analysis, affinity mapping, run analysis, synthesize interviews, what themes emerged, identify patterns, qualitative coding, analyze the study."
---

# Analyze

You are conducting qualitative analysis of interview transcripts within a study in the Obsidian research vault. This skill implements **Structured Affinity Coding** — a five-pass technique that combines the bottom-up clustering logic of affinity mapping with the evidence-trail discipline of thematic coding.

**Input:** Cleaned transcripts in a study's `01-data/` folder (produced by `/import`).
**Output:** Evidence units, codebook, category map, negative case report, and insight report in `02-analysis/`.

---

## Supporting Materials (not yet created)

The following reference documents will be created as the skill matures through testing:

- **REFERENCE.md** — Methodology depth: atomic decomposition edge cases, codebook writing guidance, multi-coding rules, negative case search techniques, minority theme handling, inter-rater reliability via parallel agents, cross-persona analysis, coding method selection (Saldana), common pitfalls with detection strategies
- **EXAMPLES.md** — Complete worked example from raw transcript through all five passes, demonstrating convergence, memos, and evidence trail
- **TEMPLATE.md** — Blank templates for all output artifacts

### Iterations from testing

*(Capture changes, bugs, and refinements discovered during real use here)*

---

## Analytic Principles

These principles are standing instructions. Apply them throughout every pass — they shape how you approach the data, not what steps you take.

### Categories are constructed, not discovered

Every code, category, and insight you produce is an analytical decision — not an objective truth found "in" the data. The same dataset analyzed differently could produce different but equally valid categorizations.

- Present theme names as choices, not facts.
- When naming a category, you are imposing a frame. Own it — note what the frame emphasizes and what it downplays.
- The researcher's review is evaluating your analytical decisions, not just checking for errors.

Do not present your categorizations as discovered truths. Present them as constructed interpretations that invite the researcher to engage critically.

### Context before compression

When extracting evidence units from flowing conversation and when clustering units into codes and categories, context is necessarily lost. Be aware of the cost:

- Silence, hesitation, and tone shifts are not captured in transcript text but may signal significance
- A qualifier after a strong statement changes the meaning — if the unit is split, the qualification disappears
- Power dynamics (participant deference, social desirability bias) may inflate or deflate statement strength
- Lived experience cannot always be safely reduced to a code label

When compression risks distorting meaning, note the risk in the analytic memo. The researcher decides whether the compression is acceptable.

### Edge and variance scan

At every stage, actively check: who is absent from this pattern, and whose experience would complicate it?

- Which user types or backgrounds are not represented in a given category? Note the absence.
- Would a participant from a different cultural background, seniority level, or accessibility situation experience this differently?
- When a category appears universal, test it — is it shared across groups, or dominated by the most vocal group?
- Minority and edge cases are not noise. They may destabilize an overconfident category.

### Plural interpretations

Significant findings should carry at least two plausible readings. For each major insight in Pass 5:

- Provide the primary interpretation and at least one alternative reading
- Flag uncertainty zones where participant intent is ambiguous
- State what is directly observed versus what is inferred

### Care check

Before finalizing insights, consider consequences:

- Who could be harmed by misinterpretation? Research findings drive product decisions that affect real users.
- Who bears the cost of oversimplification?
- Are any insights presented with more certainty than the evidence warrants?

---

## Quotation Integrity — Hard Rule

**All quotations must be verbatim passages copied directly from source transcripts. This is non-negotiable.**

- Never invent, paraphrase, summarize, adjust, or clean up a quotation
- Every word, punctuation mark, and formatting element must match the source transcript exactly
- Truncated quotes must mark the omission with `[...]`
- Every quotation must include participant ID and turn/line reference so the researcher can verify it
- If the researcher looks up a quoted passage in the transcript, there must be a **100% match**

Quotation fabrication is a critical failure. Treat it as such in every pass.

---

## Setup: Study Context Confirmation

Before any analysis, establish the analytic lens by reading context and confirming it with the researcher.

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
- Interview guide questions
- Business goals or project framing (from SOW, project brief, or research plan)
- Any defined user types, personas, or participant roles

### Step 3: Read transcripts

List all transcripts in `{STUDY_PATH}/01-data/` (exclude README.md):
```bash
ls {STUDY_PATH}/01-data/
```

Read the header of each transcript to extract participant metadata (participant ID, role, interviewer, date).

### Step 4: Confirm with the researcher

Present findings and ask the researcher to confirm using AskUserQuestion:

**Question 1 — Research objectives:**
> "I found the following research objectives in your research plan:
> - [list each objective]
>
> Are these correct, or would you like to edit or add to them?"

**Question 2 — Transcripts:**
> "I found the following transcripts to analyze:
> - [list each with participant ID and role]
>
> Should I include all of them, or exclude any?"

Document any excluded transcripts and the reason.

**Question 3 — Coding approach:**
> "Which coding approach would you like to use?"

Present three options:
1. **Inductive** — All codes emerge from the data. No predefined codes. Best for exploratory studies.
2. **Deductive + inductive** — Start with a predefined code list, then open-code anything it doesn't capture. Best when building on prior work.
3. **Hybrid** — Seed initial codes from the research objectives and interview guide, then supplement with open coding.

If the researcher selects deductive + inductive, ask them to provide or point to the predefined codebook.

**Question 4 — User types / personas:**
> "I identified the following user types from the research plan and context documents:
> - [list user types/personas found]
>
> Here is the participant-to-persona mapping I've inferred:
> - P01 = [role/type]
> - P02 = [role/type]
>
> Is this correct? Should any participants be assigned to different groups?"

If no personas are defined in the context documents, ask:
> "I didn't find defined user types or personas in the study context. Is participant segmentation relevant for this study? If so, what groups should I use?"

### Step 5: Save configuration

Save the confirmed configuration to `{STUDY_PATH}/02-analysis/analysis-config.md`:

```markdown
# Analysis Configuration

**Study:** {study name}
**Date started:** {today's date}
**Coding approach:** {inductive / deductive+inductive / hybrid}

## Research Objectives
{confirmed objectives}

## Transcripts Included
{list with participant ID, role, user type}

## Transcripts Excluded
{list with reason, or "None"}

## User Types
{confirmed persona mapping}

## Business Context
{summary of business goals from project and study context}
```

Confirm with the researcher:
> "Setup complete. I've saved the analysis configuration. Ready to begin Pass 1 (Evidence Extraction). Proceed?"

---

## Pass 1: Evidence Extraction (single agent)

**Goal:** Read each transcript and extract discrete evidence units.

### What is an evidence unit

An evidence unit is a standalone factual statement about something the participant said, experienced, described, or demonstrated. Each unit captures **one idea**. If a participant's turn covers multiple topics, produce multiple units.

### Evidence type tagging

Classify each unit by primary evidence type:

| Type | What it captures | Example |
|---|---|---|
| **Behavioral** | An action the participant took or described taking | "I opened Recent, scrolled, couldn't find it, went through four folders" |
| **Attitudinal** | A belief, feeling, judgment, or mental model | "I'm never sure if my version is the latest one" |
| **Pain point** | A specific problem, friction, or unmet need | "You have to add supporting context one at a time" |

A unit may carry multiple types. Assign the primary; note secondary types where relevant.

### Atomic decomposition rules

- **Split** compound statements when two distinct ideas are joined: "Found navigation confusing AND clicked the wrong button" becomes two units
- **Do NOT split** when context is embedded: "She found it confusing but said she would figure it out eventually" — the qualifier changes the meaning; keep as one unit
- **Do NOT split** verbatim quotes — a long quote with multiple ideas is still one unit; it will be multi-coded in Pass 2
- **Preserve causal links:** "Clicked the wrong button because the labels looked the same" — splitting loses the cause; keep as one unit

### Process

For each transcript:

1. Read the full transcript
2. Work through each participant turn sequentially
3. For each analytically significant moment, create an evidence unit
4. Skip administrative passages (greetings, consent language, scheduling) — mark as N/A in the memo if relevant
5. Apply the Quotation Integrity rule: copy the supporting quote exactly as it appears

### Per evidence unit, record

| Field | Content |
|---|---|
| ID | Sequential across the full study: EU-001, EU-002, ... |
| Participant | Participant ID (e.g., P01) |
| User type | Persona from the confirmed mapping |
| Evidence type | Behavioral / Attitudinal / Pain point |
| Observation | 1-2 sentence summary in the researcher's voice |
| Quote | Verbatim supporting quote from the transcript |
| Location | Participant ID + turn number or line range in source transcript |

### Output

Save the following files to `{STUDY_PATH}/02-analysis/`:

1. **One file per transcript:** `pass-1_evidence_{participant-id}.md`
   - Header with participant metadata
   - Evidence unit table for that transcript

2. **Combined evidence file:** `pass-1_evidence_combined.md`
   - All evidence units across all transcripts in a single table
   - Summary counts: total units, units per participant, units per user type, units per evidence type

3. **Analytic memo:** `pass-1_memo.md`
   - Notes on extraction decisions and ambiguous passages
   - Passages where context was lost in compression (per *Context before compression* principle)
   - Thin or atypical transcripts flagged (per *Edge and variance scan* principle)
   - Any N/A passages noted

### Review checkpoint

Present to the researcher:
> "Pass 1 complete. I extracted {N} evidence units across {N} transcripts.
>
> - {N} behavioral, {N} attitudinal, {N} pain point
> - Per participant: [summary]
>
> Output files are in `02-analysis/`. Please review the evidence units for:
> - Accuracy and completeness — are important moments captured?
> - Granularity — are units atomic (one idea each)?
> - Evidence types — are they correctly assigned?
> - **Quotation accuracy** — please spot-check quotes against the source transcripts
>
> The analytic memo notes {N} passages where I flagged compression risk or ambiguity.
>
> When you're satisfied, say 'proceed to Pass 2' and I'll begin coding."

**Stop and wait for researcher approval before proceeding.**

---

## Pass 2: Open Coding (three parallel agents + convergence)

**Goal:** Assign descriptive codes to every evidence unit. Run three independent agents on the same data, then converge.

### Coding approach

Use the approach confirmed during setup:

- **Inductive:** Begin with no codes. Let them emerge from the data.
- **Deductive + inductive:** Load the predefined codebook first. Apply those codes, then open-code any units the predefined codes don't capture.
- **Hybrid:** Read the confirmed research objectives and interview guide. Derive seed codes from the topics and questions they contain. Then supplement with open coding for anything the seeds don't capture.

### What is a code

A code is a short label (2-5 words) assigned to an evidence unit that captures what the unit is about. Codes name; they do not interpret.

**Naming rules:**
- Name the pattern from the participant's frame, not the topic — "Can't find saved work" not "Search and navigation"
- Write from participant experience — "Doesn't trust the system" not "Trust issues"
- No solution-implying names — "Needs better search" is a solution, not a pattern
- No evaluative names — "Poor error handling" is a design judgment, not a participant experience

### Process (each agent runs independently)

1. Read the combined evidence file (`pass-1_evidence_combined.md`) and the analysis configuration
2. Work through every evidence unit
3. For each unit, assign one or more codes
4. When a new concept appears, create a new code with a brief definition
5. When a unit matches an existing code, reuse it
6. **Multi-coding:** If a unit genuinely belongs to two or more codes, assign all of them. Multi-coding is accurate representation, not indecision. But do not multi-code to resolve uncertainty — assign the best-fit code and note the uncertainty.
7. After all units are coded, review the code list for redundancies — merge codes that mean the same thing
8. Tag units that fit no code as `RESIDUAL`

### Codebook entry structure

Each code must include all of the following:

```markdown
### {CODE NAME}

**Definition:** {1-2 sentences — what this captures, from participant perspective}

**Inclusion:** {What qualifies — specific enough to decide edge cases}

**Exclusion:** {What is NOT this code — especially against neighboring codes}

**Examples:**
- {verbatim evidence unit 1}
- {verbatim evidence unit 2}

**Evidence count:** {N units}
**User type distribution:** {type}: {N}, {type}: {N}
```

### Guard rail checks (run after coding, before output)

- [ ] No code holds >25% of all evidence units (galaxy-brained code — split it)
- [ ] No code named "Miscellaneous", "Other", or similar (dust-bin code — resolve or dissolve)
- [ ] All code names describe participant experience, not design solutions or evaluations
- [ ] All evidence units are coded, tagged RESIDUAL, or marked N/A — nothing silently dropped
- [ ] Inclusion/exclusion criteria are specific enough to decide a borderline case

### Launching the three agents

Launch three agents in parallel using the Agent tool. Each agent receives:
- The combined evidence file
- The analysis configuration (research objectives, coding approach, user type mapping)
- If deductive+inductive: the predefined codebook
- If hybrid: the research objectives and interview guide for seed derivation
- These instructions for coding process, codebook structure, and naming rules

Each agent works independently. Do not share state between them.

**Agent prompt template:**

> You are Agent {1/2/3} performing open coding on qualitative research data. Read the combined evidence file and analysis configuration provided below. For every evidence unit, assign one or more descriptive codes following the coding approach, naming rules, and codebook entry structure specified. Produce a coded evidence table and a complete codebook. Tag any uncoded units as RESIDUAL. Run the guard rail checks before finalizing. Also produce an analytic memo documenting your coding reasoning — especially where naming choices were difficult, where you considered alternatives, and where you noted ambiguity.
>
> {Include: combined evidence file content, analysis config content, coding approach details}

### Convergence

After all three agents complete, produce a convergence document. Compare the three outputs:

**Agreement zones:**
For each evidence unit, check whether all three agents assigned conceptually similar codes (exact name match is not required — conceptual alignment counts). List the codes/concepts where all three agree. These are high-confidence codes.

**Divergence zones:**
List evidence units where agents assigned meaningfully different codes. For each divergence, show:
- The evidence unit
- Each agent's code assignment and reasoning (from their memos)
- What the divergence reveals about ambiguity in the data

**Unique codes:**
List codes that only one agent created. Assess whether each represents:
- A genuine subtle pattern the other two missed
- Noise or over-splitting that the other two appropriately avoided

**Residual comparison:**
Compare which units each agent tagged as RESIDUAL. Do residuals cluster across agents? A unit tagged RESIDUAL by all three is genuinely uncategorizable. A unit tagged RESIDUAL by one but coded by the others may reveal a pattern one agent missed.

### Output

Save to `{STUDY_PATH}/02-analysis/`:

1. **Per-agent outputs:** `pass-2_agent-1_codebook.md`, `pass-2_agent-1_coded.md` (repeat for agents 2 and 3)
2. **Convergence document:** `pass-2_convergence.md`
3. **Analytic memos:** `pass-2_memo_agent-1.md`, `pass-2_memo_agent-2.md`, `pass-2_memo_agent-3.md`

### Review checkpoint

Present to the researcher:
> "Pass 2 complete. Three agents independently coded {N} evidence units.
>
> - Agent 1 produced {N} codes, Agent 2 produced {N} codes, Agent 3 produced {N} codes
> - Agreement zones: {N} evidence units coded consistently across all three agents
> - Divergence zones: {N} units coded differently — see convergence document for details
> - Residuals: {N} units tagged RESIDUAL by at least one agent
>
> Please review the convergence document (`pass-2_convergence.md`) and decide:
> - Which codes to keep, merge, rename, or discard
> - Whether inclusion/exclusion boundaries are clear
> - How to resolve divergences
>
> When you've finalized the codebook, say 'proceed to Pass 3' and provide any edits or decisions."

**Stop and wait for researcher approval. The researcher's decisions produce the authoritative codebook for Pass 3.**

After the researcher provides their decisions, save the final codebook to `pass-2_codebook_final.md` and the final coded evidence table to `pass-2_coded_final.md`.

---

## Pass 3: Theme Formation (three parallel agents + convergence)

**Goal:** Cluster the approved codes into categories (themes). Run three independent agents, then converge.

### What is a category

A category groups codes that share a conceptual pattern. It names a pattern — it does not interpret what the pattern means. (Interpretation comes in Pass 5.)

- Not a single word: "Navigation" is too broad
- From the participant's frame: "Can't find saved work" not "Findability issues"
- No solutions: "Needs better search" is a design response, not a category
- No evaluations: "Poor error handling" is a judgment, not a pattern

### Sensitizing frame

Each agent reads the confirmed research objectives and interview guide from the analysis configuration. Categories that address research objectives are expected. Categories that emerge outside the research questions are flagged as **Emergent**.

### Process (each agent runs independently)

1. Read the final codebook (`pass-2_codebook_final.md`) and the analysis configuration
2. Group related codes into candidate categories
3. For each category, produce a full definition:

```markdown
### TC-{NN}: {CATEGORY NAME}

**Definition:** {1-2 sentences — what this captures}

**Inclusion:** {What qualifies — specific enough for edge cases}

**Exclusion:** {What is NOT this category — especially against its closest neighbor}

**Objective alignment:** {Which research objective(s), or "Emergent"}

**Constituent codes:** {list of codes in this category}

**Prevalence:** {N} evidence units across {N} participants

**User type breakdown:**
- {type}: {N} units — {notes on how this group expresses the pattern}
- {type}: {N} units — {notes}

**Role concentration:** {Distributed / Concentrated: {type} / Single-type: {type}}

**Key evidence:**
> "{verbatim quote 1}" — {Participant ID}, {location}
> "{verbatim quote 2}" — {Participant ID}, {location}
> "{verbatim quote 3}" — {Participant ID}, {location}
```

4. Handle edge cases:
   - **Residual codes** that don't fit any category — if 3+ share a pattern, form a new category; otherwise document as outliers
   - **Overloaded categories** holding >25% of evidence — split them
   - **Minority categories** (1-2 units) — keep if evidence is strong, participant is underrepresented, or finding would change a decision if confirmed. Flag as Low confidence. Do not suppress.

5. Flag each category's type:
   - **Cross-group** — evidence from multiple user types
   - **Group-specific** — concentrated in one user type (>60%)
   - **Divergent** — different user types have opposing experiences

### Launching the three agents

Same pattern as Pass 2. Each agent receives the final codebook, analysis configuration, and these instructions. Each works independently.

### Convergence

Compare the three category maps:

**Shared categories:** Patterns all three agents identified (possibly named differently or with different boundaries). Map the conceptual alignment.

**Divergent groupings:** Codes clustered differently across agents. Show each agent's grouping and reasoning.

**Unique categories:** Categories only one agent proposed. Assess strength.

**Structural differences:** How the three maps differ in overall organization — number of categories, level of granularity, treatment of edge cases.

### Output

Save to `{STUDY_PATH}/02-analysis/`:

1. **Per-agent outputs:** `pass-3_agent-1_categories.md` (repeat for 2, 3)
2. **Convergence document:** `pass-3_convergence.md`
3. **Analytic memos:** `pass-3_memo_agent-1.md` (repeat for 2, 3)

### Review checkpoint

> "Pass 3 complete. Three agents independently formed categories from the approved codebook.
>
> - Agent 1: {N} categories, Agent 2: {N} categories, Agent 3: {N} categories
> - Shared categories: {N} patterns identified by all three agents
> - Divergent groupings: {N} codes clustered differently
> - Unique categories: {N} proposed by only one agent
>
> **This is the most important review checkpoint.** Please review the convergence document and decide:
> - Which categories to keep, merge, split, or rename
> - How to resolve divergent groupings
> - Whether minority categories should be kept or folded
>
> When you've finalized the category structure, say 'proceed to Pass 4' and provide your decisions."

**Stop and wait for researcher approval.**

Save the final category map to `pass-3_categories_final.md`.

---

## Pass 4: Negative Case Analysis (single agent)

**Goal:** For each approved category, deliberately search for evidence that contradicts or complicates the dominant pattern.

This pass takes the researcher-approved categories as input and works **adversarially** against them. This separation matters: the agents that formed the categories should not be the ones testing them.

### Process

For each category in `pass-3_categories_final.md`:

1. **State the dominant pattern** in one sentence. What does this category claim is true about the participant experience?

2. **Search all evidence units** for any that undercut, complicate, or invert that pattern. Specifically look for:
   - Units showing the opposite experience
   - Participants from different user types who may experience the same situation differently
   - Sessions where the pattern is notably absent
   - Evidence coded to one category that could complicate a neighboring category
   - Evidence from underrepresented user types and minority participants (*Edge and variance scan*)

3. **Assess each negative case found:**
   - Does it reveal an overgeneralization in the category definition? **Propose a revision.**
   - Do multiple negative cases cluster into their own pattern? **Propose splitting the category.**
   - Is it genuinely atypical? **Retain and document** — note what makes it atypical without dismissing it for convenience.

4. **If zero negative cases found** for a category, note this explicitly. Assess whether the search was thorough given the sample size. A small sample with no contradictions is not the same as a robust finding with no contradictions. (*Categories are constructed* — a negative case may reveal the boundary was drawn wrong, not that the evidence is an outlier.)

### Output

Save to `{STUDY_PATH}/02-analysis/`:

1. **Negative case report:** `pass-4_negative-cases.md`

   For each category:
   ```markdown
   ### TC-{NN}: {Category Name}

   **Dominant pattern:** {one sentence}

   **Negative cases found:** {N}

   {For each negative case:}
   - **Evidence unit:** EU-{NNN} — "{quote}" ({Participant}, {location})
   - **How it complicates the pattern:** {explanation}
   - **Recommended action:** {Revise definition / Propose split / Retain and document}

   {If zero:}
   - None found. Search covered all {N} evidence units across {N} transcripts. Sample size: {N} participants. Confidence in absence: {High if large sample and thorough search / Low if small sample — recommend probing in future sessions}.
   ```

2. **Proposed revisions summary:** List all recommended changes to category definitions, splits, or merges.

3. **Analytic memo:** `pass-4_memo.md` — Search reasoning, judgment calls, and any patterns noticed across the negative case analysis.

### Review checkpoint

> "Pass 4 complete. I searched for negative cases across all {N} categories.
>
> - {N} categories had negative cases found
> - {N} categories had no negative cases (documented with search notes)
> - Proposed revisions: {summary of any definition changes, splits, or merges}
>
> Please review the negative case report and approve or modify the proposed revisions before I proceed to insight synthesis.
>
> The analytic memo documents my search reasoning for each category."

**Stop and wait for researcher approval.**

If the researcher approves revisions, update `pass-3_categories_final.md` with the changes (or save a revised version as `pass-3_categories_revised.md`).

---

## Pass 5: Insight Synthesis (single agent)

**Goal:** Translate the approved categories into actionable, propositional insight statements grounded in evidence and business context.

### What is an insight

An insight is an interpretive claim — not a pattern label.

- **Category (Pass 3):** "Can't confirm system followed through"
- **Insight (Pass 5):** "Users' sense of competence depends on trusting that their work persists — and that trust is systematically undermined by the absence of visible state feedback."

An insight makes a claim. It could be argued against. It goes beyond describing *what* to interpreting *why it matters*.

### Process

1. Read the final (or revised) category map, the negative case report, and the analysis configuration (research objectives, business context)

2. For each category (or cluster of related categories), draft an insight:

```markdown
### Insight {N}: {Propositional statement}

**Supporting categories:** TC-{NN}, TC-{NN}

**Objective alignment:** {Which research objective(s), or "Emergent"}

**User type scope:** {Universal / Group-specific: {type} / Comparative: {description of contrast}}

**Strength of evidence:**
- Participants: {N} of {total}
- Evidence units: {N}
- Evidence types: {N} behavioral, {N} attitudinal, {N} pain point
- Consistency: {High — consistent across participants / Medium — some variation / Low — mixed signals}

**Negative cases:** {Summary from Pass 4 — how contradicting evidence qualifies this insight}

**Business relevance:** {Connection to project goals, product strategy, or stakeholder priorities from the business context loaded during setup}

**Key quotes:**
> "{verbatim quote 1}" — {Participant ID}, {location}
> "{verbatim quote 2}" — {Participant ID}, {location}
> "{verbatim quote 3}" — {Participant ID}, {location}

**Alternative interpretation:** {At least one other plausible reading of the same evidence — per Plural interpretations principle}

**Implications:** {What this means for design, product, or strategy decisions}
```

3. **Significance ranking:** Order insights by overall significance, weighing:
   - Strength of evidence (participants, consistency, evidence types)
   - Business relevance (connection to engagement goals)
   - Insights with strong evidence AND clear business implications rank highest
   - Flag insights that are interesting but disconnected from business goals
   - Flag insights that are business-relevant but weakly evidenced

4. **User type analysis:** Actively look for comparative insights — cases where the difference between user types IS the finding. Don't just report categories per group.

5. **Evidence type weighting:** Note the evidence mix. Behavioral evidence + pain points carry more stakeholder weight than attitudinal statements alone.

6. **Care check:** Before finalizing:
   - Who could be harmed by misinterpretation of these insights?
   - Are any insights presented with more certainty than the evidence warrants?
   - Whose experience is centered and whose is marginalized by the way insights are framed?

### Output

Save to `{STUDY_PATH}/02-analysis/`:

1. **Insight report:** `pass-5_insights.md`
   - Summary of all insights in significance order
   - Full evidence trail for each insight (insight -> categories -> codes -> evidence units -> quotes -> transcript locations)

2. **Analytic memo:** `pass-5_memo.md`
   - How the agent moved from category-level patterns to propositional claims
   - Where interpretive judgment was strongest
   - What the care check surfaced

### Review checkpoint

> "Pass 5 complete. I synthesized {N} insights from the category structure.
>
> Top insights by significance:
> 1. {insight statement} — {evidence strength}, {business relevance}
> 2. {insight statement} — {evidence strength}, {business relevance}
> 3. {insight statement} — {evidence strength}, {business relevance}
>
> Each insight includes an alternative interpretation, negative case qualifications, and user type scoping.
>
> Please review the insight report for:
> - Are insights propositional (claims) not descriptive (labels)?
> - Is the significance ranking appropriate?
> - Are business relevance assessments accurate?
> - Do negative cases appropriately qualify the claims?
> - Are the alternative interpretations plausible and useful?"

---

## Quality Gates

Before reporting the analysis as complete, verify all of the following:

- [ ] All confirmed transcripts processed — exclusions documented with reason
- [ ] Every evidence unit assigned a code, RESIDUAL tag, or N/A tag — nothing silently dropped
- [ ] Every category has: name, definition, inclusion criteria, exclusion criteria, 2+ examples
- [ ] No category defined by evidence from a single participant (or explicitly flagged as minority)
- [ ] Multi-coded units documented — not collapsed or hidden
- [ ] Negative cases listed for every category (1+ per category, or explicitly "none found" with search documentation)
- [ ] Category names describe participant-frame patterns — not topics, solutions, or design judgments
- [ ] Residuals reviewed — clusters of 3+ resolved into new categories or documented as outliers
- [ ] User type breakdown documented for every category
- [ ] Analytic memos produced for every pass
- [ ] All quotations are verbatim from source transcripts
- [ ] Insight statements are propositional (make claims) not descriptive (name patterns)
- [ ] Business relevance assessed for every insight
- [ ] Alternative interpretations provided for major insights
- [ ] All output files saved to `02-analysis/`

Run through this checklist after Pass 5. Report any unmet gates to the researcher.

---

## Output File Summary

All files saved to `{STUDY_PATH}/02-analysis/`:

| Pass | Files |
|---|---|
| Setup | `analysis-config.md` |
| Pass 1 | `pass-1_evidence_{participant}.md` (one per transcript), `pass-1_evidence_combined.md`, `pass-1_memo.md` |
| Pass 2 | `pass-2_agent-{1,2,3}_codebook.md`, `pass-2_agent-{1,2,3}_coded.md`, `pass-2_convergence.md`, `pass-2_memo_agent-{1,2,3}.md`, `pass-2_codebook_final.md`, `pass-2_coded_final.md` |
| Pass 3 | `pass-3_agent-{1,2,3}_categories.md`, `pass-3_convergence.md`, `pass-3_memo_agent-{1,2,3}.md`, `pass-3_categories_final.md` |
| Pass 4 | `pass-4_negative-cases.md`, `pass-4_memo.md`, (optionally `pass-3_categories_revised.md`) |
| Pass 5 | `pass-5_insights.md`, `pass-5_memo.md` |
