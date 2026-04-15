# Database Schema

## organizations

* id (uuid)
* name (text)
* created_at (timestamp)

## partners

* id (uuid)
* organization_id (uuid) — the partner's own org
* tier (text) — referral | reseller | embedded | group
* white_label_config (jsonb) — logo, colors, custom domain
* commission_rate (numeric)
* status (text) — pending | active | suspended
* created_at (timestamp)

## partner_clients

* id (uuid)
* partner_id (uuid)
* client_organization_id (uuid)
* invited_at (timestamp)
* activated_at (timestamp)
* status (text) — invited | active

## users

* id (uuid)
* organization_id (uuid)
* email (text)
* role (text) — admin | member
* created_at (timestamp)

## ai_systems

* id (uuid)
* organization_id (uuid)
* name (text)
* description (text)
* use_case (text)
* data_used (text)
* autonomy_level (text)
* owner (text)
* compliance_status (text) — unclassified | compliant | needs_review | at_risk
* last_classified_at (timestamp)
* last_reviewed_at (timestamp)
* archived_at (timestamp) — null if active; systems are never hard-deleted
* created_at (timestamp)

## classifications

* id (uuid)
* ai_system_id (uuid)
* risk_level (text) — minimal | limited | high | unacceptable
* explanation (text)
* obligations (jsonb) — raw list used to generate the obligations checklist
* confidence_score (integer) — 0–100; LLM self-assessed certainty
* requires_expert_review (boolean) — true if confidence_score < 70 or sensitive sector
* triggered_by (text) — user | regulatory_change | system_update
* regulatory_version_id (uuid) — references regulatory_versions
* is_current (boolean) — true for the latest classification of this system
* created_at (timestamp)

## obligations

* id (uuid)
* classification_id (uuid)
* ai_system_id (uuid) — denormalized for easier querying
* title (text)
* description (text)
* regulatory_reference (text) — e.g. "EU AI Act Art. 13(1)"
* status (text) — todo | in_progress | done
* due_date (date) — optional
* updated_by (uuid) — user_id
* updated_at (timestamp)
* created_at (timestamp)

## documents

* id (uuid)
* ai_system_id (uuid)
* classification_id (uuid) — the classification this document is based on
* content (text)
* version (integer) — increments per system
* disclaimer_version (text) — version of the disclaimer text embedded in this document
* signature_status (text) — unsigned | pending_signature | co-signed | changes_requested
* is_current (boolean) — true for the latest document of this system
* created_at (timestamp)

## document_signatures

* id (uuid)
* document_id (uuid)
* signed_by_user_id (uuid) — the prescriber user who signed
* partner_id (uuid) — the partner firm
* action (text) — approved | changes_requested | declined
* notes (text) — optional reviewer notes
* acted_at (timestamp)
* created_at (timestamp)

## audit_logs

* id (uuid)
* organization_id (uuid) — for org-level queries
* ai_system_id (uuid) — nullable (some actions are org-level)
* user_id (uuid)
* action (text) — create | update | archive | classify | obligation_updated | generate_document | signature_requested | signature_approved | signature_declined | alert_acknowledged | alert_dismissed | status_changed
* metadata (jsonb) — before/after state, confidence score, trigger type, etc.
* created_at (timestamp)

## regulatory_versions

* id (uuid)
* version_label (text) — e.g. "EU AI Act — April 2026 Update"
* effective_date (date)
* summary (text) — plain-language description of what changed
* affected_use_cases (jsonb) — list of use case categories impacted
* source_url (text)
* created_at (timestamp)

## compliance_alerts

* id (uuid)
* organization_id (uuid)
* ai_system_id (uuid) — nullable if alert applies to all systems in the org
* regulatory_version_id (uuid)
* source_agent_log_id (uuid) — nullable; references agent_logs if triggered by an agent
* alert_type (text) — regulatory_change | classification_expired | system_updated
* message (text)
* status (text) — pending | acknowledged | dismissed
* acknowledged_by (uuid) — user_id
* acknowledged_at (timestamp)
* created_at (timestamp)

## agent_logs

* id (uuid)
* agent_name (text)
* run_at (timestamp)
* status (text) — success | warning | error
* summary (text)
* output (jsonb)
* admin_action (text) — approved | dismissed | snoozed | null
* acted_at (timestamp)
