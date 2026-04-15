# Roadmap

## Phase 1 — Core Product (Weeks 1–5)

**Week 1**
* Project setup (Next.js, Supabase, Tailwind, Claude API)
* Auth (user + organization, multi-tenant)
* DB schema: organizations (with sector, wizard_completed_at), users, ai_systems (with source, description_quality_score), classifications, obligations (with implementation_guide), documents, audit_logs, regulatory_versions
* Seed regulatory_versions with current EU AI Act version and key deadline entries

**Week 2**
* **Identification Wizard** (Axis 1 — step zero):
  * Sector selection + tool inventory checklist (pre-populated by sector)
  * Wizard Analysis LLM prompt: assess tools against EU AI Act scope
  * Results review screen: suggested systems with pre-filled descriptions
  * Batch system creation from wizard
  * Wizard nudge on dashboard until completed
* CRUD AI systems (soft-delete / archive, scoped to organization)
* `compliance_status` per system (unclassified by default)

**Week 3**
* **Description Quality Check** (Axis 1 — garbage-in prevention):
  * LLM quality check on every system save
  * Inline warning + improvement suggestions if score < 60
  * Classification blocked with warning if score < 60
* Dashboard UI: compliance status badges, summary bar, alert banner, wizard nudge
* Classification questionnaire (10 questions, sector-aware)
* LLM classification: risk level, explanation, obligations + implementation_guide, confidence score, requires_expert_review
* Classification versioning: linked to regulatory_version, is_current flag

**Week 4**
* Obligations checklist: generated from classification, with implementation_guide per item, updatable by user
* Compliance status auto-update after classification
* Compliance document generation: linked to classification_id, versioned, disclaimer embedded
* Document history per system (current vs. superseded)
* Audit log: immutable trail for all actions

**Week 5**
* Export: PDF with disclaimer, regulatory version, classification date
* Landing page: updated messaging (registry + wizard + traceability + human validation)
* Polish UI — wizard flow, system page with quality indicator, obligations with how-to guide
* End-to-end test: wizard → classify → obligations → document

---

## Phase 2 — Human Validation & Partner Channel (Weeks 6–12)

**Week 6**
* compliance_alerts table + alert creation logic
* Auto-flag systems as `needs_review` on description update or regulatory change
* In-app alert banner + email notification
* Alerts Center page (filterable, bulk acknowledge/dismiss)

**Week 7**
* **Prescriber Certification — data model & flow** (Axis 2):
  * prescriber_certifications table
  * Co-signature scope document (versioned text, accept flow)
  * Certification training module content (5 modules, in-app)
  * Final assessment (20 questions, 80% pass threshold)
  * `is_certified` flag on partner profile; certification badge

**Week 8**
* Co-signature flow (end user side):
  * Request co-signature (blocked if prescriber not certified — warning shown)
  * Document status: unsigned → pending_signature → co-signed
  * Email notification to prescriber on request
* document_signatures table (with professional_notes, certification_id, cosignature_scope_version)

**Week 9**
* Co-signature flow (prescriber side):
  * Certification gate on signature queue (full-screen prompt if not certified)
  * Document review page: classification, confidence score, sector, obligations with implementation guides
  * **Professional notes field** (Axis 2): free-text reviewer comments included in document
  * Actions: approve & sign | request changes | decline
  * Co-signed document: prescriber name, firm, certification badge, professional notes embedded
  * Client notification on decision; audit log entries

**Week 10**
* Partner application flow (form + tier selection + agreement acceptance → redirect to certification)
* Partner data model (partners, partner_clients)
* Partner API endpoints

**Week 11**
* Partner portal UI: certification status banner, client compliance health, signature queue (gated by certification), invite client
* Certification renewal flow (30-min module + re-assessment)
* Certification expiry reminder (email at 60 days)
* Branded onboarding email for invited clients

**Week 12**
* White-label settings (logo, colors, custom domain)
* Commission tracking dashboard
* End-to-end test: partner onboarding → certification → client invitation → co-signature with professional notes

---

## Phase 3 — AI Agents & Admin Intelligence (Weeks 13–16)

**Week 13**
* Agent infrastructure (Vercel Cron / Supabase Edge Functions)
* agent_logs table + Admin Dashboard (internal protected route)
* Platform Guardian agent (uptime, error rates, LLM cost monitoring)

**Week 14**
* Compliance Intelligence Tracker agent:
  * Monitors EU AI Act sources (EUR-Lex RSS, AI Office, CEN/CENELEC, secondary sources)
  * Creates regulatory_version entries pending admin approval
  * Runs regulatory impact assessment per affected system
  * Auto-triggers compliance_alerts with source_agent_log_id
  * Proactive deadline alerts (90-day, 30-day warnings from seeded deadlines)
* Customer Success Assistant agent:
  * Tracks onboarding drop-off (wizard not completed, no classification 7 days after signup)
  * Daily focus list for admin

**Week 15**
* Partner Success Monitor agent (partner health, certification expiry, co-signature adoption, churn alerts)
* Prescriber Hunter agent (prospect search, scoring, outreach drafts)

**Week 16**
* Admin Dashboard polish (agent log viewer, one-click actions, regulatory version approval flow)
* Slack/email alert integration
* End-to-end agent testing (regulatory update → admin approval → alerts → user notification → re-classify → co-signature)

---

## Phase 4 — Scale (Weeks 17+)

* Marketplace listings (AWS, Azure, Google Cloud)
* Embedded API for integrators (classify systems from external tools via webhook)
* Auto-detection of AI systems deployed on cloud providers
* Multi-regulation expansion: GDPR, NIS2, AI Liability Directive
* Organization compliance report export (audit-ready PDF: all systems, history, obligation completion, co-signatures)
* Multi-user workspaces with role-based access
* Sector-specific certification modules for prescribers (Healthcare, Finance, HR deep-dives)
* Improved classification accuracy (prompt iteration, feedback loop from co-signature review patterns)
* SLA and dedicated account management for Embedded tier partners
* Tool catalog expansion: community-contributed tool descriptions for the wizard
