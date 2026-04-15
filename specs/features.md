# Features

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

## Prescriber Co-signature

* Any generated document can be submitted for co-signature to the organization's prescriber partner
* Co-signature request notifies the prescriber via email + partner portal
* Prescriber reviews classification, obligations, and document in the partner portal
* Prescriber can: approve (sign), request changes, or decline
* On approval: prescriber name, firm, and date embedded in the document; document status set to `co-signed`
* Co-signed documents display a verified badge visible to the end user
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
