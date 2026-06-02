# running-org-assessment

A CTA-level enterprise Salesforce org assessment skill. It ingests metadata scan artifacts (OrgCheck, Code Analyzer, Splunk, force-app source) and produces a self-contained HTML report: domain tabs → sub-domain sub-tabs → observation cards scored on a Risk/Value/Effort/Priority rubric.

**The skill always includes a human review checkpoint before generating HTML.** Findings are written to markdown first; you edit and approve before the report is produced.

## When to use

Trigger this skill when the user:
- Asks to assess a Salesforce org domain
- Says "org assessment" or "assess domain"
- Cites a sub-domain code (e.g. DM-01, PS-05, CD-04)
- Shares org scan artifacts (OrgCheck exports, Code Analyzer results, Splunk reports, metadata inventories)

Do **not** trigger for:
- A pure gap analysis without org artifacts → use `gap-analysis`
- A single diagram → use `generating-mermaid-diagrams`
- A targeted code fix → use the relevant `generating-*` skill

## Setup

### Project install (current setup — available in this repo)

The skill lives at `.agents/skills/running-org-assessment/` and is symlinked from `.claude/skills/running-org-assessment`. It is automatically available when working in this repo.

To replicate this setup in another project:

1. Copy the skill folder into the project's agents directory:

```bash
cp -r path/to/running-org-assessment <project-root>/.agents/skills/running-org-assessment
```

2. Create a symlink in `.claude/skills/` so Claude Code discovers it:

```bash
cd <project-root>
ln -s ../../.agents/skills/running-org-assessment .claude/skills/running-org-assessment
```

3. Restart Claude Code. The skill will appear as `/running-org-assessment` when working inside that project.

> **Tip:** Commit `.agents/skills/running-org-assessment/` to version control so the whole team gets it. The `.claude/skills/` symlink can be committed too, or added to each developer's local setup.

### Global install (available in all projects)

Copy the skill folder into your global Claude commands directory:

```bash
cp -r path/to/running-org-assessment ~/.claude/commands/running-org-assessment
```

Restart Claude Code, then run `/running-org-assessment` — it should appear in tab-completion from any project.

> **Note:** The skill references artifact paths under `docs/org-assessment/` relative to the project root. When used globally, ensure those paths exist in the project you are assessing, or provide absolute paths when prompted.

**Required artifact locations** (provide at least one):
- `docs/org-assessment/findings/Org Check Rules.xlsx` — OrgCheck violation data
- `docs/org-assessment/findings/DATA MODEL/CustomFields.xlsx` — field inventory
- `docs/org-assessment/findings/PROGRAMMATIC/Apex Classes Analysis.docx` — Apex evidence
- `docs/org-assessment/findings-review/` — markdown draft files (written by the skill during Phase 2)

**Output location:**
- `docs/org-assessment/[org-name]-OrgAssessment-Report.html` — cumulative live report

## Usage

Invoke via `/running-org-assessment` in any Claude Code session.

Before proceeding, the skill will ask you for:

**Required**
- **Customer / org name** — e.g. "United Partners" — used in report header and file naming
- **Platform products** — e.g. "Sales Cloud, FSC, Data Cloud, CRMA, Agentforce"
- **Domains / sub-domains to assess** — e.g. "DM-01, PS-02, CD-04" — see taxonomy below
- **Org artifacts** — OrgCheck exports, Code Analyzer results, Splunk, metadata inventories, force-app source
- **Org context** — LDV? Multi-org? Heavy customisation? Key managed packages?
- **Audience** — e.g. Architect, delivery lead, client stakeholders
- **Report title** — e.g. "United Partner — Salesforce Org Assessment"

**Recommended**
- **Prepared-by / date**

**Optional**
- **Focus areas per sub-domain** — e.g. "DM-01: unused fields, field limits, formula performance"

## Phase flow

```
Phase 0 — Scope confirmation
  Present proposed domains/sub-domains to user
  ↓ STOP — user edits scope if needed, then says "confirmed"

Phase 1 — Ingest
  Read org artifacts → extract concrete signals per sub-domain

Phase 2 — Draft findings
  Check for existing draft → offer Revise / Append / Replace
  Write observations to docs/org-assessment/findings-review/<sub-domain>.md
  ↓ STOP — open the file, edit observations, then say "approved"

Phase 3 — HTML report
  Re-read approved markdown → generate / update cumulative HTML report
  Output: docs/org-assessment/[org-name]-OrgAssessment-Report.html
```

## Assessment taxonomy

**DM — Data Management**
- DM-01: Object Fields
- DM-02: Record Types, Page Layouts, Validation Rules
- DM-03: Reports, Dashboards & Listviews Performance
- DM-04: Object Storage

**SV — Security & Visibility**
- SV-01: Security Health Check
- SV-02: Release Updates
- SV-03: Sharing Settings – OWD
- SV-04: Profiles and Permission Sets
- SV-05: Role Hierarchies

**IA — Integration Architecture**
- IA-01: Use of Middleware and Connectors
- IA-02: Use of Integration Patterns
- IA-03: Integration Security
- IA-04: System Administrators & Integration Users

**PS — Performance & Scalability**
- PS-01: Ownership Skew
- PS-02: Governor Limits
- PS-03: Record Locking
- PS-04: List Views Performance
- PS-05: Apex Class Performance
- PS-06: Async Apex Performance (Batches)
- PS-07: Future Performance (Async Processing)
- PS-08: Apex Trigger Performance
- PS-09: SOQL Query Performance
- PS-10: API Performance
- PS-11: Reports and Dashboards Performance
- PS-12: Visualforce Performance
- PS-13: Automations Performance

**CD — Configuration & Development**
- CD-01: Distribution of Metadata per API Version
- CD-02: Workflows, Process Builder, Flow
- CD-03: Static Resource & Custom Labels
- CD-04: Coding Violations
- CD-05: Coding Vulnerability
- CD-06: Code Smell
- CD-07: Code Performance Issues
- CD-08: Apex Best Practices: Classes & Triggers
- CD-09: Apex Best Practices: Tests

**SG — Strategy & Governance**
- SG-01: SF Technical Governance
- SG-02: DevOps Strategy
- SG-03: Design & Development Standards
- SG-04: Backup, Recovery and Archive

## Rubric

Every observation card is scored on four dimensions. Timeline tags: `[ST]` Short Term · `[LT]` Long Term

**Risk** — risk to the org if no action is taken
- High: data loss, limit breach, compliance, or integration failure
- Medium: degraded performance, reduced functionality, or maintainability concern
- Low: minor friction or low-probability future risk

**Value** — business or technical gain from implementing the recommendation
- High: improves functionality, performance, or roadmap alignment AND reduces complexity
- Medium: moderate improvement in one of the above dimensions
- Low: marginal gain or nice-to-have

**Effort** — degree of effort to implement
- High: architectural redesign, data migration, or broad user-base impact
- Medium: moderate code change, config update, or targeted data fix
- Low: simple config, field delete, or metadata cleanup

**Priority** — derived from Risk × Value
- High: High Risk + High Value → act immediately
- Medium: High Risk + Medium Value, or Medium Risk + High Value
- Low: Low Risk or Low Value regardless of effort

## Supporting files

- [taxonomy.md](taxonomy.md) — all 6 domains with sub-domain codes
- [rubric.md](rubric.md) — Risk/Value/Effort/Priority definitions and HTML pill colours
- [phases.md](phases.md) — Phase 0–3 step-by-step execution, markdown draft template, quality rules
- [html-design.md](html-design.md) — HTML design system: layout, CSS, JS, observation card template
