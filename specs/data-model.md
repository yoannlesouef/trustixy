# Database Schema

---

## SDK Layer

### projects

* id (uuid)
* organization_id (uuid) — references organizations
* name (text)
* slug (text) — unique per organization; used in SDK config
* description (text) — nullable
* created_at (timestamp)

### sdk_tokens

* id (uuid)
* project_id (uuid)
* name (text) — human-readable label (e.g. "CI/CD", "local dev")
* token_hash (text) — bcrypt hash of the token; raw token shown once at creation
* last_used_at (timestamp) — nullable
* revoked_at (timestamp) — nullable; null = active
* created_at (timestamp)

### agent_identities

* id (uuid)
* project_id (uuid)
* name (text) — agent name as provided in SDK config
* slug (text) — unique per project; derived from name
* framework (text) — `claude_code` | `anthropic` | `openai` | `langchain` | `vercel_ai` | `crewai` | `llamaindex` | `mcp` | `custom`
* auto_discovered (boolean) — true if first seen from SDK, false if manually created
* first_seen_at (timestamp)
* last_seen_at (timestamp)
* total_sessions (integer) — denormalized counter
* total_actions (integer) — denormalized counter
* linked_ai_system_id (uuid) — nullable; references ai_systems; set when promoted to compliance registry
* status (text) — `discovered` | `registered` | `archived`
* created_at (timestamp)

### sessions

* id (uuid)
* project_id (uuid)
* agent_identity_id (uuid)
* human_identity (text) — nullable; who triggered this session (email, username, CI actor, etc.)
* environment (text) — `production` | `staging` | `development` | `test`
* started_at (timestamp)
* ended_at (timestamp) — nullable; null if session still running
* total_actions (integer) — denormalized counter
* total_tokens (integer) — sum of all token_usage in this session
* status (text) — `running` | `completed` | `error` | `timeout`
* metadata (jsonb) — arbitrary caller-provided context (PR number, branch, deployment ID, etc.)
* created_at (timestamp)

### actions

* id (uuid)
* session_id (uuid)
* project_id (uuid) — denormalized for efficient project-level queries
* agent_identity_id (uuid) — denormalized
* sequence (integer) — 1-based order within the session
* type (text) — `llm_call` | `tool_call` | `file_read` | `file_write` | `file_delete` | `api_call` | `shell_command` | `bash` | `mcp_call` | `custom`
* timestamp (timestamp) — when the action started
* duration_ms (integer)
* input (jsonb) — sanitized input: path/URL/prompt structure (not full content unless captureContent=true)
* output (jsonb) — sanitized output: status/structure (not full content unless captureContent=true)
* affected_resources (text[]) — file paths, URLs, or identifiers accessed or modified
* model (text) — nullable; LLM model identifier for `llm_call` actions
* token_usage (jsonb) — nullable; `{ input_tokens, output_tokens, cache_read_tokens }`
* status (text) — `success` | `error` | `timeout`
* error_message (text) — nullable
* metadata (jsonb) — nullable; caller-provided context
* created_at (timestamp)

### anomaly_detections

* id (uuid)
* project_id (uuid)
* session_id (uuid) — nullable; the session where the anomaly was detected
* action_id (uuid) — nullable; the specific action that triggered the anomaly
* type (text) — `unexpected_scope` | `sensitive_file_access` | `policy_violation` | `volume_spike` | `error_rate_spike` | `exfiltration_pattern` | `unusual_shell_commands` | `custom`
* severity (text) — `low` | `medium` | `high` | `critical`
* description (text) — plain-language explanation of what was flagged
* evidence (jsonb) — structured details (matched pattern, threshold, comparison baseline)
* status (text) — `open` | `acknowledged` | `dismissed`
* acknowledged_by (uuid) — nullable; user_id
* acknowledged_at (timestamp) — nullable
* created_at (timestamp)

### policy_rules

* id (uuid)
* project_id (uuid)
* agent_identity_id (uuid) — nullable; null = applies to all agents in the project
* rule_type (text) — `deny` | `alert`
* action_type (text) — action type this rule applies to (or `*` for all)
* condition (jsonb) — matching criteria: `{ paths: ["**/.env"], hosts: ["!*.internal.co"] }` etc.
* severity (text) — for `alert` rules: `low` | `medium` | `high` | `critical`
* enabled (boolean)
* created_at (timestamp)

---

## Organization & Users

### organizations

* id (uuid)
* name (text)
* sector (text) — `healthcare` | `finance` | `hr` | `legal` | `education` | `retail` | `manufacturing` | `public` | `other`
* plan (text) — `free` | `pro` | `enterprise` | `federation_member` | `integrator` — current subscription plan
* sdk_plan (text) — `free` | `pro` | `enterprise` — SDK/audit tier, separate from compliance tier
* parent_organization_id (uuid) — nullable; set for federation members
* wizard_completed_at (timestamp) — null until the identification wizard is completed
* created_at (timestamp)

### users

* id (uuid)
* organization_id (uuid)
* email (text)
* role (text) — `admin` | `member`
* created_at (timestamp)

---

## Compliance Layer

### ai_systems

* id (uuid)
* organization_id (uuid)
* name (text)
* description (text)
* use_case (text)
* data_used (text)
* autonomy_level (text)
* owner (text)
* sector (text) — inherited from organization, overridable per system
* source (text) — `sdk_discovered` | `manual` | `wizard`
* linked_agent_identity_id (uuid) — nullable; references agent_identities; links compliance record to SDK data
* description_quality_score (integer) — 0–100; LLM-assessed; null until first check
* description_quality_feedback (text) — nullable
* compliance_status (text) — `unclassified` | `compliant` | `needs_review` | `at_risk`
* last_classified_at (timestamp)
* last_reviewed_at (timestamp)
* archived_at (timestamp) — null if active; systems are never hard-deleted
* created_at (timestamp)

### classifications

* id (uuid)
* ai_system_id (uuid)
* risk_level (text) — `minimal` | `limited` | `high` | `unacceptable`
* explanation (text)
* obligations (jsonb)
* confidence_score (integer) — 0–100
* requires_expert_review (boolean)
* triggered_by (text) — `user` | `regulatory_change` | `system_update` | `behavioral_change`
* regulatory_version_id (uuid)
* is_current (boolean)
* evidence_session_id (uuid) — nullable; references sessions; links classification to SDK evidence
* created_at (timestamp)

### obligations

* id (uuid)
* classification_id (uuid)
* ai_system_id (uuid) — denormalized
* title (text)
* description (text)
* implementation_guide (text)
* regulatory_reference (text)
* status (text) — `todo` | `in_progress` | `done`
* due_date (date) — nullable
* updated_by (uuid)
* updated_at (timestamp)
* created_at (timestamp)

### documents

* id (uuid)
* ai_system_id (uuid)
* classification_id (uuid)
* evidence_session_id (uuid) — nullable; references sessions; included as "Evidence Trail" section in document
* content (text)
* version (integer)
* disclaimer_version (text)
* signature_status (text) — `unsigned` | `pending_signature` | `co-signed` | `changes_requested`
* is_current (boolean)
* created_at (timestamp)

### document_signatures

* id (uuid)
* document_id (uuid)
* signed_by_user_id (uuid)
* partner_id (uuid)
* certification_id (uuid)
* cosignature_scope_version (text)
* action (text) — `approved` | `changes_requested` | `declined`
* professional_notes (text) — nullable
* acted_at (timestamp)
* created_at (timestamp)

### audit_logs (platform-level)

* id (uuid)
* organization_id (uuid)
* ai_system_id (uuid) — nullable
* user_id (uuid)
* action (text) — `system_create` | `system_update` | `system_archive` | `classify` | `obligation_updated` | `generate_document` | `signature_requested` | `signature_approved` | `signature_declined` | `alert_acknowledged` | `alert_dismissed` | `status_changed` | `certification_completed` | `cosignature_scope_accepted`
* metadata (jsonb)
* created_at (timestamp)

### regulatory_versions

* id (uuid)
* version_label (text)
* effective_date (date)
* summary (text)
* affected_use_cases (jsonb)
* source_url (text)
* created_at (timestamp)

### compliance_alerts

* id (uuid)
* organization_id (uuid)
* ai_system_id (uuid) — nullable
* regulatory_version_id (uuid) — nullable
* source_agent_log_id (uuid) — nullable; references agent_logs
* alert_type (text) — `regulatory_change` | `classification_expired` | `system_updated` | `behavioral_change`
* message (text)
* status (text) — `pending` | `acknowledged` | `dismissed`
* acknowledged_by (uuid)
* acknowledged_at (timestamp)
* created_at (timestamp)

---

## Partner & Billing Layer

### partners

* id (uuid)
* organization_id (uuid)
* tier (text) — `per_act` | `federation` | `integrator` | `reseller`
* billing_model (text) — `per_act` | `flat_fee` | `subscription`
* cosignature_fee (numeric) — nullable; prescriber-set price per co-signature (Model 1)
* stripe_account_id (text) — nullable; Stripe Connect (Model 1)
* white_label_config (jsonb) — logo, colors, custom domain
* status (text) — `pending` | `active` | `suspended`
* is_certified (boolean) — derived from prescriber_certifications
* created_at (timestamp)

### prescriber_certifications

* id (uuid)
* partner_id (uuid)
* certified_by_user_id (uuid)
* certification_level (text) — `level_1` | `level_2`
* certification_version (text)
* cosignature_scope_version (text)
* score (integer) — nullable; level_1 has no quiz
* certified_at (timestamp)
* expires_at (timestamp)
* renewed_at (timestamp) — nullable
* created_at (timestamp)

### partner_clients

* id (uuid)
* partner_id (uuid)
* client_organization_id (uuid)
* invited_at (timestamp)
* activated_at (timestamp)
* status (text) — `invited` | `active`

### federation_memberships

* id (uuid)
* federation_partner_id (uuid)
* member_organization_id (uuid)
* joined_at (timestamp)
* plan_granted (text)

### integrator_projects

* id (uuid)
* integrator_partner_id (uuid)
* client_organization_id (uuid)
* project_name (text)
* report_generated_at (timestamp) — nullable
* created_at (timestamp)

### cosignature_transactions

* id (uuid)
* document_signature_id (uuid)
* partner_id (uuid)
* client_organization_id (uuid)
* amount (numeric)
* trustixy_commission (numeric)
* stripe_transfer_id (text)
* status (text) — `pending` | `completed` | `refunded`
* created_at (timestamp)

---

## Internal Agent Layer

### agent_logs (internal automation runs)

* id (uuid)
* agent_name (text) — internal agent name (Compliance Intelligence Tracker, Anomaly Agent, etc.)
* run_at (timestamp)
* status (text) — `success` | `warning` | `error`
* summary (text)
* output (jsonb)
* admin_action (text) — `approved` | `dismissed` | `snoozed` | null
* acted_at (timestamp)
