# Features

## AI System Identification Wizard

The wizard is triggered automatically on first login and accessible at any time from the dashboard ("Discover my AI systems"). Its purpose is to solve the "step zero" problem: most SMEs don't know which of their tools qualify as AI systems under the EU AI Act.

### Step 1 — Sector selection
* User selects their primary sector (one or more): Healthcare | Finance & Insurance | HR & Recruitment | Legal | Education | Retail & E-commerce | Manufacturing | Public Sector | Other
* Sector is stored on the organization profile and used to contextualize all subsequent analysis

### Step 2 — Tool inventory checklist
* User is presented with a categorized list of common AI-powered tools, pre-populated by sector:
  * **Productivity**: Microsoft Copilot, Google Workspace AI, Notion AI, GitHub Copilot…
  * **Customer service**: Intercom, Zendesk AI, custom chatbots…
  * **HR & Recruitment**: LinkedIn Recruiter AI, Workday AI, CV screening tools…
  * **Sales & CRM**: Salesforce Einstein, HubSpot AI, lead scoring tools…
  * **Finance**: automated credit scoring, fraud detection, forecasting tools…
  * **Custom / other**: free-text field for tools not in the list
* User checks tools they use; they can also add tools not in the list manually

### Step 3 — LLM analysis & system suggestions
* For each checked tool, Trustixy runs the **Wizard Analysis** LLM prompt (see `specs/llm-prompts.md`)
* The LLM returns: whether this tool is likely in scope of the EU AI Act, a suggested risk category, and a pre-filled system description
* Results are displayed as a review screen: "We identified X systems that may require EU AI Act compliance"
* Each suggested system shows: name, likely risk level, brief explanation, pre-filled description
* User can: confirm, edit, or exclude each suggestion

### Step 4 — Batch system creation
* User confirms the list and all selected suggestions are created as AI systems in the registry with status `unclassified`
* Pre-filled descriptions are editable before confirmation
* Description quality is validated before save (see Description Quality Check below)

### Description Quality Check
* Applied on every system save (wizard and manual)
* LLM evaluates the description against a minimum quality threshold: is it specific enough to enable accurate classification?
* If quality is insufficient: inline warning with specific suggestions ("Please clarify how the system makes decisions and what data it uses")
* Classification is not blocked but the warning persists until the description meets the threshold

---

## AI Systems Registry

* Create / edit / archive AI systems (soft-delete only — audit trail preserved)
* Fields: name, description, use_case, data_used, autonomy_level, owner
* `compliance_status` per system: `unclassified` | `compliant` | `needs_review` | `at_risk`
* `last_classified_at`, `last_reviewed_at` timestamps
* Status auto-degrades to `needs_review` when:
  * A regulatory update affects the system's use case or risk category
  * The system description is updated by the user
  * More than 12 months have passed since the last classification

## Risk Classification

* Structured questionnaire (10 questions covering: use case, affected population, decision autonomy, data sensitivity, human oversight, sector)
* LLM classification aligned to the current regulatory version
* Every classification stores:
  * `risk_level`: minimal | limited | high | unacceptable
  * `explanation`: plain-language justification
  * `obligations`: list of applicable regulatory obligations
  * `confidence_score`: 0–100 — how certain the LLM is of this classification
  * `requires_expert_review`: true if confidence < 70% or if use case falls in a sensitive sector
  * `triggered_by`: user | regulatory_change | system_update
  * `regulatory_version_id`: which EU AI Act version was in effect
  * `is_current`: true for the latest classification of this system
* Full classification history accessible per system
* Classifications with `requires_expert_review: true` display a warning and prompt the user to request prescriber review

## Obligations Checklist

* Each classification produces an actionable obligations checklist (not just a document section)
* Each obligation item: title, description, regulatory reference, status (todo | in_progress | done), optional due date
* Users can update obligation status as they implement compliance measures
* Checklist completion percentage visible on the system page and dashboard
* Checklist items are versioned: a new classification creates a new checklist (previous one archived)
* Completing all obligations triggers status upgrade to `compliant`

## Compliance Status

* Per-system badge: `Compliant` | `Needs Review` | `At Risk` | `Unclassified`
* Organization-level summary: count by status, overall compliance score
* Status changes are logged in the audit trail
* Partner portal shows compliance health per client organization

## Regulatory Change Alerts

* Triggered when the Compliance Intelligence Tracker agent detects a relevant EU AI Act update
* Affected systems auto-flagged as `needs_review`
* Alert includes: what changed, which systems are affected, recommended action
* In-app notification + email
* Workflow: pending → acknowledged → dismissed (all states logged)

## Prescriber Certification

Prescribers must complete a certification before they can co-sign documents. This serves two purposes: give prescribers the knowledge to co-sign confidently, and give end users assurance that the reviewer is qualified.

### Certification program
* In-app training module: ~2 hours, covering:
  * EU AI Act fundamentals (risk levels, obligations by level, key deadlines)
  * How to evaluate an AI system classification
  * Sector-specific risk criteria (Healthcare, Finance, HR, Education)
  * Scope and limits of the co-signature act
  * Professional liability framework and insurance considerations
* Assessment: 20-question quiz, 80% pass threshold
* On pass: `certified_at` stored on partner profile, certification badge displayed on all co-signed documents
* Certification validity: 12 months — renewal required (shorter module, 30 min, focused on regulatory changes)
* Certification version tracked: a new version is issued when the EU AI Act is significantly updated

### Co-signature scope document
* Every prescriber reviews and explicitly accepts the **Co-signature Scope Document** before their first co-signature
* The scope document defines precisely what the prescriber confirms when co-signing:
  1. The system description and use case appear accurate and complete
  2. The AI-generated classification is appropriate given their professional judgment
  3. The listed obligations are applicable and complete
  4. Their review was conducted using professional care consistent with their advisory practice
* The scope document states explicitly what the co-signature does NOT cover:
  * It is not a guarantee that obligations have been or will be implemented
  * It is not a legal opinion on the organization's full regulatory compliance posture
  * It does not replace the organization's own legal counsel
* The scope document is versioned (`cosignature_scope_version`) — prescribers re-accept when it changes
* Scope document text is publicly accessible on the Trustixy legal page

## Prescriber Co-signature

* Only certified prescribers (certification valid) can co-sign documents
* Any generated document can be submitted for co-signature to the organization's prescriber partner
* Co-signature request notifies the prescriber via email + partner portal
* Prescriber reviews classification, obligations, and document in the partner portal
* **Professional notes**: prescriber can add a free-text "Reviewer comments" section (optional but recommended for complex or high-risk systems) — included in the document and PDF
* Prescriber can: approve (sign), request changes, or decline
* On approval: prescriber name, firm, certification badge, and date embedded in the document; document status set to `co-signed`
* Co-signed documents display a verified badge + certification status visible to the end user
* Co-signature is optional but recommended — prominently surfaced when `requires_expert_review: true`
* Only one active co-signature per document version; re-generation resets signature status

## Compliance Document Generator

* Generates a structured EU AI Act compliance document
* Document is linked to a specific `classification_id`
* Every document includes a mandatory, non-removable disclaimer (see Legal Scope in product.md)
* Document header: generation date, regulatory version, classification result, confidence score, signature status
* Document versions: multiple versions per system, each tied to a classification; older ones marked as superseded
* Document is exportable as PDF or text

## Audit Log

* Immutable log of all actions:
  * `create`, `update`, `archive` — system lifecycle
  * `classify` — classification run (with trigger type and confidence score)
  * `obligation_updated` — checklist item status changed
  * `generate_document` — document generated
  * `signature_requested`, `signature_approved`, `signature_declined` — co-signature events
  * `alert_acknowledged`, `alert_dismissed` — regulatory alert handling
  * `status_changed` — compliance status transitions
* Each entry: user, timestamp, action type, before/after metadata
* Exportable as PDF or CSV

## Export

* Export compliance document (PDF) — includes disclaimer, signature block, regulatory version
* Export full system audit trail (PDF / CSV)
* Export organization compliance report: all systems, statuses, classification dates, obligation completion rates
