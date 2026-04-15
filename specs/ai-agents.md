# AI Agents — Trustixy Internal Automation

## Overview

A set of AI agents runs behind the scenes to help the Trustixy administrator grow the partner network, monitor the platform, and focus time on high-value customer success activities rather than manual operational tasks.

---

## Agent 1 — Prescriber Hunter

**Role:** Proactively identify and qualify new potential prescriber partners.

### What it does
- Searches LinkedIn, company directories, and professional databases for target profiles (accounting firms, IT integrators, law firms, federations)
- Scores each prospect based on fit criteria: sector, SME client base, size, EU market presence
- Drafts a personalized outreach message for each qualified prospect
- Adds qualified prospects to a CRM pipeline with context notes

### Triggers
- Runs weekly
- Also triggered when a new prescriber tier or vertical is added to the strategy

### Inputs
- Prescriber profile definitions from `specs/prescriber-partnership.md`
- Existing partner list (to avoid duplicates)
- Target geographies and sectors

### Outputs
- Weekly shortlist of 10 qualified prospects with contact info and outreach drafts
- Enriched CRM entries ready for admin review and send

### Admin interaction
- Admin reviews and approves/rejects prospects
- Admin approves outreach message before sending
- Admin marks leads as contacted, in discussion, or converted

---

## Agent 2 — Partner Success Monitor

**Role:** Track partner activity and flag accounts that need attention.

### What it does
- Monitors partner portal activity: logins, client invitations sent, client activations
- Detects inactive partners (no client invited in 30 days)
- Detects stuck clients (invited but not activated after 7 days)
- Flags partners showing high engagement (potential upsell to higher tier)
- Generates a weekly partner health report

### Triggers
- Runs daily for alerts
- Runs weekly for the health report

### Inputs
- Partner activity data from the database
- Partner tier and commission data

### Outputs
- Daily Slack/email alert for critical issues (partner churning, client stuck)
- Weekly partner health dashboard summary for the admin
- Suggested actions per partner (re-engage, upsell, celebrate milestone)

### Admin interaction
- Admin receives daily digest with one-click actions (send re-engagement email, schedule call)
- Admin can snooze alerts for specific partners

---

## Agent 3 — Platform Guardian

**Role:** Monitor platform health, detect anomalies, and ensure the product stays operational.

### What it does
- Monitors API response times, error rates, and LLM call failures
- Detects unusual activity (spike in failed classifications, document generation errors)
- Checks Supabase DB health (storage, query latency)
- Monitors LLM costs and flags budget overruns
- Runs nightly smoke tests on critical user flows (signup, classify, generate doc)

### Triggers
- Continuous monitoring (every 5 minutes for critical metrics)
- Nightly full smoke test

### Inputs
- Application logs and error tracking
- Supabase metrics
- Claude API usage and cost data

### Outputs
- Instant alert on critical failure (API down, LLM unavailable)
- Daily platform health summary
- Monthly cost and usage report

### Admin interaction
- Admin receives alerts via Slack/email with severity level and suggested fix
- Admin can trigger a manual smoke test run

---

## Agent 4 — Customer Success Assistant

**Role:** Help the admin deliver proactive, personalized support to end users and partners.

### What it does
- Tracks user milestones: first system created, first classification, first document generated
- Sends automated congratulation and tip messages at key milestones
- Detects users who signed up but never created a system (onboarding drop-off)
- Detects users stuck at classification (started but never completed)
- Prepares a daily admin briefing: who to call, who to email, who is at risk of churning
- Drafts check-in messages for the admin to review and send

### Triggers
- Runs daily

### Inputs
- User activity data from the database
- Subscription plan (free vs pro vs enterprise)
- Partner association (is the user a partner's client?)

### Outputs
- Daily "Focus list": top 5 users/partners the admin should reach out to today
- Draft messages ready to send (admin reviews before sending)
- Monthly churn risk report

### Admin interaction
- Admin starts each day with a prioritized action list
- One-click approve and send for draft messages
- Admin marks users as "handled" to keep the list clean

---

## Agent 5 — Compliance Intelligence Tracker

**Role:** Monitor EU AI Act regulatory developments, assess their impact on registered systems, and keep the platform up to date.

### What it does
- Fetches and parses monitored sources (see Sources below)
- Filters out noise: press opinions, political statements, and minor FAQs are ignored — only material regulatory changes trigger action
- Summarizes confirmed changes in plain language and assesses their impact on registered AI systems
- Runs the regulatory impact assessment LLM prompt (see `specs/llm-prompts.md`) against each active AI system to determine which are affected
- Creates a new `regulatory_version` entry for each confirmed material update (pending admin approval before activation)
- Auto-creates `compliance_alerts` for affected organizations and systems once a version is approved (sets `source_agent_log_id`)
- Auto-sets affected systems to `needs_review`
- Flags when classification prompts or compliance document templates may need updating
- Generates a weekly regulatory briefing for the admin, optionally forwarded to partners

### Triggers
- Runs weekly (every Monday at 06:00 UTC)
- Can be triggered manually by the admin for an on-demand scan

### Sources monitored

#### Primary sources (authoritative — trigger regulatory_version creation)
- **EUR-Lex RSS feed** — official EU publications: delegated acts, implementing acts, corrigenda
  - Feed: `https://eur-lex.europa.eu/tools/rss/RSS_EN.xml` filtered on AI Act identifiers
- **European AI Office** — official guidelines, FAQ updates, enforcement decisions
  - Site: `digital-strategy.ec.europa.eu/en/policies/ai-office`
- **CEN/CENELEC** — harmonised technical standards for high-risk AI systems
  - Monitored for new EN standards referencing the AI Act

#### Secondary sources (informational — used for briefing only, not for regulatory_version creation)
- **EDPB (European Data Protection Board)** — opinions on AI Act / GDPR intersection
- **Bird & Bird** AI regulatory blog
- **Fieldfisher** AI & data law updates
- **Linklaters** digital regulation publications
- **IAPP** (iapp.org) — AI compliance news
- **AlgorithmWatch** (algorithmwatch.org)
- **Politico Tech** — EU digital policy coverage

#### Signal filters (what NOT to act on)
- Press articles and journalist opinions
- Political speeches or declarations without a published official text
- Announcements of upcoming consultations (monitor but do not trigger)
- Minor FAQ clarifications with no change to obligations or risk categories

### Key regulatory deadlines (pre-seeded in regulatory_versions)

| Date | Milestone | Impact |
|---|---|---|
| August 2024 | AI Act enters into force | Baseline version |
| February 2025 | Prohibition on unacceptable-risk systems | Systems classified as Unacceptable must be flagged |
| August 2025 | GPAI model obligations | General-purpose AI model providers |
| August 2026 | High-risk obligations (Annex III) | Most SME-relevant high-risk systems |
| August 2027 | High-risk obligations (Annex I) | Safety-component systems |

These dates are encoded as `regulatory_versions` entries at platform launch. As each date approaches (90-day and 30-day warnings), the agent generates proactive alerts for organizations with affected systems.

### Inputs
- RSS feeds and web sources listed above
- Current LLM prompts from `specs/llm-prompts.md`
- All active AI systems from the database (for impact assessment)
- Current `regulatory_versions` table (to detect what is already known and avoid duplicates)

### Outputs
- Draft `regulatory_version` entry submitted for admin approval (not yet active)
- Once approved: `compliance_alerts` created for each affected system (linked via `source_agent_log_id`)
- Affected systems set to `needs_review`
- Weekly regulatory briefing (plain language, impact assessment) for admin review
- Draft updates to LLM prompts or compliance templates flagged for admin review

### Admin interaction
- Admin receives the weekly briefing with a summary of sources checked and findings
- Admin reviews draft `regulatory_version` entries and approves, edits, or rejects before they activate
- Admin approves any proposed LLM prompt changes before deployment
- Admin can forward the briefing to partners as a value-add communication
- Admin can trigger a manual scan from the admin dashboard

---

## Summary Table

| Agent | Role | Frequency | Key Output |
|---|---|---|---|
| Prescriber Hunter | Find & qualify new partners | Weekly | 10 qualified prospects + outreach drafts |
| Partner Success Monitor | Track partner health | Daily / Weekly | Partner health report + action alerts |
| Platform Guardian | Monitor platform uptime & costs | Continuous / Nightly | Incident alerts + health summary |
| Customer Success Assistant | Proactive user & partner support | Daily | Focus list + draft messages |
| Compliance Intelligence Tracker | Track EU AI Act changes | Weekly | Regulatory briefing + product impact |

---

## Technical Approach

- All agents are built using the **Claude API** (claude-opus-4-6 for reasoning-heavy tasks, claude-haiku-4-5 for lightweight monitoring)
- Agents run as **scheduled serverless functions** (Vercel Cron or Supabase Edge Functions)
- Agent outputs are stored in a dedicated `agent_logs` table in Supabase
- Admin interactions happen via a lightweight **Admin Dashboard** (internal Next.js route, protected)
- Alerts are delivered via **Slack webhook** and/or **email** (Resend or SendGrid)

---

## Agent Data Model

The `agent_logs` table is defined in `specs/data-model.md`. The `compliance_alerts` table includes a `source_agent_log_id` foreign key to trace which agent run triggered each alert — providing full auditability of automated compliance decisions.
