# Features

## Quick Classification Path

The primary entry point for new users. No setup required. Delivers a classification result in under 3 minutes.

* Single input: system name
* 5 essential questions (one at a time): description, affected population, human override, data types, deployment scope
* Optional: 5 refinement questions — expand to improve confidence score (not required)
* Immediate result: risk level + top 3 obligations
* From result: generate document in one click, or save to registry

---

## AI System Identification Wizard

Optional path for users who want to discover all in-scope systems at once. Accessible from the empty state, dashboard, and "Add system" menu.

### Step 1 — Sector + tools (single screen)
* Sector selector (single-select, filters tool list immediately)
* Categorized tool checklist filtered by sector:
  * Productivity: Microsoft Copilot, Google Workspace AI, Notion AI, GitHub Copilot…
  * Customer service: Intercom AI, Zendesk AI, custom chatbots…
  * HR & Recruitment: CV screening tools, Workday AI, LinkedIn Recruiter…
  * Sales & CRM: Salesforce Einstein, HubSpot AI, lead scoring tools…
  * Finance: fraud detection, credit scoring, forecasting tools…
  * Custom: free-text for unlisted tools
* Helper: "Check everything you use — we'll filter what's in scope"

### Step 2 — Review and confirm (single screen)
* LLM analysis runs inline (~3s)
* In-scope tools shown as cards: risk level badge, editable description, include/exclude toggle
* Out-of-scope tools collapsed
* Batch creation on confirm

### Description Quality Check
* Runs automatically on every system save (wizard and manual)
* LLM returns a quality score (0–100) and specific improvement suggestions
* Displayed as a subtle indicator below the description field:
  * Score ≥ 60: green dot — "Good description"
  * Score < 60: amber dot — "Could be more specific" + tooltip with suggestions
* **Never blocks classification** — the user always decides whether to improve the description first
* Low quality is noted in the audit log and shown alongside the classification result

---

## AI Systems Registry

* Create / edit / archive AI systems (soft-delete only — audit trail preserved)
* Fields: name, description, use_case, data_used, autonomy_level, owner, sector
* `source` field: quick_path | wizard | manual
* `compliance_status` per system: `unclassified` | `compliant` | `needs_review` | `at_risk`
* `last_classified_at`, `last_reviewed_at` timestamps
* `description_quality_score` and `description_quality_feedback` stored per system
* Status auto-degrades to `needs_review` when:
  * A regulatory update affects the system's use case or risk category
  * The system description is updated by the user
  * More than 12 months have passed since the last classification

---

## Risk Classification

* **5 essential questions** (required): description, affected population, human override, data sensitivity, deployment context
* **5 optional refinements** (improve confidence score): sector, scale, explainability, bias testing, regulated product
* LLM classification aligned to current regulatory version
* Every classification stores:
  * `risk_level`: minimal | limited | high | unacceptable
  * `explanation`: plain-language justification (2–3 sentences)
  * `obligations`: list of applicable obligations with implementation guides
  * `confidence_score`: 0–100
  * `requires_expert_review`: true if confidence < 70 or sensitive sector
  * `triggered_by`: user | regulatory_change | system_update
  * `regulatory_version_id`
  * `is_current`
* Classification history accessible per system (tab, not default view)
* Low-confidence warning surfaced as an invitation ("Strengthen with expert review") — not as a blocker

---

## Obligations Checklist

* Generated from each classification
* Each item: title, description, implementation_guide, regulatory_reference, status (todo | in_progress | done), optional due date
* **Top 3 most critical obligations shown by default** — "Show all X" toggle
* Completion percentage visible on system page and dashboard
* Completing all obligations → status upgrades to `compliant`
* Checklist versioned: new classification creates new checklist (previous archived)

---

## Compliance Status

* Per-system badge: `Compliant` | `Needs attention` | `Not assessed`
  * (displayed label simplified; stored value: compliant | needs_review | at_risk | unclassified)
* Organization summary: count by status
* Partner portal shows health per client organization

---

## Regulatory Change Alerts

* Triggered when Compliance Intelligence Tracker detects a confirmed EU AI Act update
* Affected systems auto-flagged as `needs_review`
* Alert shown as non-intrusive banner on system page + badge in nav
* Email notification
* Workflow: pending → acknowledged → dismissed (all logged)
* Alerts Center accessible via nav badge — hidden from nav when no active alerts

---

## Prescriber Certification

Two levels allowing prescribers to start quickly and upgrade when ready.

### Level 1 — Scope Acceptance (15 minutes)
* Read the Co-signature Scope Document (no forced scroll — single checkbox acceptance)
* Unlocks co-signature for Minimal and Limited risk systems
* Badge on co-signed documents: "Scope-accepted review"
* No assessment required

### Level 2 — Full Certification (~90 minutes)
* 4 modules: EU AI Act risk levels, how to review a classification, co-signature responsibilities, sector quick-guides
* 15-question assessment, 75% pass threshold
* Immediate retry on failure (no delay)
* Unlocks co-signature for all risk levels including High
* Badge: "Certified AI Compliance Reviewer"
* Valid 12 months; renewal: 20-min module + 5-question quiz

### Co-signature Scope Document
* Defines what the prescriber confirms (system accuracy, classification appropriateness, obligations completeness)
* Defines what is excluded (guarantee of implementation, full legal opinion, replacement for own counsel)
* Versioned — prescribers re-accept on material change
* Publicly accessible on Trustixy legal page

---

## Prescriber Co-signature

* Level 1 certified prescribers can sign Minimal/Limited risk documents
* Level 2 certified prescribers can sign all risk levels
* Co-signature request notifies prescriber via email + partner portal
* Prescriber reviews on a two-panel page (document + review actions)
* **Professional notes**: optional free-text reviewer comments — included in document as "Reviewer Comments" section
* Actions: approve & sign | request changes | decline
* On approval: prescriber name, firm, certification level badge, date embedded in document
* No linked prescriber → user offered partner directory + "Download with disclaimer" fallback

---

## Compliance Document Generator

* Linked to specific classification_id
* Mandatory disclaimer block (non-removable, compact footer style)
* Header: date · risk level · regulatory version date · status
* Versioned per system; older versions accessible in history tab
* Exportable as PDF

---

## Audit Log

* Immutable, always running in the background — not surfaced in primary UX
* Accessible from system page actions menu ("View audit trail")
* Actions logged: create | update | archive | classify | obligation_updated | generate_document | signature_requested | signature_approved | signature_declined | alert_acknowledged | alert_dismissed | status_changed | wizard_completed | certification_completed | cosignature_scope_accepted
* Exportable as PDF or CSV

---

## Export

* Export compliance document (PDF) — disclaimer + signature block + regulatory version
* Export system audit trail (PDF / CSV) — from actions menu on system page
* Export organization compliance report — from dashboard settings menu
