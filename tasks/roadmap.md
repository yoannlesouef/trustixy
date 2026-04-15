# Roadmap

## Phase 1 — Core Product (Weeks 1–4)

**Week 1**
* Project setup (Next.js, Supabase, Tailwind, Claude API)
* Auth (user + organization, multi-tenant)
* DB schema: organizations, users, ai_systems, classifications, obligations, documents, audit_logs, regulatory_versions

**Week 2**
* CRUD AI systems (soft-delete / archive, scoped to organization)
* `compliance_status` per system (unclassified by default)
* Dashboard UI: compliance status badges, summary bar, alert banner

**Week 3**
* Classification questionnaire (10 questions)
* LLM classification: risk level, explanation, obligations, confidence score, requires_expert_review
* Classification versioning: linked to regulatory_version, is_current flag
* Obligations checklist: generated from classification, updatable by user
* Compliance status auto-update after classification

**Week 4**
* Compliance document generation: linked to classification_id, versioned, disclaimer embedded
* Document history per system (current vs. superseded)
* Audit log: immutable trail for all actions
* Export: PDF with disclaimer, regulatory version, classification date
* Landing page: updated messaging (registry + traceability + human validation)
* Polish UI

---

## Phase 2 — Human Validation & Partner Channel (Weeks 5–10)

**Week 5**
* compliance_alerts table + alert creation logic
* Auto-flag systems as `needs_review` on description update or regulatory change
* In-app alert banner + email notification
* Alerts Center page (filterable, bulk acknowledge/dismiss)

**Week 6**
* Co-signature flow (end user side):
  * Request co-signature from prescriber partner
  * Document status: unsigned → pending_signature → co-signed
  * Email notification to prescriber on request
* document_signatures table

**Week 7**
* Co-signature flow (prescriber side):
  * Signature queue in partner portal
  * Document review page: classification summary, confidence score, obligations, full document
  * Actions: approve & sign | request changes | decline
  * Client notification on decision
  * Audit log entries for all signature events

**Week 8**
* Partner application flow (form + tier selection + agreement acceptance)
* Partner data model (partners, partner_clients)
* Partner API endpoints

**Week 9**
* Partner portal UI: client compliance health summary, signature queue, invite client
* Branded onboarding email for invited clients

**Week 10**
* White-label settings (logo, colors, custom domain)
* Commission tracking dashboard
* End-to-end partner → client → co-signature flow testing

---

## Phase 3 — AI Agents & Admin Intelligence (Weeks 11–14)

**Week 11**
* Agent infrastructure (Vercel Cron / Supabase Edge Functions)
* agent_logs table + Admin Dashboard (internal protected route)
* Platform Guardian agent (uptime, error rates, LLM cost monitoring)

**Week 12**
* Compliance Intelligence Tracker agent:
  * Monitors EU AI Act regulatory updates
  * Creates regulatory_version entries on new updates
  * Runs regulatory impact assessment per affected system (new LLM prompt)
  * Auto-triggers compliance_alerts with source_agent_log_id
  * Auto-sets affected systems to `needs_review`
* Customer Success Assistant agent (milestone tracking, onboarding drop-off, daily focus list)

**Week 13**
* Partner Success Monitor agent (partner health, co-signature adoption, churn alerts)
* Prescriber Hunter agent (prospect search, scoring, outreach drafts)

**Week 14**
* Admin Dashboard polish (agent log viewer, one-click actions)
* Slack/email alert integration
* End-to-end agent testing (regulatory update → alert → user notification → co-signature flow)

---

## Phase 4 — Scale (Weeks 15+)

* Marketplace listings (AWS, Azure, Google Cloud)
* Embedded API for integrators (classify systems from external tools via webhook)
* Auto-detection of AI systems deployed on cloud providers
* Multi-regulation expansion: GDPR, NIS2, AI Liability Directive
* Organization compliance report export (audit-ready PDF: all systems, history, obligation completion)
* Multi-user workspaces with role-based access
* Improved classification accuracy (prompt iteration, feedback loop from co-signature reviews)
* SLA and dedicated account management for Embedded tier partners
