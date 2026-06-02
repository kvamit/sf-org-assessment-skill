# Execution Phases

---

## Phase 0 — Scope Confirmation (STOP — Two-step selection)

Scope selection is a **two-step conversation**. Do not skip or combine the steps.

### Step 0a — Domain selection

Present the six domains and ask the user which ones to include:

> "Which domains should this assessment cover for **[Customer / Org Name]**?
>
> | # | Domain | Code |
> |---|--------|------|
> | 1 | 📦 Data Management | DM |
> | 2 | 🔒 Security & Visibility | SV |
> | 3 | 🔗 Integration Architecture | IA |
> | 4 | ⚡ Performance & Scalability | PS |
> | 5 | ⚙️ Configuration & Development | CD |
> | 6 | 🏛 Strategy & Governance | SG |
>
> Reply with the numbers or codes (e.g. `1, 4, 5` or `DM, PS, CD`)."

**Wait for the user's domain selection before proceeding.**

### Step 0b — Sub-domain selection (one domain at a time)

For **each selected domain**, show only that domain's sub-domains and ask which to include. Do this one domain at a time — do not show all domains at once.

Example for DM:

> "Which **Data Management (DM)** sub-domains should be included?
>
> | Code | Sub-Domain |
> |------|------------|
> | DM-01 | Object Fields |
> | DM-02 | Record Types, Page Layouts, Validation Rules |
> | DM-03 | Reports, Dashboards & Listviews Performance |
> | DM-04 | Object Storage |
>
> Reply with the codes, or `all` to include all of them."

Repeat for each selected domain in turn. Wait for confirmation on each before moving to the next.

### Step 0c — Scope summary and confirmation

Once all domains have been iterated, present the final scope summary and ask for confirmation:

> "Here is the confirmed assessment scope for **[Customer / Org Name]**:
> Platform: [Product 1] · [Product 2] · …
>
> | Domain | Sub-Domains selected |
> |--------|----------------------|
> | 📦 Data Management (DM) | DM-01, DM-02 |
> | ⚡ Performance & Scalability (PS) | PS-02, PS-05, PS-08 |
>
> ✅ Reply **'confirmed'** to begin evidence ingestion, or edit any row above."

**Do not proceed to Phase 1 until the user replies 'confirmed'.**

If the user edits the summary, update it and ask for confirmation again.

---

## Phase 1 — Evidence Ingestion

Read all provided artifacts and extract **concrete signals only**:
- Exact object names, field names, class names, flow names, metric values
- OrgCheck rule violations with severity scores and counts, if available
- Code Analyzer violation counts and rule names
- Splunk/performance metric values (CPU time, row counts, execution times)
- Storage volumes and record counts
- Governor limit error counts and class names

**Do not invent findings.** Every observation must cite a source: filename, object name, class name, or metric value extracted from the evidence.

---

## Phase 2 — Draft Findings to Markdown (STOP — Human Review Required)

**File path:** `docs/org-assessment/findings-review/[DOMAIN-CODE]-[SUB-CODE]-findings-draft.md`

### 2a — Check for existing draft

Before writing, check whether the file already exists.

**If the file does NOT exist** → proceed to 2b (write fresh draft).

**If the file already exists** → stop and ask the user:

> "A draft already exists for `[SUB-CODE]` (v[N], status: [Status from file]).
> How would you like to proceed?
> - **Revise** — I'll read the existing observations and update them with new evidence, keeping observations you've already approved unchanged
> - **Append** — I'll add only new observations found in the new evidence, leaving existing content untouched
> - **Replace** — I'll discard the existing draft and write a fresh one from the new evidence"

Do not proceed until the user selects an option.

- **Revise**: read the existing file, merge new evidence into existing observations (update wording, rubric, or recommendation where evidence warrants), increment the version, set status back to DRAFT.
- **Append**: read the existing file, add only net-new observation blocks below the last existing one, increment the version, set status back to DRAFT.
- **Replace**: overwrite the file entirely with a fresh draft, reset version to v1.

### 2b — Write the draft file

Use the exact structure below. The header must include `**Version**` and `**Status**` fields.

```markdown
# [Domain Code] — [Sub-Domain Name] — Findings Draft
**Assessment date:** YYYY-MM-DD
**Version:** v1
**Status:** DRAFT — Pending Review

---

## Observation [N]: [Short title, ≤ 6 words]

**Observation:**
[One sentence. Name the specific artifact. No multi-sentence bullets.]

**Impact:**
[One sentence. What fails, degrades, or costs money. Quantify where evidence supports it.]

**Recommendation (Short Term or Long Term):** `[ST]` or `[LT]`
[One sentence. Specific action tied to the named artifact.]

| Risk | Value | Effort | Priority |
|------|-------|--------|----------|
| High / Medium / Low | High / Medium / Low | High / Medium / Low | High / Medium / Low |

---
```

After writing, **stop and tell the user:**

> "I've written [N] findings to `docs/org-assessment/findings-review/[filename].md` ([version]).
> Please open the file, review each observation, and:
> - ✏️ Edit any observation, impact, recommendation, or rubric rating directly in the file
> - ➕ Add new observations using the same template structure
> - 🗑 Delete any observation that is inaccurate or out of scope
> - ✅ When done, reply **'approved'** (or 'approved with changes') to generate the HTML report"

**Do not proceed to Phase 3 until the user explicitly approves.**

When the user approves, update the file header:
- Set `**Status:** APPROVED`
- Increment `**Version:**` by one (e.g. v1 → v2 if changes were made, keep v1 if approved as-is)

---

## Phase 3 — Generate HTML Report (only after approval)

Always re-read the markdown file from `docs/org-assessment/findings-review/` immediately before generating — never use an in-memory copy from Phase 2. This ensures user edits made in the IDE are picked up.

Derive the file name from the customer / org name collected in Required Inputs:
- Lowercase, replace spaces with hyphens, strip special characters
- Format: `docs/org-assessment/[org-name]-OrgAssessment-Report.html`
- Examples: `acme-corp-OrgAssessment-Report.html`, `acme-corp-OrgAssessment-Report.html`

If the file already exists under this name, update it in place (cumulative). If a previous report exists under the old generic name `OrgAssessment-Report.html`, treat it as the same report and rename it on first write.

The HTML file is **cumulative**:
- If a previous version exists, preserve all existing domain/sub-domain panels.
- Add or **replace** only the panels for the sub-domain(s) just approved.
- Render the sub-domain's `Version` and `Assessment date` from the markdown header in small text in the sub-domain sidebar tab and panel header, so repeated iterations are visible (e.g. `DM-01 · v2 · 2026-05-20`).

**Use the collected inputs in the report header:**
- **Customer / org name** → displayed prominently in the header (e.g. "Acme Corp")
- **Platform products** → rendered as a pill row beneath the title (e.g. `Sales Cloud · FSC · Data Cloud · MuleSoft · CRMA`)

For HTML structure and component specs → [html-design.md](html-design.md)

---

## Quality Rules

| Rule | Detail |
|------|--------|
| Evidence-grounded only | Every observation names a specific artifact from the provided scans |
| No generic advice | Every recommendation is tied to a named object, class, or metric |
| One sentence per field | Observation, Impact, Recommendation are each one sentence |
| Rubric consistency | Risk × Value must logically derive Priority — see [rubric.md](rubric.md) |
| Human approval required | Never generate HTML without explicit user approval of the markdown draft |
| Cumulative HTML | Never overwrite assessed panels when adding new sub-domains |
| Extensibility | HTML structure allows new sub-domain panels without restructuring |
