# Sprint 1 — Supabase: Schema + RLS

**Goal:** All tables, indexes, RLS policies, auth hook, and RPC functions deployed to Supabase EU project. No app code. Done when cross-org isolation is verified and rate limiting works.

Spec refs: `specs/data-model.md` · `specs/rls.md` · `specs/auth.md` · `specs/infra.md`

---

## Setup

- [ ] Create Supabase project — region **eu-west-1** or **eu-central-1** (mandatory, GDPR)
- [ ] Enable email confirmation in Supabase Auth settings
- [ ] Create `supabase/migrations/` directory structure in repo

---

## Migration 001 — Initial schema

Tables to create (in dependency order):

**SDK layer**
- [ ] `organizations` — id, name, sector, plan, stripe_customer_id, stripe_subscription_id, subscription_status, current_period_end, default_payment_method_id, grace_period_ends_at, created_at
- [ ] `users` — id, organization_id, email, marketing_opted_out, restricted_at, created_at
- [ ] `projects` — id, organization_id, name, slug (unique per org), created_at
- [ ] `sdk_tokens` — id, project_id, name, token_hash (SHA-256), last_used_at, revoked_at, requests_this_minute, minute_window, created_at
- [ ] `agents` — all fields from data-model.md; composite unique on (organization_id, slug); compliance_status default `unclassified`
- [ ] `sessions` — id, project_id, agent_id, human_identity, environment, started_at, ended_at, total_actions, total_tokens, status, metadata, created_at
- [ ] `actions` — id, session_id, project_id, agent_id, sequence, type, timestamp, duration_ms, input, output, affected_resources (text[]), model, token_usage, status, error_message, metadata, created_at
- [ ] `sensitive_file_alerts` — id, project_id, agent_id, session_id, action_id, matched_pattern, affected_resource, severity (always `high`), status, acknowledged_by, acknowledged_at, created_at

**Compliance layer**
- [ ] `classifications` — id, agent_id, risk_level, explanation, obligations (jsonb), confidence_score, requires_expert_review, regulatory_version, is_current, evidence_session_id, created_at
- [ ] `obligations` — id, classification_id, agent_id, title, description, implementation_guide, regulatory_reference, done, updated_by, updated_at, archived_at, created_at
- [ ] `documents` — id, agent_id, classification_id, evidence_session_id, content, version, disclaimer_version, signature_status (`unsigned`|`pending_signature`|`co-signed`), is_current, created_at
- [ ] `document_signatures` — id, document_id, signed_by_user_id, partner_id, cosignature_scope_version, action, professional_notes, acted_at, created_at
- [ ] `audit_logs` — id, organization_id, agent_id (nullable), user_id, action, metadata (jsonb), created_at

**Partner & billing layer**
- [ ] `partners` — id, organization_id, tier, cosignature_fee, stripe_account_id, scope_accepted_at, scope_version, status, created_at
- [ ] `partner_clients` — id, partner_id, client_organization_id (nullable until accepted), invite_token_hash, invite_email, invited_at, accepted_at, activated_at, status, expires_at
- [ ] `cosignature_transactions` — id, document_signature_id, partner_id, client_organization_id, amount, trustixy_commission, stripe_transfer_id, status, created_at

**Internal agent layer**
- [ ] `agent_logs` — id, agent_name, run_at, status, summary, output (jsonb), admin_action, acted_at

**Feedback & community**
- [ ] `feedback` — id, organization_id, created_by, type, title, description, status, admin_response, admin_responded_at, github_issue_number, github_issue_url, vote_count, created_at, updated_at
- [ ] `feedback_votes` — id, feedback_id, user_id, created_at; unique on (feedback_id, user_id)
- [ ] `platform_settings` — id (always `'singleton'`), community_social_url, updated_at

---

## Migration 002 — Indexes

- [ ] `idx_actions_agent_created` ON actions (agent_id, created_at DESC)
- [ ] `idx_actions_session_sequence` ON actions (session_id, sequence ASC)
- [ ] `idx_actions_project_created` ON actions (project_id, created_at DESC)
- [ ] `idx_actions_resources` ON actions USING GIN (affected_resources)
- [ ] `idx_sessions_org_created` ON sessions (organization_id, created_at)
- [ ] `idx_agents_org_status` ON agents (organization_id, compliance_status) WHERE archived_at IS NULL

---

## Migration 003 — RLS policies

Enable RLS on every table. Policies per `specs/rls.md`:

- [ ] Helper functions: `auth.org_id()` and `auth.user_role()` extracting JWT claims
- [ ] `organizations` — SELECT/UPDATE for own org only
- [ ] `users` — SELECT/INSERT/UPDATE scoped to own org
- [ ] `projects` — SELECT/INSERT/UPDATE/DELETE scoped to own org
- [ ] `sdk_tokens` — scoped to own org via project
- [ ] `agents` — org SELECT/INSERT/UPDATE; partner SELECT for their clients
- [ ] `sessions` — org SELECT/INSERT; partner SELECT for their clients
- [ ] `actions` — org SELECT/INSERT; partner SELECT for their clients
- [ ] `sensitive_file_alerts` — org SELECT/INSERT; UPDATE for acknowledge/dismiss
- [ ] `classifications` — org SELECT/INSERT; partner SELECT for their clients
- [ ] `obligations` — org SELECT/INSERT/UPDATE; partner SELECT for their clients
- [ ] `documents` — org SELECT/INSERT; partner SELECT for their clients
- [ ] `document_signatures` — org SELECT; partner SELECT/INSERT/UPDATE for their clients
- [ ] `audit_logs` — org SELECT only (no INSERT/UPDATE/DELETE via RLS — service role only)
- [ ] `partners` — self SELECT/UPDATE; platform_admin full access
- [ ] `partner_clients` — partner CRUD for own rows; org SELECT for own linked partner
- [ ] `cosignature_transactions` — partner SELECT for own; org SELECT for own documents
- [ ] `agent_logs` — platform_admin only
- [ ] `feedback` — authenticated SELECT (all orgs); INSERT own org; UPDATE platform_admin only
- [ ] `feedback_votes` — authenticated SELECT; INSERT own user_id; DELETE own vote
- [ ] `platform_settings` — authenticated SELECT; UPDATE platform_admin only

---

## Migration 004 — Auth hook

- [ ] Custom JWT hook: on every sign-in, add `organization_id` and `role` to JWT claims
- [ ] `role` values: `admin` (default org users) · `partner` · `platform_admin` (bootstrapped via ADMIN_EMAIL)
- [ ] `platform_admin` role granted at DB level to the user whose email matches `ADMIN_EMAIL` env var

---

## Migration 005 — RPC functions

- [ ] `check_rate_limit(p_token_id uuid, p_limit integer) → boolean` — atomic per-minute counter on sdk_tokens (see `specs/auth.md`)
- [ ] Verify function is callable from service role (ingest API context)

---

## Migration 006 — Scheduled jobs

- [ ] Nightly session purge (02:30 UTC) — delete sessions + cascade actions for Free plan orgs older than 30 days
- [ ] 12-month compliance review check (nightly) — set `agents.compliance_status = 'needs_review'` where `last_classified_at < now() - interval '12 months'` and compliance_status = 'compliant'

---

## Supabase Storage

- [ ] Create `documents` bucket — private
- [ ] Signed URL config: 1-hour for downloads, 24-hour for co-signature review links

---

## Type generation

- [ ] `npx supabase gen types typescript --project-id [id] > types/database.ts`
- [ ] Commit generated types to repo

---

## Verification

- [ ] Cross-org access denied: authenticated user from org A cannot read org B's agents/sessions/actions
- [ ] Partner access: partner can read their client's agents/classifications but not other orgs
- [ ] Platform admin: can read all orgs (service role)
- [ ] Unauthenticated: all tables return 0 rows or error
- [ ] Rate limit RPC: calling `check_rate_limit` 101 times in one minute returns false on the 101st call
- [ ] Nightly purge job: manually trigger, verify 31-day-old Free plan sessions are deleted, Pro plan sessions are not
