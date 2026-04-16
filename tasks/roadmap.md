# Roadmap

---

## Phase 1 — SDK & Audit Core (Weeks 1–4)

**Week 1 — Infrastructure & SDK**
* Project setup (Next.js, Supabase, Tailwind, Claude API)
* Auth (user + organization, multi-tenant)
* DB schema: organizations, users, projects, sdk_tokens, agent_identities, sessions, actions, policy_rules, anomaly_detections
* Ingest API: `POST /v1/ingest` — receives session + action batches from SDK
* `@trustixy/sdk` npm package: `trustixy.wrap()` for Anthropic SDK and OpenAI SDK
* SDK token management: create, list, revoke

**Week 2 — Action Log & Session View**
* Session list page (developer view — primary home)
* Session detail page: ordered action timeline, color-coded by type, expandable inline
* Filter and search: by agent, action type, time range, status
* Agent identities: auto-created on first ingest, listed in Agents page

**Week 3 — Session Replay & Python SDK**
* Session replay: step navigator + context / action / outcome panels per step
* Keyboard navigation, jump to step, filter by action type
* Export replay as PDF session report
* `trustixy` pip package: `wrap()` for Anthropic Python SDK and LangChain
* Claude Code hook integration: `trustixy hook pre/post/stop` CLI commands

**Week 4 — Anomaly Detection & Policy Rules**
* `anomaly_detections` table + Anomaly Detection Agent (Claude API)
* Behavioral baseline per agent identity (rolling 30-day)
* Policy rules: per-project and per-agent, deny/alert rules
* Anomaly Center UI: severity list, evidence, acknowledge/dismiss
* Alert delivery: in-app badge + email notification
* End-to-end: npm install → ingest → timeline → replay → anomaly flag

---

## Phase 2 — Compliance Layer (Weeks 5–9)

**Week 5 — Auto-discovery & Registry**
* Agent-to-system registration flow: review detected behavior → pre-filled form → register
* `POST /v1/ingest` pre-fill inference (LLM prompt: Agent Behavior Pre-fill for Compliance)
* Compliance dashboard: systems table with SDK source indicator
* Discover nudge: "[N] agent(s) detected but not registered"
* Quick classification path: 5 questions, one at a time, immediate result
* Empty state compliance path (Path B — classify without SDK)

**Week 6 — Classification & Obligations**
* Full classification flow including SDK evidence in LLM prompt
* Classification result: risk level + explanation + top 3 obligations
* Obligations checklist: top 3 shown, "Show all" toggle, implementation_guide per item
* `compliance_status` auto-update after classification
* System page: tabbed layout (Classification / Obligations / Document / SDK Activity / History)

**Week 7 — Documents & Compliance Export**
* Compliance document generation: linked to classification_id + optional evidence session
* Mandatory disclaimer block, compact footer style
* Versioned documents per system
* Document page: evidence trail block (links to SDK sessions)
* Export: PDF with disclaimer + regulatory version + evidence trail section
* Description quality check (LLM): amber/green indicator, never blocks classification

**Week 8 — Regulatory Alerts & Identification Wizard**
* `compliance_alerts` table + alert creation logic
* Auto-flag systems `needs_review` on regulatory change or behavioral change
* Alert banner (non-intrusive) + nav badge
* Email notification on new alert
* Identification Wizard: sector + tool checklist → LLM analysis → batch system creation

**Week 9 — Compliance Intelligence Tracker Agent**
* Agent infrastructure (Vercel Cron / Supabase Edge Functions)
* `agent_logs` table + Admin Dashboard (internal protected route)
* Compliance Intelligence Tracker: monitors EUR-Lex, AI Office, CEN/CENELEC
* Draft `regulatory_version` entries pending admin approval
* Regulatory impact assessment per affected system
* Pre-seed key EU AI Act deadline entries in `regulatory_versions`

---

## Phase 3 — Prescriber & Partners (Weeks 10–14)

**Week 10 — Prescriber Certification**
* `prescriber_certifications` table
* Level 1 — Scope Acceptance (15 min): read scope doc + checkbox → `level_1` cert
* Level 2 — Full Certification (~90 min): 4 modules + 15-question assessment (75% threshold)
* Certification banner on partner portal (non-blocking)
* Renewal flow: 20-min module + 5-question quiz

**Week 11 — Co-signature Flow (End User Side)**
* "Request expert review" CTA on document page and system page
* Pre-filled modal if org has linked prescriber (one-click send)
* No linked prescriber → modal with partner directory + "Download with disclaimer" fallback
* `document_signatures` table (professional_notes, certification_id, scope_version)
* Document status: unsigned → pending_signature → co-signed / changes_requested

**Week 12 — Co-signature Flow (Prescriber Side)**
* Signature queue: lock icon if not certified
* Two-panel review page: document (left) + review panel with SDK evidence section (right)
* Professional notes field (optional, recommended for High-risk)
* Actions: approve & sign | request changes | decline
* Mobile-compatible review flow

**Week 13 — Partner Portal & Directory**
* Partner application flow + tier selection + agreement acceptance
* Partner portal UI: compliance health per client, signature queue, invite client
* Partner directory: searchable, filterable by sector and certification level
* White-label settings (logo, colors, custom domain) for Reseller and Integrator tiers

**Week 14 — Billing & Partner Models**
* **Model 1 — Per-act co-signature:**
  * `cosignature_fee` on partner profile
  * Stripe Connect onboarding
  * `cosignature_transactions` table + transaction history
  * End-user sees prescriber fee at request time
* **Model 2 — Licence fédération:**
  * `federation_memberships` table
  * Auto-grant Pro plan to federation members
  * Federation aggregate compliance dashboard
* **Model 3 — Licence intégrateur:**
  * `integrator_projects` table
  * White-labeled PDF report generation
  * API: `/integrator/projects` + `/integrator/projects/:id/report`
* End-to-end test: per-act co-signature with Stripe payout + federation onboarding + integrator report

---

## Phase 4 — AI Agents & Scale (Weeks 15+)

**Week 15**
* Platform Guardian agent (continuous monitoring, nightly smoke tests)
* Customer Success Assistant agent (milestone tracking, onboarding drop-off)
* Slack/email alert integration for all agents
* Admin Dashboard polish (agent log viewer, regulatory version approval)

**Week 16**
* Partner Success Monitor agent
* Prescriber Hunter agent
* End-to-end agent testing

**Week 17+**
* SIEM export (JSON / CEF) for anomaly events
* LangChain callback handler (npm + pip) — verified with LangGraph
* Vercel AI SDK integration
* LlamaIndex + CrewAI Python integrations
* MCP server (`@trustixy/mcp`)

**Scale**
* Marketplace listings (AWS, Azure, Google Cloud)
* Embedded API for integrators (compliance report generation from external tooling)
* Multi-regulation expansion (GDPR, NIS2, AI Liability Directive)
* SSO (SAML/OIDC) for Enterprise
* Self-hosted ingest endpoint (Enterprise)
* Multi-user workspaces with role-based access (developer / security / compliance roles)
* Advanced replay: diff view between sessions, branching session comparison
* Improved classification via feedback loop from co-signature patterns
* SDK support for additional frameworks (Haystack, AutoGen, Semantic Kernel)
