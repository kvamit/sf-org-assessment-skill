---
name: running-org-assessment
description: "Enterprise Salesforce org assessment skill producing a self-contained tabbed HTML report with Risk/Value/Effort/Priority rubric scoring per observation. TRIGGER when: user asks to assess a Salesforce org domain, says 'org assessment', 'assess domain', cites a sub-domain code (DM-01, PS-05, CD-04, etc.), or shares org scan artifacts (OrgCheck, Code Analyzer, Splunk, metadata inventories). DO NOT TRIGGER when: user wants a pure gap analysis without org artifacts (use running-gap-analysis), a single diagram (use generating-mermaid-diagrams), or a targeted code fix (use the relevant generating-* skill)."
license: AMIT
metadata:
  version: "2.0"
---

# running-org-assessment: Enterprise Salesforce Org Assessment → HTML Report

Use this skill to produce a **CTA-level technical org assessment** grounded in actual metadata, scan artifacts, and source code evidence. Output is a single self-contained HTML file: domain tabs → sub-domain sub-tabs → observation cards with Risk/Value/Effort/Priority rubric ratings per finding.

**The skill always involves a human review checkpoint before generating HTML.** Findings are written to a markdown file first; the user edits and approves before the report is produced.

---

## Required Inputs

| Input | Required? | Notes |
|---|---|---|
| **Customer / org name** | Required | e.g. "Acme Corp" — used in the report header and file naming |
| **Platform products** | Required | Salesforce products in use, e.g. "Sales Cloud, FSC, Data Cloud, MuleSoft, CRMA, Agentforce" — scopes which findings are relevant |
| **Domains / sub-domains to assess** | Required | e.g. "DM-01, PS-02, CD-04" — see [taxonomy.md](taxonomy.md) |
| **Org artifacts** | Required | OrgCheck exports, Code Analyzer results, Splunk reports, metadata inventories, force-app source |
| **Org context** | Required | LDV? Multi-org? Heavy customisation? Key managed packages? |
| **Audience** | Required | e.g. CTO, delivery lead, client stakeholders |
| **Report title** | Required | e.g. "Acme Corp — Salesforce Org Assessment" |
| **Prepared-by / date** | Recommended | |
| **Focus areas per sub-domain** | Optional | e.g. "DM-01: unused fields, field limits, formula performance" |

---

## Phase Flow Summary

```
Phase 0: Present proposed scope (domains/sub-domains) to user
    ↓  STOP
    ↓  User edits scope if needed → replies "confirmed"
    ↓
Phase 1: Ingest artifacts → extract concrete signals
    ↓
Phase 2: Check for existing draft → ask Revise / Append / Replace if found
         Write findings to markdown draft file
    ↓  STOP
    ↓  User opens file → edits / adds / removes observations
    ↓  User replies "approved"
    ↓
Phase 3: Re-read approved markdown → generate / update HTML report (cumulative)
```

Full phase instructions → [phases.md](phases.md)

---

## Modules

| File | Contents |
|------|----------|
| [taxonomy.md](taxonomy.md) | All 6 domains and their sub-domain codes and names |
| [rubric.md](rubric.md) | Risk / Value / Effort / Priority definitions, pill colours, description lookup |
| [phases.md](phases.md) | Phase 0–3 step-by-step execution, markdown draft template, quality rules |
| [html-design.md](html-design.md) | HTML design system: layout, CSS, JS, observation card, summary panels |

---

## Reference Files

- [running-gap-analysis/SKILL.md](../running-gap-analysis/SKILL.md) — sibling skill for gap analysis without org artifacts
- `docs/org-assessment/[org-name]-OrgAssessment-Report.html` — live cumulative report (name derived from customer / org name input)
- [findings-review/](../../../docs/org-assessment/findings-review/) — markdown draft files (one per sub-domain)
- [Org Check Rules.xlsx](../../../docs/org-assessment/findings/Org%20Check%20Rules.xlsx) — OrgCheck violation data
- [CustomFields.xlsx](../../../docs/org-assessment/findings/DATA%20MODEL/CustomFields.xlsx) — field inventory
- [Apex Classes Analysis.docx](../../../docs/org-assessment/findings/PROGRAMMATIC/Apex%20Classes%20Analysis.docx) — Apex evidence
