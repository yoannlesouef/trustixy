# Database Schema

---

## SDK Layer

### projects

* id (uuid)
* organization_id (uuid)
* name (text)
* slug (text) — unique per organization; used in SDK config
* created_at (timestamp)

### sdk_tokens

* id (uuid)
* project_id (uuid)
* name (text) — human-readable label (e.g. "CI/CD", "local dev")
* token_hash (text) — bcrypt hash; raw token shown once at creation
* last_used_at (timestamp) — nullable
* revoked_at (timestamp) — nullable; null = active
* created_at (timestamp)

### agents

Single table. Merges what was previously `agent_identities` (SDK) and `ai_systems` (compliance) into one concept. Every agent discovered by the SDK is also the compliance record — no promotion step required.

* id (uuid)
* organization_id (uuid)
* project_id (uuid)
* name (text) — editable; defaults to agent name from SDK config
* slug (text) — unique per organization
* description (text) — nullable; what this agent does (used for classification pre-fill)
* use_case (text) — nullable; business process it supports
* data_used (text) — nullable; types of data processed
* autonomy_level (text) — nullable; `full` | `supervised` | `advisory`
* owner (text) — nullable; team or person responsible
* sector (text) — inherited from organization, overridable
* framework (text) — `claude_code` | `anthropic` | `openai` | `langchain` | `vercel_ai` | `crewai` | `llamaindex` | `mcp` | `custom`
* first_seen_at (timestamp) — set on first ingest call
* last_seen_at (timestamp) — updated on each ingest
* total_sessions (integer) — denormalized counter
* total_actions (integer) — denormalized counter
* compliance_status (text) — `unclassified` | `compliant` | `needs_review` | `at_risk`
* last_classified_at (timestamp) — nullable
* archived_at (timestamp) — nullable; soft delete; never hard-deleted
* created_at (timestamp)

`compliance_status` auto-sets to `needs_review` when:
- `description` is updated after a classification exists
- `last_classified_at < NOW() - INTERVAL '12 months'`

### sessions

* id (uuid)
* project_id (uuid)
* agent_id (uuid) — references agents
* human_identity (text) — nullable; who triggered this session
* environment (text) — `production` (default) | `staging` | `development`
* started_at (timestamp)
* ended_at (timestamp) — nullable
* total_actions (integer) — denormalized counter
* total_tokens (integer) — denormalized sum
* status (text) — `running` | `completed` | `error` | `timeout`
* metadata (jsonb) — nullable; caller-provided context
* created_at (timestamp)

### actions

* id (uuid)
* session_id (uuid)
* project_id (uuid) — denormalized for efficient queries
* agent_id (uuid) — denormalized
* sequence (integer) — 1-based order within session
* type (text) — `llm_call` | `tool_call` | `file_read` | `file_write` | `file_delete` | `api_call` | `shell_command` | `bash` | `mcp_call` | `custom`
* timestamp (timestamp)
* duration_ms (integer)
* input (jsonb) — sanitized; full content only if `captureContent=true`
* output (jsonb) — sanitized
* affected_resources (text[]) — file paths, URLs, or identifiers
* model (text) — nullable; LLM model for `llm_call` actions
* token_usage (jsonb) — nullable; `{ input_tokens, output_tokens, cache_read_tokens }`
* status (text) — `success` | `error` | `timeout`
* error_message (text) — nullable
* metadata (jsonb) — nullable
* created_at (timestamp)

### sensitive_file_alerts

* id (uuid)
* project_id (uuid)
* agent_id (uuid)
* session_id (uuid)
* action_id (uuid)
* matched_pattern (text) — e.g. `**/.env`
* affected_resource (text) — the file path accessed
* status (text) — `open` | `acknowledged` | `dismissed`
* acknowledged_by (uuid) — nullable
* acknowledged_at (timestamp) — nullable
* created_at (timestamp)

---

## Organization & Users

### organizations

* id (uuid)
* name (text)
* sector (text) — `healthcare` | `finance` | `hr` | `legal` | `education` | `retail` | `manufacturing` | `public` | `other`
* plan (text) — `free` | `pro` | `enterprise` | `integrator`
* stripe_customer_id (text) — nullable; set on first checkout
* stripe_subscription_id (text) — nullable
* subscription_status (text) — `active` | `past_due` | `canceled` | `trialing` | null
* current_period_end (timestamp) — nullable
* default_payment_method_id (text) — nullable; used for per-act co-signature charges
* created_at (timestamp)

### users

* id (uuid)
* organization_id (uuid)
* email (text)
* created_at (timestamp)

---

## Compliance Layer

### classifications

* id (uuid)
* agent_id (uuid)
* risk_level (text) — `minimal` | `limited` | `high` | `unacceptable`
* explanation (text)
* obligations (jsonb) — snapshot of obligations at classification time
* confidence_score (integer) — 0–100
* requires_expert_review (boolean)
* regulatory_version (text) — e.g. `"EU AI Act 2024-08-01"`
* is_current (boolean)
* evidence_session_id (uuid) — nullable; SDK session used as evidence
* created_at (timestamp)

### obligations

* id (uuid)
* classification_id (uuid)
* agent_id (uuid) — denormalized
* title (text)
* description (text)
* implementation_guide (text)
* regulatory_reference (text)
* done (boolean) — default false
* updated_by (uuid)
* updated_at (timestamp)
* created_at (timestamp)

### documents

* id (uuid)
* agent_id (uuid)
* classification_id (uuid)
* evidence_session_id (uuid) — nullable
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
* cosignature_scope_version (text)
* action (text) — `approved` | `changes_requested` | `declined`
* professional_notes (text) — nullable
* acted_at (timestamp)
* created_at (timestamp)

### audit_logs

Immutable. Records all compliance platform actions.

* id (uuid)
* organization_id (uuid)
* agent_id (uuid) — nullable
* user_id (uuid)
* action (text) — `agent_created` | `agent_updated` | `agent_archived` | `classify` | `obligation_updated` | `generate_document` | `signature_requested` | `signature_approved` | `signature_declined` | `status_changed` | `scope_accepted`
* metadata (jsonb)
* created_at (timestamp)

---

## Partner & Billing Layer

### partners

* id (uuid)
* organization_id (uuid)
* tier (text) — `per_act` | `integrator`
* cosignature_fee (numeric) — nullable; prescriber-set price per co-signature
* stripe_account_id (text) — nullable; Stripe Connect
* scope_accepted_at (timestamp) — nullable; null = not yet certified; set on partner signup completion
* scope_version (text) — nullable; version of scope document accepted
* status (text) — `pending` | `active` | `suspended`
* created_at (timestamp)

`is_certified` is derived: `scope_accepted_at IS NOT NULL`

### partner_clients

* id (uuid)
* partner_id (uuid)
* client_organization_id (uuid)
* invited_at (timestamp)
* activated_at (timestamp) — nullable
* status (text) — `invited` | `active`

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

### agent_logs

* id (uuid)
* agent_name (text) — internal agent name
* run_at (timestamp)
* status (text) — `success` | `warning` | `error`
* summary (text)
* output (jsonb)
* admin_action (text) — `approved` | `dismissed` | `snoozed` | null
* acted_at (timestamp)
