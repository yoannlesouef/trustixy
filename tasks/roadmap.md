# Roadmap

## Phase 1 — Core Product (Weeks 1–5)

**Week 1**
* Project setup (Next.js, Supabase, Tailwind, Claude API)
* Auth (user + organization, multi-tenant)
* DB schema: organizations (sector, wizard_completed_at), users, ai_systems (source, description_quality_score), classifications, obligations (implementation_guide), documents, audit_logs, regulatory_versions
* Seed regulatory_versions with current EU AI Act version and key deadline entries

**Week 2**
* **Quick classification path** (primary UX — value in 3 minutes):
  * Empty state with two entry points: Quick assess (primary) / Discover all systems (secondary)
  * 5-question classification flow, one question at a time, mobile-compatible
  * Optional 5 refinement questions (collapsed, improve confidence score)
  * Immediate result screen: risk level badge + top 3 obligations
  * One-click document generation from result screen
* CRUD AI systems (soft-delete / archive)
* `compliance_status` per system (`unclassified` by default)
* Dashboard: compact 5-column table, contextual next action per system, summary strip

**Week 3**
* **Identification Wizard** (optional path — sector + tools on one combined screen):
  * Wizard Analysis LLM prompt
  * Inline results review with include/exclude toggles
  * Batch system creation
  * Dismissable wizard nudge card on dashboard
* **Description Quality Check** (warning only — never blocks):
  * LLM quality check on save
  * Subtle amber/green dot indicator with tooltip suggestions
  * Score stored on ai_system; surfaced alongside classification result
* System page: tabbed layout (Classification / Obligations / Document / History)
* One context-aware primary action above the fold

**Week 4**
* Obligations checklist: top 3 shown by default, "Show all" toggle, implementation_guide per item
* Compliance status auto-update after classification
* Compliance document generation: linked to classification_id, versioned, disclaimer as compact footer
* Document page: clean two-section layout (content + action panel)
* Audit log: immutable trail in background

**Week 5**
* Export: PDF with disclaimer, regulatory version, classification date
* Landing page with two-path messaging (Quick assess / Discover all systems)
* Mobile-compatible: quick classification, obligations checkbox, document view
* End-to-end test: quick path (3 min) + wizard path + document generation

---

## Phase 2 — Human Validation & Partner Channel (Weeks 6–12)

**Week 6**
* compliance_alerts table + alert creation logic
* Auto-flag systems as `needs_review` on description update or regulatory change
* Alert as non-intrusive banner on system page + nav badge (hidden when no alerts)
* Email notification on new alert

**Week 7**
* **Prescriber Certification — two levels**:
  * Level 1 (Scope Acceptance, 15 min): read scope doc + checkbox → unlocks Minimal/Limited signing
  * Level 2 (Full Cert, 90 min): 4 modules + 15-question assessment (75% threshold, immediate retry)
  * prescriber_certifications table; `is_certified` on partner profile
  * Certification banner on portal home (non-blocking — portal fully usable without cert)

**Week 8**
* Co-signature flow (end user side):
  * "Request expert review" CTA (not "co-signature" in the UI label)
  * Pre-filled modal if org has linked prescriber (one-click send)
  * No linked prescriber → modal with partner directory link + "Download with disclaimer" fallback
  * Document status: unsigned → pending → reviewed
* document_signatures table (professional_notes, certification_id, cosignature_scope_version)

**Week 9**
* Co-signature flow (prescriber side):
  * Signature queue with lock icon (tooltip to certify) if not certified
  * Two-panel review page: document (left) + review panel (right)
  * Professional notes field (optional)
  * Actions: approve & sign | request changes | decline
  * Mobile-compatible review flow

**Week 10**
* Partner directory: searchable list of certified partners, filterable by sector
* Partner application flow + tier selection + agreement acceptance → redirect to Level 1 cert
* Partner API endpoints

**Week 11**
* Partner portal UI: compliance health summary, signature queue, invite client
* Branded onboarding email for invited clients
* Certification renewal flow (20-min module + 5-question quiz)
* Expiry reminder email at 60 days; in-app banner (non-blocking) when expired

**Week 12**
* White-label settings (logo, colors, custom domain)
* Commission tracking dashboard
* End-to-end test: partner onboarding → Level 1 cert → client invitation → co-signature with professional notes

---

## Phase 3 — AI Agents & Admin Intelligence (Weeks 13–16)

**Week 13**
* Agent infrastructure (Vercel Cron / Supabase Edge Functions)
* agent_logs table + Admin Dashboard (internal protected route)
* Platform Guardian agent

**Week 14**
* Compliance Intelligence Tracker agent:
  * Monitors EU AI Act sources (EUR-Lex RSS, AI Office, CEN/CENELEC, secondary sources)
  * Creates regulatory_version entries pending admin approval
  * Regulatory impact assessment per affected system
  * Auto-triggers compliance_alerts with source_agent_log_id
  * Proactive deadline alerts (90-day, 30-day warnings)
* Customer Success Assistant agent:
  * Tracks users who completed quick path but never saved to registry
  * Tracks drop-off after wizard (systems created but none classified)
  * Daily focus list for admin

**Week 15**
* Partner Success Monitor agent (certification expiry, co-signature adoption, churn)
* Prescriber Hunter agent (prospect search, outreach drafts)

**Week 16**
* Admin Dashboard polish (regulatory version approval, agent log viewer)
* Slack/email alert integration
* End-to-end agent testing

---

## Phase 4 — Scale (Weeks 17+)

* Marketplace listings (AWS, Azure, Google Cloud)
* Embedded API for integrators
* Auto-detection of AI systems from cloud providers
* Multi-regulation expansion (GDPR, NIS2, AI Liability Directive)
* Organization compliance report (audit-ready PDF)
* Multi-user workspaces with role-based access
* Sector-specific Level 2 certification modules
* Tool catalog expansion (community-contributed wizard entries)
* Improved classification via feedback loop from co-signature patterns
* Free plan upgrade: 5 systems (from 3)
