# Qualitative Analysis Technique Proposal

## Context

The research system plan (Phase 4) originally defined three separate agents: qualitative coding, thematic analysis, and insight writing. We've agreed to collapse these into a single analysis capability that works across methodologies (stakeholder interviews, user interviews, etc.). This proposal defines the analytic technique that capability should implement.

The technique draws from **affinity mapping** (UX design/research) and **thematic coding** (social sciences). A colleague's thematic coding skill will be shared for comparison before we finalize implementation.

---

## Proposed Technique: Structured Affinity Coding

A four-pass hybrid that takes the evidence-trail discipline of thematic coding and the bottom-up clustering logic of affinity mapping. Each pass produces a reviewable artifact; the researcher approves before the next pass runs.

### Why hybrid, not one or the other

**Affinity mapping** is fast and intuitive but under-structured for AI-assisted work — it relies on spatial reasoning, physical manipulation, and implicit researcher judgment that doesn't translate well to a text-based agent workflow. Its strength is the inductive clustering step: start with discrete observations, group by similarity, let themes emerge.

**Thematic coding** provides the structure AI needs — explicit codes, codebooks, defined evidence links — but its full academic form (Braun & Clarke six-phase, for example) is heavier than consulting UX research typically requires. Its strength is the audit trail: every insight traces back through theme, code, and quote to a specific moment in a specific transcript.

The hybrid takes **clustering from affinity mapping** and **evidence grounding from thematic coding**, producing outputs that are rigorous enough to withstand stakeholder scrutiny but practical enough for consulting timelines.

---

## Hard Rule: Quotation Integrity

All quotations surfaced in the analysis must be **verbatim passages** copied directly from the source transcripts. This is a non-negotiable rule enforced across all passes:

- Quotations must never be invented, paraphrased, summarized, adjusted, or cleaned up in any way
- Every word, punctuation mark, and formatting element must match the source transcript exactly
- If a quote is truncated, the omission must be marked with `[...]`
- Every quotation must include participant ID and sufficient location information (turn/line reference) for the researcher to find and verify it in the source transcript
- If the researcher looks up a quoted passage in the original transcript, there must be a **100% match**

This rule exists as a safeguard against AI hallucination. The analysis skill instructions must treat quotation fabrication as a critical failure mode, not a soft preference.

---

## Setup Step: Study Context Confirmation

Before analysis begins, the skill runs an interactive setup that establishes the analytic lens for the study. This step reads available context and confirms it with the researcher.

**The skill reads from two levels:**
- **Project context** (`Client/_context/`) — SOW, project briefs, stakeholder maps, business goals, and other documents that define the overall engagement scope and objectives
- **Study context** (`{study}/00-context/`) — research plan, interview guide, and any study-specific context documents

**The skill then asks the researcher to confirm:**

1. **Research objectives** — Displays the objectives found in the research plan and asks the researcher to confirm, edit, or supplement them. These objectives will guide theme formation and insight prioritization.
2. **Transcripts for analysis** — Lists all transcripts found in `01-data/` and asks the researcher to confirm which ones to include. (All by default, but the researcher may exclude incomplete or off-topic sessions.)
3. **Coding approach** — Presents the three options (inductive, deductive+inductive, hybrid). If the researcher selects deductive+inductive, prompts them to identify or provide the predefined codebook.
4. **User types / personas** — Displays any user types or personas identified in the research plan or context documents. Asks the researcher to confirm the participant-to-persona mapping (e.g., P01 = Clinical Operations, P02 = Data Scientist). If no personas are defined, asks whether participant segmentation is relevant for this study.

This setup step is critical because it front-loads the interpretive decisions that shape every subsequent pass. The researcher's confirmations become the authoritative configuration for the analysis run.

---

## Sensitizing Frames: Research Objectives and Business Context

### Research objectives and interview guide

Analysis is not purely inductive. Before any coding begins, the skill reads the study's `00-context/` folder to load:
- **Research objectives** from the research plan — what questions is this study trying to answer?
- **Interview guide questions** — what specific topics were explored?

These act as a **sensitizing frame** during theme formation (Pass 3): themes that align with research objectives are expected and should be evaluated for how well the data answers the original questions. Themes that fall *outside* the research objectives should still be captured but flagged as **emergent** — unexpected patterns the data revealed that weren't part of the original inquiry.

### Business value axis

Since this system serves consulting UX research (not academia), insight significance is evaluated along two dimensions:

1. **Strength of evidence** — How many participants? How consistent is the pattern? How vivid is the supporting data?
2. **Business relevance** — What are the implications for product decisions, strategy, or stakeholder priorities? Does this theme connect to the business goals of the engagement?

An insight that is "interesting" but disconnected from business value carries less weight as a key takeaway than one with clear implications for the product, feature, or strategy under evaluation.

**Business context sources:**
- **Project-level** (`Client/_context/`) — SOW, project briefs, business goals, stakeholder priorities. These define the broader engagement context and what the client cares about.
- **Study-level** (`{study}/00-context/`) — Research plan framing, any study-specific business context.

Both levels are loaded during the setup step and inform the business relevance assessment in Pass 4.

---

## The Four Passes

### Pass 1: Observation Extraction (single agent)

**Task:** Read each transcript and extract discrete observations.

An observation is a standalone factual statement about something the participant said, experienced, described, or demonstrated. Each observation captures one idea — if a participant's turn covers multiple topics, it produces multiple observations.

**Per observation, record:**
| Field | Description |
|---|---|
| ID | Sequential (e.g., OBS-001) |
| Participant | Participant ID (e.g., P01) |
| User type | Persona or archetype this participant belongs to (from setup step) |
| Observation | 1-2 sentence summary in the researcher's voice |
| Quote | Verbatim supporting quote from the transcript (see Quotation Integrity rule) |
| Location | Participant ID + approximate turn/line reference in source transcript |

**Scope:** All transcripts in the study are processed in a single pass.

**Output:** One file per transcript in `02-analysis/` (e.g., `pass-1_observations_p01.md`), plus a combined observations file across all transcripts.

**What this pass is NOT:** It is not interpretation. Observations should be descriptive and grounded — "P01 found the upload process slow because files had to be added one at a time" rather than "P01 was frustrated with the tool." The researcher's interpretive lens comes in later passes.

**Analogy:** This is the "write the sticky notes" step of affinity mapping, but with the citation precision of qualitative coding.

**Human review:** Researcher scans observations for accuracy, completeness, and appropriate granularity. Are important moments captured? Are observations atomic (one idea each) or do some need splitting? Are any observations interpretive rather than descriptive? **Critically: spot-check quotations against source transcripts for verbatim accuracy.**

---

### Pass 2: Open Coding (three parallel agents + convergence)

**Task:** Review all observations across transcripts and assign descriptive codes.

**Before this pass begins**, the researcher selects a coding approach:

1. **Inductive only** — All codes emerge from the data. No predefined code list. Best when the study is exploratory and the researcher wants to avoid biasing the analysis.
2. **Deductive + inductive** — Start with a predefined code list (from a prior study, research hypotheses, or framework) and apply it first. Then open-code anything the predefined codes don't capture. Best when building on prior work or testing specific hypotheses.
3. **Hybrid** — Derive initial seed codes from the research objectives and interview guide questions, then supplement with open inductive coding. The research context naturally seeds the codebook without requiring the researcher to manually define codes upfront.

**Process (per agent):**
1. Work through the combined observation list
2. For each observation, assign one or more codes (short labels, 2-5 words)
3. When a new concept appears, create a new code with a brief definition
4. When an observation matches an existing concept, reuse the existing code
5. After all observations are coded, review the code list for redundancies — merge codes that describe the same thing with different words

**Output per agent:** Coded observation table + working codebook (list of all codes with definitions, observation counts, and user type distribution per code).

**Convergence step:** After all three agents complete, produce a comparison document that shows:
- **Agreement zones** — codes/concepts all three agents identified (high confidence)
- **Divergence zones** — observations coded differently across agents, with each agent's interpretation shown side by side
- **Unique codes** — codes only one agent surfaced (may indicate either noise or a subtle pattern the others missed)

**Human review:** Researcher reviews the convergence document and makes final decisions: which codes to keep, merge, rename, or discard. Produces the authoritative codebook that Pass 3 will use.

---

### Pass 3: Theme Formation (three parallel agents + convergence)

**Task:** Cluster codes into themes.

This is the core affinity mapping step, applied to codes rather than raw observations. Group codes that together describe a larger pattern, behavior, need, or tension. A theme is not just a category — it should make a claim or describe a pattern.

**Sensitizing frame:** Agents reference the research objectives and interview guide from `00-context/` when forming themes. Themes that address research objectives directly are expected. Themes that emerge outside the research questions are flagged as **emergent findings**.

**Process (per agent):**
1. Review the researcher-approved codebook from Pass 2
2. Group related codes into candidate themes
3. For each theme, write:
   - **Theme name:** Descriptive phrase (not a single word)
   - **Theme description:** 2-3 sentences defining what this theme captures
   - **Objective alignment:** Which research objective(s) this theme addresses, or "Emergent" if none
   - **Constituent codes:** Which codes belong to this theme
   - **Prevalence:** How many participants and observations support this theme
   - **User type breakdown:** Which user types contribute to this theme, how strongly, and whether the pattern is universal or group-specific. Note where different user types express the same theme differently or where a theme is concentrated in one group.
   - **Key evidence:** 2-3 representative quotes (verbatim, per Quotation Integrity rule), selected to represent the range of user types where applicable
4. Check for orphan codes that don't fit any theme
5. Check for overloaded themes that should be split
6. Flag **cross-group themes** (shared across user types), **group-specific themes** (concentrated in one user type), and **divergent themes** (where different user types have opposing experiences or perspectives on the same topic)

**Output per agent:** Complete theme map showing themes -> codes -> observations -> quotes.

**Convergence step:** Comparison document showing:
- **Shared themes** — patterns all three agents identified (possibly with different names/boundaries)
- **Divergent groupings** — codes that were clustered differently across agents
- **Unique themes** — themes only one agent proposed
- **Structural differences** — how the three theme maps differ in overall organization

**Human review:** Researcher reviews the convergence document, settles the theme structure, and produces the authoritative theme map. This is the most critical review checkpoint — theme formation is where the most interpretive judgment lives.

---

### Pass 4: Insight Synthesis (single agent)

**Task:** Translate themes into actionable insight statements.

An insight is a concise claim about users, their context, or their needs, grounded in the evidence. Good insights are specific enough to inform design or strategy decisions — not just restated observations.

**Per insight, record:**
| Field | Description |
|---|---|
| Insight | A clear, specific statement (1-2 sentences) |
| Supporting theme(s) | Which theme(s) this insight draws from |
| Objective alignment | Which research objective(s) this addresses, or "Emergent" |
| User type scope | Universal, group-specific (which group), or comparative (the difference between groups is the insight) |
| Strength of evidence | How many participants, how consistent the pattern |
| Business relevance | Connection to product goals, strategy, or stakeholder priorities |
| Key quotes | 2-3 representative verbatim quotes |
| Implications | What this means for design, product, or strategy |

**Significance ranking:** Insights are presented in order of overall significance, weighing both strength of evidence and business relevance. Insights with strong evidence AND clear business implications rank highest. Insights that are interesting but lack business connection, or business-relevant but weakly evidenced, are flagged accordingly.

**User type analysis in insights:** Some of the most valuable insights are comparative — they surface that different user types have fundamentally different needs, mental models, or experiences. The insight synthesis should actively look for these comparative patterns, not just report themes per group. Insights should be clearly scoped: does this apply to all user types, one specific group, or is the contrast itself the finding?

**Output:** Insight report in `02-analysis/` with full evidence trail linking each insight back through themes, codes, observations, and quotes to specific transcript moments.

**Human review:** Researcher evaluates insight quality. Are insights specific and actionable? Are they well-supported by the evidence? Do any overreach beyond what the data shows? Is the significance ranking appropriate? Are business relevance assessments accurate given the researcher's knowledge of the engagement context?

---

## Key Design Choices

1. **Single skill, multiple methodologies.** The technique is method-agnostic. It operates on cleaned transcripts regardless of source methodology. Method-specific interpretation comes from the researcher's review at each checkpoint.

2. **Multi-pass with human review.** Each pass has a defined input, output, and review checkpoint. Aligns with the architecture decision in the research system plan.

3. **Inter-rater reliability via parallel agents.** Passes 2 (coding) and 3 (theme formation) run three independent agents simultaneously. A convergence document surfaces agreements and divergences for researcher review. This hedges against AI pattern-fixation and provides a reliability signal analogous to multi-coder agreement in traditional qualitative research.

4. **Auditable evidence trail.** Every insight traces back: insight -> theme -> codes -> observations -> quotes -> transcript location. Quotations are verbatim with a 100% match guarantee against source transcripts.

5. **Incremental commitment.** The researcher can stop after any pass. Each intermediate artifact has standalone value.

6. **Research-objective-aware.** Analysis is sensitized by the study's research objectives and interview guide, distinguishing expected themes from emergent findings.

7. **Business-value-weighted.** Insight significance considers both evidence strength and business relevance, reflecting the consulting context.

8. **Coding approach as researcher choice.** Before Pass 2, the researcher selects inductive, deductive+inductive, or hybrid coding — matching the approach to the study's needs.

9. **User type as an analytical dimension.** Observations are tagged with the participant's persona/archetype. Codes track user type distribution. Themes are classified as cross-group, group-specific, or divergent. Insights are scoped by user type and actively surface comparative patterns between groups.

10. **Interactive setup step.** Before analysis begins, the researcher confirms research objectives, transcript scope, coding approach, and participant-to-persona mapping. These confirmations become the authoritative configuration for the analysis run.

---

## Resolved Decisions

| Question | Decision |
|---|---|
| Pass scope | All transcripts processed at once per pass |
| Parallel agents | Passes 2 and 3 run three agents; Passes 1 and 4 single agent |
| Convergence method | Automated comparison document; researcher makes final decisions |
| Coding approach | Researcher chooses from three options before Pass 2 |
| Cross-study analysis | Self-contained per study for now; design outputs for future cross-study comparison |
| Output format | Markdown for now; evaluate export formats based on actual downstream needs |

---

## Next Step

User will share a colleague's thematic coding skill for comparison. We'll evaluate both approaches before committing to implementation.
