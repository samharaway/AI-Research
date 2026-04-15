# UX Research System — Planning Document

**Status:** In progress  
**Last updated:** 2026-04-15  
**Owner:** Sam Haraway, Valtech

---

## Current State

*Last updated: 2026-04-15*

### Completed
- Obsidian vault created at `~/AI-Research`
- Obsidian MCP connected to Claude Code via two community plugins: Local REST API + MCP Tools; registered as `obsidian-mcp-tools`
- Planning document created in Obsidian (`research-system-plan.md`)
- GitHub repo created at github.com/samharaway/AI-Research (private)
- Local vault initialized as git repo and connected to remote
- `.gitignore` updated (excludes `.obsidian/`, `.DS_Store`, and `Client/` — participant data stays local)
- Stale duplicate MCP server entry (`obsidian`) removed from Claude Code config
- GitHub CLI authenticated as `samharaway` (Valtech account)
- Vault folder scaffold created (`_shared/`, `Client/`, and all subfolders with README files)
- Initial commit pushed to GitHub
- `obsidian-mcp-setup` skill updated to V2 based on colleague feedback
- Bootstrap skill (`/ux-research-setup`) written and committed to repo at `.claude/skills/ux-research-setup/SKILL.md`
- **Phase 1 complete**
- README updated with full pre-clone bootstrap steps (Xcode CLT, Homebrew, gh) so colleagues can onboard from a single document
- `/new-study` skill written — initializes a study folder with standard subfolder structure and placeholder files for research plan and interview guide
- `/import` skill written — handles transcripts (Teams HTML, DOCX, PDF) via 7-step cleaning pipeline, and context documents via conversion pipeline; routes to correct vault location
- `_shared/skills/` populated with file copies of all skills for Obsidian visibility (symlinks don't work in Obsidian)
- Bootstrap skill updated with Phase 8: installs research skills as global symlinks to `~/.claude/skills/` so they are available from any directory and auto-update on `git pull`
- `/import` and `/new-study` tested with real study data (test-study-04-2026); first transcript successfully imported and cleaned
- `/import` skill significantly improved based on real-use testing:
  - Review and approval checkpoint added — presents all corrections, flags, and questions before saving
  - Backchannel interjection removal — short interviewer responses mid-participant-turn are removed; ambiguous fragments flagged `[?]`
  - Two-path cleaning pipeline: detects whether source transcript has speaker labels; labeled path standardizes existing labels; inferred path applies inference heuristics (question/answer patterns, name mentions, content context, turn length) followed by anti-collapse validation and name-mention disambiguation
  - Transcript saves use the filesystem `Write` tool instead of Obsidian MCP — avoids timeouts on large files
- `obsidian-mcp-setup` and `phase-complete` skills added to repo (were local-only previously)
- Skill symlink architecture implemented: `~/.claude-personal/skills/` now contains symlinks to `.claude/skills/` in the repo; git is the single source of truth, personal instance auto-updates
- README updated with current capabilities

### In Progress
- Phase 3: Skills — `/import` and `/new-study` complete and tested; research planning and interview guide skills remaining

### Next Step
Proceed to Phase 2 (methods doc and study template for stakeholder interviews) or continue Phase 3 with the research planning or interview guide skill.

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

> **Known issue:** The MCP Tools binary is not always auto-generated on first enable. Users must check the plugin settings for an "Install Server" button and click it if present.

### One vault per client
Each client engagement gets its own Obsidian vault. This maintains clear data separation, supports NDA compliance, and keeps client work fully isolated.

### Skills and agents are global, not client-specific
Claude Code skills (`.claude/skills/`) and agents (`.claude/agents/`) encode research methodology, which does not change per client. They are universal. What changes per study is the context and data passed to them.

Skills and agents are **copied into each vault's `_shared/` folder** so researchers can review and reference them inside Obsidian without leaving the workspace. (Obsidian does not follow symlinks, so copies are used instead.)

Skills are also installed globally via symlinks from `~/.claude/skills/` pointing to the vault's `.claude/skills/` directory. This makes skills available from any Claude Code session regardless of working directory, and ensures they auto-update when the vault is updated via `git pull`. The bootstrap skill handles this installation in Phase 8.

The personal Claude Code instance (`~/.claude-personal/skills/`) uses directory-level symlinks pointing directly to `.claude/skills/` in this repo. Editing a skill file in the repo updates both the project-level and personal-instance copies simultaneously. The `_shared/skills/` Obsidian copies remain separate files and must be kept in sync manually.

### Bootstrap skill distribution
The bootstrap skill (`/ux-research-setup`) lives in the repo at `.claude/skills/ux-research-setup/SKILL.md`. When a colleague clones the repo and opens Claude Code from that directory, the skill is available as a project-level skill. It handles the full first-time setup: tools, GitHub auth, vault initialization, Obsidian plugins, and MCP registration.

### Consistent study folder structure across all research methods
All study types — stakeholder interviews, user interviews, usability tests, etc. — use the same top-level folder structure. Method-specific variation is handled by templates, not by changing the structure itself. This keeps agent behavior predictable and the researcher experience consistent.

### External documents imported as markdown
Client materials that originate elsewhere (SOWs, PDFs, DOCX files, web pages) are converted to markdown and stored in the vault. Direct integrations with client systems (SharePoint, Confluence, etc.) are explicitly out of scope — these vary per client and introduce dependencies that cannot be assumed. The `/import` skill handles conversion.

### Client data is local-only
The `Client/` directory is excluded from version control via `.gitignore`. Participant transcripts, study data, and other client materials live on the researcher's local machine only and are never pushed to GitHub. The repo tracks system infrastructure (skills, templates, methods docs, vault structure) but not research data.

### GitHub for distribution
The full system — vault template, skills, agents, methods docs, and bootstrap skill — lives in a GitHub repository. Colleagues clone the repo to set up a new client vault. A GitHub account is a required prerequisite.

**GitHub repo:** github.com/samharaway/AI-Research (private)  
**GitHub org:** `[PLACEHOLDER — resolve with Valtech IT]`

### MCP server directory scoping
The `claude mcp add` command must be run from the same directory the user launches Claude Code from. If run from the wrong directory, the MCP server is registered to the wrong scope and won't appear. The bootstrap skill asks users for their Claude workspace directory upfront and verifies the `project:` path in the confirmation output.

### Multi-pass synthesis with human review
Qualitative data synthesis is performed by agents trained in specific analytic techniques (e.g., qualitative coding, thematic analysis). Agents complete analysis in discrete passes, producing a reviewable output after each pass. The researcher reviews and confirms before the agent proceeds to the next pass. This approach is designed to:
- Keep the human in the loop at each interpretive step
- Increase reliability and repeatability of results
- Create an auditable record of how findings were developed

---

## Vault Structure

```
[Vault Root]/              ← one vault per client engagement
  .claude/
    skills/                ← project-level skills (including bootstrap skill)
  _shared/
    methods/               ← synthesis protocols, analysis frameworks (reusable across studies)
    templates/             ← blank scaffolding by study type (interview, usability test, etc.)
    skills/                ← copies of .claude/skills/*/SKILL.md (for Obsidian visibility)
    agents/                ← copies of .claude/agents/*/AGENT.md (for Obsidian visibility)
  Client/
    _context/              ← SOW, stakeholder map, project goals, timelines, kick-off materials
    Studies/
      TITLE-MM-YYYY/
        00-context/        ← research plan, interview guide, prior research context
        01-data/           ← transcripts and raw data stored as markdown files
        02-analysis/       ← agent pass outputs, coding, synthesis working notes
        03-outputs/        ← draft deliverables (final artifacts exported to Figma, Miro, slides)
```

---

## Build Phases

The build phases below are a framework for iterative development, not a waterfall. The intended execution path is:

1. Stand up the foundation (Phase 1) ✓
2. Build end-to-end for **one methodology first** as a proof of concept — including its methods doc, template, relevant skills, and agents (Phases 2–4)
3. Iterate on that first methodology until the workflow is reliable
4. Add additional methodologies one at a time using the same pattern

### Phase 1: Foundation ✓
- [x] Create vault template (folder scaffold with README files)
- [x] Set up GitHub repository
- [x] Write bootstrap skill (`/ux-research-setup`)

### Phase 2: Shared methodology layer (per methodology, repeat as needed)
- [ ] Write methods doc for stakeholder interviews
- [ ] Build study template for stakeholder interviews

**Target methodologies (in intended build order):**
- [ ] Stakeholder interviews ← starting here
- [ ] User interviews
- [ ] Concept evaluation
- [ ] Usability testing
- [ ] Additional methods TBD

### Phase 3: Skills (per methodology or cross-cutting)
- [x] Study initialization skill (`/new-study`) — creates study folder structure with placeholder files
- [x] Document import skill (`/import`) — converts and cleans transcripts (Teams HTML, DOCX, PDF) and context documents; routes to correct vault location
- [ ] Research planning skill — help structure a research plan for a given study
- [ ] Interview guide skill — generate or refine interview guides based on research objectives
- [ ] Additional skills TBD as research workflows mature

### Phase 4: Agents (per methodology, repeat as needed)
- [ ] Qualitative coding agent — first-pass open coding of interview transcripts
- [ ] Thematic analysis agent — identify and organize themes across coded data
- [ ] Insight writing agent — draft insight statements from synthesized themes
- [ ] Additional agents TBD; all designed for multi-pass workflows with human review between passes

### Phase 5: Hardening and sharing
- [x] Document prerequisites — README now covers full pre-clone bootstrap (Xcode CLT, Homebrew, gh)
- [x] Refine bootstrap skill based on gaps found in testing — Phase 8 added for global skill installation
- [ ] Test full setup flow with a colleague unfamiliar with the system (partially tested — surfaced README gap, now fixed)
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
