# Qualitative Analysis Technique — Structured Affinity Coding

## Context

The research system plan (Phase 4) originally defined three separate agents: qualitative coding, thematic analysis, and insight writing. We agreed to collapse these into a single analysis skill (`/analyze`) that works across methodologies (stakeholder interviews, user interviews, etc.). This document traces the development of the analytic technique: initial proposal, comparison with a colleague's thematic coding skill, what was adopted from each, and the final technique as implemented.

The technique draws from **affinity mapping** (UX design/research) and **thematic coding** (social sciences), refined through comparison with Juan Reina's thematic-coding skill (Valtech/John Deere) and CORE.md epistemic framework.

---

## Initial Proposal: Structured Affinity Coding

A four-pass hybrid that takes the evidence-trail discipline of thematic coding and the bottom-up clustering logic of affinity mapping. Each pass produces a reviewable artifact; the researcher approves before the next pass runs.

### Why hybrid, not one or the other

**Affinity mapping** is fast and intuitive but under-structured for AI-assisted work — it relies on spatial reasoning, physical manipulation, and implicit researcher judgment that doesn't translate well to a text-based agent workflow. Its strength is the inductive clustering step: start with discrete observations, group by similarity, let themes emerge.

**Thematic coding** provides the structure AI needs — explicit codes, codebooks, defined evidence links — but its full academic form (Braun & Clarke six-phase, for example) is heavier than consulting UX research typically requires. Its strength is the audit trail: every insight traces back through theme, code, and quote to a specific moment in a specific transcript.

The hybrid takes **clustering from affinity mapping** and **evidence grounding from thematic coding**, producing outputs that are rigorous enough to withstand stakeholder scrutiny but practical enough for consulting timelines.

### Hard Rule: Quotation Integrity

All quotations surfaced in the analysis must be **verbatim passages** copied directly from the source transcripts. This is a non-negotiable rule enforced across all passes:

- Quotations must never be invented, paraphrased, summarized, adjusted, or cleaned up in any way
- Every word, punctuation mark, and formatting element must match the source transcript exactly
- If a quote is truncated, the omission must be marked with `[...]`
- Every quotation must include participant ID and sufficient location information (turn/line reference) for the researcher to find and verify it in the source transcript
- If the researcher looks up a quoted passage in the original transcript, there must be a **100% match**

This rule exists as a safeguard against AI hallucination. The analysis skill instructions must treat quotation fabrication as a critical failure mode, not a soft preference.

### Setup Step: Study Context Confirmation

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

### Sensitizing Frames: Research Objectives and Business Context

**Research objectives and interview guide**

Analysis is not purely inductive. Before any coding begins, the skill reads the study's `00-context/` folder to load:
- **Research objectives** from the research plan — what questions is this study trying to answer?
- **Interview guide questions** — what specific topics were explored?

These act as a **sensitizing frame** during theme formation (Pass 3): themes that align with research objectives are expected and should be evaluated for how well the data answers the original questions. Themes that fall *outside* the research objectives should still be captured but flagged as **emergent** — unexpected patterns the data revealed that weren't part of the original inquiry.

**Business value axis**

Since this system serves consulting UX research (not academia), insight significance is evaluated along two dimensions:

1. **Strength of evidence** — How many participants? How consistent is the pattern? How vivid is the supporting data?
2. **Business relevance** — What are the implications for product decisions, strategy, or stakeholder priorities? Does this theme connect to the business goals of the engagement?

An insight that is "interesting" but disconnected from business value carries less weight as a key takeaway than one with clear implications for the product, feature, or strategy under evaluation.

**Business context sources:**
- **Project-level** (`Client/_context/`) — SOW, project briefs, business goals, stakeholder priorities. These define the broader engagement context and what the client cares about.
- **Study-level** (`{study}/00-context/`) — Research plan framing, any study-specific business context.

Both levels are loaded during the setup step and inform the business relevance assessment in Pass 4.

### The Four Passes (as originally proposed)

**Pass 1: Observation Extraction (single agent)**

Read each transcript and extract discrete observations. An observation is a standalone factual statement about something the participant said, experienced, described, or demonstrated. Each observation captures one idea — if a participant's turn covers multiple topics, it produces multiple observations.

Per observation, record:

| Field | Description |
|---|---|
| ID | Sequential (e.g., OBS-001) |
| Participant | Participant ID (e.g., P01) |
| User type | Persona or archetype this participant belongs to (from setup step) |
| Observation | 1-2 sentence summary in the researcher's voice |
| Quote | Verbatim supporting quote from the transcript (see Quotation Integrity rule) |
| Location | Participant ID + approximate turn/line reference in source transcript |

Scope: All transcripts in the study are processed in a single pass. Output: one file per transcript in `02-analysis/`, plus a combined observations file across all transcripts.

This pass is not interpretation. Observations should be descriptive and grounded — "P01 found the upload process slow because files had to be added one at a time" rather than "P01 was frustrated with the tool."

Human review: Researcher scans observations for accuracy, completeness, and appropriate granularity. **Critically: spot-check quotations against source transcripts for verbatim accuracy.**

**Pass 2: Open Coding (three parallel agents + convergence)**

Review all observations across transcripts and assign descriptive codes. Before this pass begins, the researcher selects a coding approach:

1. **Inductive only** — All codes emerge from the data. No predefined code list. Best when the study is exploratory and the researcher wants to avoid biasing the analysis.
2. **Deductive + inductive** — Start with a predefined code list (from a prior study, research hypotheses, or framework) and apply it first. Then open-code anything the predefined codes don't capture.
3. **Hybrid** — Derive initial seed codes from the research objectives and interview guide questions, then supplement with open inductive coding.

Process (per agent): Work through the combined observation list. For each observation, assign one or more codes (short labels, 2-5 words). When a new concept appears, create a new code with a brief definition. After all observations are coded, review for redundancies.

Output per agent: Coded observation table + working codebook (codes with definitions, observation counts, user type distribution per code).

Convergence step: After all three agents complete, produce a comparison document showing agreement zones, divergence zones, and unique codes.

Human review: Researcher reviews the convergence document and makes final decisions: which codes to keep, merge, rename, or discard. Produces the authoritative codebook for Pass 3.

**Pass 3: Theme Formation (three parallel agents + convergence)**

Cluster codes into themes — the core affinity mapping step applied to codes rather than raw observations. Group codes that together describe a larger pattern, behavior, need, or tension.

Sensitizing frame: Agents reference the research objectives and interview guide when forming themes. Themes that address research objectives directly are expected. Themes that emerge outside the research questions are flagged as **emergent findings**.

For each theme, write: theme name (descriptive phrase), theme description (2-3 sentences), objective alignment, constituent codes, prevalence, user type breakdown (which user types contribute, whether the pattern is universal or group-specific), and key evidence (2-3 representative verbatim quotes).

Flag **cross-group themes** (shared across user types), **group-specific themes** (concentrated in one user type), and **divergent themes** (different user types have opposing experiences).

Convergence step: Comparison document showing shared themes, divergent groupings, unique themes, and structural differences.

Human review: Researcher settles the theme structure and produces the authoritative theme map. This is the most critical review checkpoint.

**Pass 4: Insight Synthesis (single agent)**

Translate themes into actionable insight statements. An insight is a concise claim about users, their context, or their needs, grounded in the evidence.

Per insight, record:

| Field | Description |
|---|---|
| Insight | A clear, specific statement (1-2 sentences) |
| Supporting theme(s) | Which theme(s) this insight draws from |
| Objective alignment | Which research objective(s) this addresses, or "Emergent" |
| User type scope | Universal, group-specific (which group), or comparative (the contrast is the finding) |
| Strength of evidence | How many participants, how consistent the pattern |
| Business relevance | Connection to product goals, strategy, or stakeholder priorities |
| Key quotes | 2-3 representative verbatim quotes |
| Implications | What this means for design, product, or strategy |

Significance ranking: Insights ordered by overall significance, weighing both evidence strength and business relevance. The insight synthesis actively looks for comparative patterns between user types.

### Original Key Design Choices

1. **Single skill, multiple methodologies.** Method-agnostic; operates on cleaned transcripts regardless of source methodology.
2. **Multi-pass with human review.** Each pass has defined input, output, and review checkpoint.
3. **Inter-rater reliability via parallel agents.** Passes 2 and 3 run three independent agents with convergence documents.
4. **Auditable evidence trail.** Insight -> theme -> codes -> observations -> quotes -> transcript location. Verbatim quotation guarantee.
5. **Incremental commitment.** Researcher can stop after any pass.
6. **Research-objective-aware.** Distinguishes expected themes from emergent findings.
7. **Business-value-weighted.** Insight significance considers both evidence strength and business relevance.
8. **Coding approach as researcher choice.** Inductive, deductive+inductive, or hybrid.
9. **User type as an analytical dimension.** Threaded through every pass.
10. **Interactive setup step.** Front-loads interpretive decisions.

### Original Resolved Decisions

| Question | Decision |
|---|---|
| Pass scope | All transcripts processed at once per pass |
| Parallel agents | Passes 2 and 3 run three agents; Passes 1 and 4 single agent |
| Convergence method | Automated comparison document; researcher makes final decisions |
| Coding approach | Researcher chooses from three options before Pass 2 |
| Cross-study analysis | Self-contained per study for now; design outputs for future cross-study comparison |
| Output format | Markdown for now; evaluate export formats based on actual downstream needs |

---

## Comparison: Structured Affinity Coding vs. Juan Reina's Thematic Coding Skill

Juan Reina's skill (Valtech/John Deere, 2026-03-02) was reviewed alongside its supporting materials: SKILL.md (9-step workflow), REFERENCE.md (methodology depth), EXAMPLES.md (complete worked example), TEMPLATE.md (blank output templates), and scripts/code_themes.py (Python evidence extraction tool). The skill also references CORE.md, an epistemic framework that applies across all skills in Juan's research system.

### Where Juan's skill was stronger

**1. Codebook rigor.** Each theme in Juan's codebook requires: name, definition, inclusion criteria, exclusion criteria, examples, and negative cases. Our initial proposal defined themes with a name, description, and constituent codes but didn't formalize the inclusion/exclusion boundary. Inclusion/exclusion criteria are what make coding decisions replicable — without them, the same code applied by different agents could mean different things.

**2. Negative case analysis.** Juan dedicates an entire step (Step 7) to deliberately searching for evidence that contradicts each theme. Our initial proposal had no equivalent. A theme with zero negative cases would pass review unchallenged, whereas Juan's skill treats that as a red flag: "a theme with zero negative cases is usually an under-examined theme, not a robust one."

**3. Analytic memos.** Juan requires concurrent documentation of analytical reasoning — why codes were assigned, why clusters were named, why boundaries were drawn. Every decision leaves a trail. Our initial proposal produced output artifacts but didn't document the reasoning behind them. For AI-assisted analysis, memos let the researcher see *how* the agent interpreted the data, not just what it produced.

**4. Evidence type differentiation.** Juan distinguishes observations, quotes, and pain points as different evidence types that carry different analytical weight and call for different coding methods. Our initial proposal treated everything as "observations" with attached quotes.

**5. Coding method selection.** Juan references Saldana's coding methods — Descriptive, In Vivo, Process, Emotion, Values, Versus — with guidance on matching method to data type. A "versus code" like `EFFICIENCY VS. CONTROL` captures something fundamentally different from a descriptive code like `SLOW UPLOAD`. Our initial proposal didn't address coding method selection.

**6. Code / Category / Theme hierarchy.** Juan draws a formal three-level distinction from Saldana (2021): codes label individual data points, categories group codes (his TC-XX entries), and themes are interpretive propositional claims. Our initial proposal collapsed this — using "codes" and "themes" without precision about the analytical level.

**7. Residual and minority theme handling.** Juan has explicit protocols for units that don't fit any theme (RESIDUAL tag) and themes with thin evidence (minority themes). Residuals that cluster (3+) become new themes. Minority themes are flagged but not suppressed. Our initial proposal mentioned "orphan codes" but didn't formalize handling.

**8. Quality gates.** Juan ends with a concrete checklist of conditions that must be true before the analysis is complete. Our initial proposal relied on human review at each pass but had no formal completion criteria.

**9. Common pitfalls.** Juan documents named failure modes ("galaxy-brained themes" that subsume 40% of data, "dust-bin themes" called Miscellaneous, "one-voice themes" from a single participant) with detection criteria. These are especially useful as AI agent guard rails.

**10. Machine-readable output.** Juan produces a CSV coded dataset alongside the markdown codebook, plus optional JSON for cross-study querying. Our initial proposal was markdown-only.

### Where our proposal was stronger

**1. Quotation integrity rule.** Our proposal has a hard rule against fabricated or paraphrased quotes — verbatim only, 100% match against source transcripts. Juan's skill preserves original wording but doesn't call out AI hallucination as an explicit failure mode. For an AI-assisted workflow where agents do the coding independently, this safeguard is critical.

**2. Business value as an analytical axis.** Our proposal evaluates insight significance against business goals, pulling context from both project-level and study-level documents. Juan's skill evaluates themes on evidence strength and cross-participant coverage but not on their implications for the engagement's business objectives.

**3. Research objective sensitization.** Our proposal classifies themes as objective-aligned or emergent, using the research plan and interview guide as a sensitizing frame. Juan's skill is purely data-driven — themes emerge from evidence without reference to what the study set out to learn. For applied UX research, knowing which themes answer the research questions and which are unexpected is valuable for stakeholder communication.

**4. Inter-rater reliability via parallel agents.** Our proposal runs three independent agents on the most interpretive passes and produces convergence documents. Juan addresses IRR as an optional lightweight protocol (20% sample, 80% agreement target) suited for human coders. Our approach is more ambitious and more native to AI-assisted workflows.

**5. Insight synthesis as a formal output.** Our proposal has a dedicated pass that translates themes into insight statements with business relevance, user type scope, and implications. Juan's skill stops at the theme summary (Step 8) or optionally produces interpretive theme statements (Step 9). The insight synthesis pass directly serves the consulting deliverable need.

**6. Interactive setup step.** Our proposal front-loads interpretive decisions: confirm research objectives, select transcripts, choose coding approach, map participants to personas. Juan's skill starts with an input audit but doesn't interactively confirm the analytical configuration.

**7. User type as a first-class dimension throughout.** Our proposal threads user type through every pass — evidence units tagged, codes tracked by distribution, themes classified as cross-group/group-specific/divergent, insights scoped by user type. Juan has cross-persona analysis in REFERENCE.md with role concentration tracking, but it's a post-hoc step rather than woven into the workflow from the start.

### Structural differences (neither better, different)

**Granularity of workflow steps.** Juan has 9 steps; we proposed 4 passes (later 5). Juan's granularity gives more precise control but would create heavier human review burden if each step required approval. Our pass structure is coarser but maps better to the "run, review, approve, continue" agent workflow.

**Input format.** Juan expects structured session notes (observations[], quotes[], pain_points[] from a session-note-taking skill). Our system expects cleaned markdown transcripts from the `/import` skill. Different data preparation pipelines, different input assumptions.

**Supporting materials.** Juan provides REFERENCE.md (deep methodology guidance), EXAMPLES.md (complete worked example), TEMPLATE.md (blank structures), and a Python extraction script. Our initial proposal was a single document. Juan's materials represent significant analytical depth worth creating equivalents for.

**System context.** Juan's skill references CORE.md (epistemic framework) and PIPELINE.md (upstream/downstream handoffs) — it operates within a broader research OS. Our skill operates within a narrower system (Obsidian vault + Claude Code skills) with different integration points.

### Recommendations adopted

All of the following were incorporated into the final technique:

1. **Codebook structure with inclusion/exclusion criteria** — the single biggest quality improvement; makes coding decisions replicable and convergence review more precise
2. **Negative case analysis as a dedicated pass** — added as Pass 4 between theme formation and insight synthesis, running after researcher-approved categories are settled
3. **Analytic memos** — concurrent reasoning documentation at every pass as a supplementary output; audit mechanism for AI decisions
4. **Evidence type differentiation** — behavioral / attitudinal / pain point tagging in Pass 1, carried through analysis
5. **Residual and minority theme protocols** — RESIDUAL tags, 3+ clustering rule, minority category flagging
6. **Quality gates** — explicit completion checklist
7. **Code / category / theme hierarchy** — Saldana's three analytical levels formalized
8. **Common pitfalls as agent guard rails** — galaxy-brained categories, dust-bin categories, fabricated quotes, etc.

Additionally, five analytic principles were adapted from Juan's CORE.md epistemic framework: categories are constructed (not discovered), context before compression, edge and variance scan, plural interpretations, and care check.

---

## Final Technique: Structured Affinity Coding (as implemented in `/analyze`)

A five-pass hybrid with analytic principles, interactive setup, and human review between each pass.

### Analytic principles (standing instructions across all passes)

1. **Categories are constructed, not discovered.** Every code, category, and insight is an analytical decision — not an objective truth. Present categorizations as constructed interpretations, not discovered facts.
2. **Context before compression.** When extracting units from conversation and clustering into codes/categories, context is lost. Note the cost in memos — qualifiers, hesitation, power dynamics, lived experience that resists reduction.
3. **Edge and variance scan.** At every stage, check who is absent from a pattern and whose experience would complicate it. Minority cases are not noise.
4. **Plural interpretations.** Major insights carry at least two plausible readings. Flag uncertainty zones. State what is observed versus inferred.
5. **Care check.** Before finalizing insights, consider who bears the consequences. Who could be harmed by misinterpretation? Are insights presented with more certainty than the evidence warrants?

### Analytical levels (Saldana, 2021)

| Level | What it is | Where it appears |
|---|---|---|
| **Code** | Label assigned to a single evidence unit — names what it's about | Pass 2 |
| **Category** | Grouping of codes sharing a conceptual pattern — descriptive | Pass 3 |
| **Insight** | Interpretive propositional claim grounded in categories — makes a claim | Pass 5 |

### Quotation integrity (hard rule)

All quotations verbatim from source transcripts. 100% match. No invention, paraphrase, or adjustment. Truncation marked with `[...]`. Every quote traceable to participant ID and transcript location. Fabrication is a critical failure.

### Setup step

Interactive confirmation before analysis begins:
1. Confirm research objectives from research plan
2. Confirm transcripts to include (document any exclusions)
3. Choose coding approach (inductive / deductive+inductive / hybrid)
4. Confirm participant-to-persona mapping

Context loaded from project level (`Client/_context/`) and study level (`{study}/00-context/`).

### Pass 1: Evidence Extraction (single agent)

Extract discrete evidence units from transcripts. Each unit tagged with participant, user type, evidence type (behavioral / attitudinal / pain point), observation summary, verbatim quote, and transcript location. Atomic decomposition rules govern splitting. Analytic memo documents extraction decisions and compression risks.

### Pass 2: Open Coding (three parallel agents + convergence)

Assign descriptive codes to every evidence unit. Each code has: name, definition, inclusion criteria, exclusion criteria, examples, evidence count, user type distribution. Three agents code independently. Convergence document surfaces agreement zones, divergences, unique codes, and residual comparison. Researcher finalizes the authoritative codebook. Guard rail checks enforce: no >25% concentration, no dust-bin codes, no solution names, no silently dropped units.

### Pass 3: Theme Formation (three parallel agents + convergence)

Cluster codes into categories (TC-XX). Each category has: name, definition, inclusion/exclusion criteria, objective alignment, constituent codes, prevalence, user type breakdown, role concentration, key evidence. Handles residuals (3+ cluster into new categories), overloaded categories (split), and minority categories (flag, don't suppress). Three agents form categories independently. Convergence document compared. Researcher settles the authoritative category map.

### Pass 4: Negative Case Analysis (single agent)

Adversarial search against researcher-approved categories. For each category: state dominant pattern, search for contradicting evidence, assess impact (revise definition / propose split / retain and document). Zero negative cases explicitly noted with sample size assessment. Runs after categories are settled — not by the agents that formed them.

### Pass 5: Insight Synthesis (single agent)

Translate categories into propositional insight statements. Each insight includes: claim, supporting categories, objective alignment, user type scope (universal / group-specific / comparative), strength of evidence, negative case qualifications, business relevance, key quotes, alternative interpretation, and implications. Ranked by significance (evidence strength x business relevance). Care check applied before finalizing.

### Quality gates

Explicit completion checklist covering: all transcripts processed, all units coded or tagged, codebook completeness, negative cases documented, user type breakdowns, memos maintained, quotation accuracy verified, insights propositional, business relevance assessed.

---

## Key Design Choices (final)

1. Single skill, multiple methodologies
2. Five-pass workflow with human review (expanded from four after adding negative case analysis)
3. Inter-rater reliability via three parallel agents on Passes 2 and 3
4. Auditable evidence trail (insight -> category -> code -> evidence unit -> quote -> transcript)
5. Quotation integrity as a hard rule against AI hallucination
6. Research-objective-aware (objective-aligned vs. emergent themes)
7. Business-value-weighted insight significance
8. Coding approach as researcher choice (inductive / deductive+inductive / hybrid)
9. User type as a first-class analytical dimension across all passes
10. Interactive setup step with context confirmation
11. Codebook rigor with inclusion/exclusion criteria (adopted from Juan's skill)
12. Negative case analysis as a dedicated adversarial pass (adopted from Juan's skill)
13. Analytic memos as supplementary output at every pass (adopted from Juan's skill)
14. Evidence type differentiation — behavioral / attitudinal / pain point (adapted from Juan's skill)
15. Residual and minority handling protocols (adopted from Juan's skill)
16. Quality gates as explicit completion checklist (adopted from Juan's skill)
17. Three-level analytical hierarchy — code / category / insight (Saldana, via Juan's skill)
18. Analytic principles from CORE.md — constructed categories, context before compression, edge/variance scan, plural interpretations, care check (adapted from Juan's epistemic framework)

---

## Resolved Decisions

| Question | Decision |
|---|---|
| Pass scope | All transcripts processed at once per pass |
| Parallel agents | Passes 2 and 3 run three agents; Passes 1, 4, and 5 single agent |
| Convergence method | Automated comparison document; researcher makes final decisions |
| Coding approach | Researcher chooses from three options during setup |
| Negative case analysis | Own pass (Pass 4) after researcher-approved category structure |
| Analytic memos | Supplementary output at every pass; not a separate review checkpoint |
| Evidence types | Behavioral / Attitudinal / Pain point; tagged in Pass 1 |
| Cross-study analysis | Self-contained per study for now; design outputs for future cross-study comparison |
| Output format | Markdown for now; evaluate export formats based on actual downstream needs |

---

## Acknowledgments

This technique incorporates analytical methods and structural elements from Juan Reina's thematic-coding skill (Valtech/John Deere, 2026-03-02), particularly: codebook structure with inclusion/exclusion criteria, negative case analysis protocol, analytic memo practice, evidence type differentiation, residual and minority theme handling, quality gates, common pitfall guard rails, and the code/category/theme hierarchy drawn from Saldana (2021). The analytic principles section adapts Juan Reina's CORE.md epistemic framework (MIT license).
