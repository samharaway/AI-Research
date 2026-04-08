# UX Research System — Planning Document

**Status:** In progress  
**Last updated:** 2026-04-08  
**Owner:** Sam Haraway, Valtech

---

## Current State

*Last updated: 2026-04-08*

### Completed
- Obsidian vault created at `~/AI-Research`
- Obsidian MCP connected to Claude Code via two community plugins: Local REST API + MCP Tools; registered as `obsidian-mcp-tools`
- Planning document created in Obsidian (`research-system-plan.md`)
- GitHub repo created at github.com/samharaway/AI-Research (private)
- Local vault initialized as git repo and connected to remote
- `.gitignore` added (excludes `.obsidian/` and `.DS_Store`)
- Stale duplicate MCP server entry (`obsidian`) removed from Claude Code config
- GitHub CLI authenticated as `samharaway` (Valtech account)

### In Progress
- Phase 1: Foundation — vault template folder scaffold not yet created; bootstrap skill not yet written; first commit not yet pushed

### Next Step
Create the vault folder scaffold, then do the first commit and push to GitHub.

---

## Vision

An AI-assisted UX research system that supports the full research lifecycle — from planning through synthesis and readout — using Claude Code as the primary platform. The system is designed to be:

- **Replicable:** Any researcher or team member can set it up on a new client project using a single bootstrap skill, with minimal prior technical knowledge
- **Client-scoped:** Each client engagement lives in its own Obsidian vault, maintaining clean data separation and NDA compliance
- **Methodology-driven:** Skills and agents encode repeatable research methods, not one-off scripts
- **Human-in-the-loop:** Agents support analysis but do not replace researcher judgment — especially in synthesis, where multi-pass workflows require explicit human review and sign-off between steps

---

## Architecture Decisions

### Obsidian as the research workspace
Obsidian is the primary interface for storing and navigating research artifacts. Claude Code connects to Obsidian via MCP, enabling reading, writing, and searching notes programmatically.

**Required Obsidian community plugins (both are needed):**
- **Local REST API** (Adam Coddington) — runs inside Obsidian and exposes the vault over a local HTTP API on port 27123/27124; generates the API key
- **MCP Tools** (Jack Steam) — provides the MCP server binary that Claude Code connects to; the binary communicates with Local REST API under the hood using the API key

These two plugins work in tandem. Local REST API is the underlying transport; MCP Tools is the bridge to Claude Code's MCP protocol. Only one MCP server entry is registered with Claude Code (`obsidian-mcp-tools`).

### One vault per client
Each client engagement gets its own Obsidian vault. This maintains clear data boundaries, supports NDA compliance, and keeps client work fully isolated.

### Skills and agents are global, not client-specific
Claude Code skills (`.claude/skills/`) and agents (`.claude/agents/`) encode research methodology, which does not change per client. They are universal. What changes per study is the context and data passed to them.

Skills and agents are **symlinked into each vault's `_shared/` folder** so researchers can review and reference them inside Obsidian without leaving the workspace.

### Consistent study folder structure across all research methods
All study types — stakeholder interviews, user interviews, usability tests, etc. — use the same top-level folder structure. Method-specific variation is handled by templates, not by changing the structure itself. This keeps agent behavior predictable and the researcher experience consistent.

### External documents imported as markdown
Client materials that originate elsewhere (SOWs, PDFs, DOCX files, web pages) are converted to markdown and stored in the vault. Direct integrations with client systems (SharePoint, Confluence, etc.) are explicitly out of scope — these vary per client and introduce dependencies that cannot be assumed. A document import skill will handle conversion.

### GitHub for distribution
The full system — vault template, skills, agents, methods docs, and bootstrap skill — lives in a GitHub repository. Colleagues clone the repo to set up a new client vault. A GitHub account is a required prerequisite.

**GitHub repo:** github.com/samharaway/AI-Research (private)  
**GitHub org:** `[PLACEHOLDER — resolve with Valtech IT]`

### Multi-pass synthesis with human review
Qualitative data synthesis is performed by agents trained in specific analytic techniques (e.g., qualitative coding, thematic analysis). Agents complete analysis in discrete passes, producing a reviewable output after each pass. The researcher reviews and confirms before the agent proceeds to the next pass. This approach is designed to:
- Keep the human in the loop at each interpretive step
- Increase reliability and repeatability of results
- Create an auditable record of how findings were developed

---

## Vault Structure

```
[Vault Root]/              ← one vault per client engagement
  _shared/
    methods/               ← synthesis protocols, analysis frameworks (reusable across studies)
    templates/             ← blank scaffolding by study type (interview, usability test, etc.)
    skills/                ← symlinks to ~/.claude/skills/*/SKILL.md
    agents/                ← symlinks to ~/.claude/agents/*/AGENT.md
  Client/
    _context/              ← SOW, stakeholder map, project goals, timelines, kick-off materials
    Studies/
      YYYY-[method]-[topic]/
        00-context/        ← research plan, interview guide, prior research context
        01-data/           ← transcripts and raw data stored as markdown files
        02-analysis/       ← agent pass outputs, coding, synthesis working notes
        03-outputs/        ← draft deliverables (final artifacts exported to Figma, Miro, slides)
```

---

## Build Phases

The build phases below are a framework for iterative development, not a waterfall. The intended execution path is:

1. Stand up the foundation (Phase 1)
2. Build end-to-end for **one methodology first** as a proof of concept — including its methods doc, template, relevant skills, and agents (Phases 2–4)
3. Iterate on that first methodology until the workflow is reliable
4. Add additional methodologies one at a time using the same pattern

### Phase 1: Foundation
- [ ] Create vault template (folder scaffold with placeholder README files)
- [ ] Write bootstrap skill — interactive setup guide covering: GitHub clone, Obsidian install, plugin setup (Local REST API + MCP Tools), MCP registration with Claude Code, vault initialization, symlink creation
- [ ] Set up GitHub repository to host the system

### Phase 2: Shared methodology layer (per methodology, repeat as needed)
- [ ] Write methods doc for the methodology (synthesis protocol, analytic approach)
- [ ] Build study template for the methodology (pre-populated folder scaffold)

**Target methodologies (in intended build order):**
- [ ] Stakeholder interviews
- [ ] User interviews
- [ ] Concept evaluation
- [ ] Usability testing
- [ ] Additional methods TBD

### Phase 3: Skills (per methodology or cross-cutting)
- [ ] Document import skill — convert PDFs, DOCX, and web pages to markdown and place in the correct vault folder
- [ ] Research planning skill — help structure a research plan for a given study
- [ ] Interview guide skill — generate or refine interview guides based on research objectives
- [ ] Additional skills TBD as research workflows mature

### Phase 4: Agents (per methodology, repeat as needed)
- [ ] Qualitative coding agent — first-pass open coding of interview transcripts
- [ ] Thematic analysis agent — identify and organize themes across coded data
- [ ] Insight writing agent — draft insight statements from synthesized themes
- [ ] Additional agents TBD; all designed for multi-pass workflows with human review between passes

### Phase 5: Hardening and sharing
- [ ] Test full setup flow with a colleague unfamiliar with the system
- [ ] Refine bootstrap skill based on gaps found in testing
- [ ] Document prerequisites (GitHub account, Mac, Obsidian)
- [ ] Resolve GitHub org placeholder

---

## Open Questions

- **GitHub org:** Sam's Valtech GitHub account does not currently show org/enterprise membership despite being work-email-linked. Needs resolution with Valtech IT before the repo can be hosted under the org.
- **Colleague prerequisites:** Bootstrap skill assumes Mac. Windows support is out of scope for now but may need to be revisited depending on team composition.
- **Template depth:** How detailed should study templates be on first release? Start minimal and iterate, or build out fully before sharing?

---

## Out of Scope (explicitly)

- Direct integrations with client systems (Confluence, SharePoint, Jira, etc.)
- Windows support (initial release)
- Cross-client research or shared data across vaults
