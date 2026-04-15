# Database Schema

## organizations

* id (uuid)
* name (text)
* sector (text) — healthcare | finance | hr | legal | education | retail | manufacturing | public | other
* plan (text) — free | pro | enterprise | federation_member | integrator — current subscription plan
* parent_organization_id (uuid) — nullable; references organizations; set for federation members
* wizard_completed_at (timestamp) — null until the identification wizard is completed
* created_at (timestamp)

## partners

* id (uuid)
* organization_id (uuid) — the partner's own org
* tier (text) — per_act | federation | integrator | reseller
* billing_model (text) — per_act | flat_fee | subscription — drives invoicing logic
* cosignature_fee (numeric) — price per co-signature set by the prescriber (Model 1 only); shown to end user at request time
* stripe_account_id (text) — Stripe Connect account for direct payouts (Model 1); null for other models
* white_label_config (jsonb) — logo, colors, custom domain (reseller and integrator tiers)
* status (text) — pending | active | suspended
* is_certified (boolean) — derived from prescriber_certifications; true if a valid certification exists
* created_at (timestamp)

## prescriber_certifications

* id (uuid)
* partner_id (uuid)
* certified_by_user_id (uuid) — the partner user who completed the certification
* certification_version (text) — version of the certification content at time of completion
* cosignature_scope_version (text) — version of the co-signature scope document accepted
* score (integer) — quiz score (0–100)
* certified_at (timestamp)
* expires_at (timestamp) — certified_at + 12 months
* renewed_at (timestamp) — nullable; set on renewal
* created_at (timestamp)

## partner_clients

* id (uuid)
* partner_id (uuid)
* client_organization_id (uuid)
* invited_at (timestamp)
* activated_at (timestamp)
* status (text) — invited | active

## federation_memberships

* id (uuid)
* federation_partner_id (uuid) — references partners (tier: federation)
* member_organization_id (uuid) — references organizations
* joined_at (timestamp)
* plan_granted (text) — plan automatically assigned to the member (typically pro)

## integrator_projects

* id (uuid)
* integrator_partner_id (uuid) — references partners (tier: integrator)
* client_organization_id (uuid) — references organizations
* project_name (text)
* report_generated_at (timestamp) — nullable; set when the compliance report PDF is generated
* created_at (timestamp)

## cosignature_transactions

* id (uuid)
* document_signature_id (uuid) — references document_signatures
* partner_id (uuid)
* client_organization_id (uuid)
* amount (numeric) — total charged to client (prescriber's cosignature_fee)
* trustixy_commission (numeric) — amount retained by Trustixy (fixed per-act fee)
* stripe_transfer_id (text) — Stripe Connect transfer reference
* status (text) — pending | completed | refunded
* created_at (timestamp)

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
* sector (text) — inherited from organization, overridable per system
* source (text) — wizard | manual — how the system was created
* description_quality_score (integer) — 0–100; LLM-assessed description quality; null until first check
* description_quality_feedback (text) — LLM suggestions to improve the description; null if quality sufficient
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
* implementation_guide (text) — practical guidance on how to implement this obligation
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
* certification_id (uuid) — the prescriber_certification active at time of signing
* cosignature_scope_version (text) — version of the scope document accepted by this prescriber
* action (text) — approved | changes_requested | declined
* professional_notes (text) — optional reviewer analysis, caveats, or comments; included in the document
* acted_at (timestamp)
* created_at (timestamp)

## audit_logs

* id (uuid)
* organization_id (uuid) — for org-level queries
* ai_system_id (uuid) — nullable (some actions are org-level)
* user_id (uuid)
* action (text) — create | update | archive | classify | obligation_updated | generate_document | signature_requested | signature_approved | signature_declined | alert_acknowledged | alert_dismissed | status_changed | wizard_completed | description_quality_checked | certification_completed | cosignature_scope_accepted
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
