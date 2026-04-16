# AI Agents — Trustixy Internal Automation

## Overview

Trustixy runs a set of internal AI agents to monitor platform health, detect behavioral anomalies in customer agent sessions, track regulatory changes, and support the partner network. All are built with the Claude API and run as scheduled serverless functions.

---

## Agent 1 — Anomaly Detection Agent

**Role:** Monitor all customer SDK sessions in real time, detect behavioral anomalies, and surface alerts.

### What it does
- Processes incoming action batches from the ingest API
- Evaluates each session against the project's policy rules and the agent identity's behavioral baseline
- Detects: unexpected scope access, sensitive file patterns, volume spikes, error rate spikes, exfiltration patterns, unusual shell commands
- Creates `anomaly_detections` records with severity classification and structured evidence
- Sends instant alerts for `high` and `critical` severity events (Slack webhook + email)
- Maintains a rolling behavioral baseline per agent identity (session count, action type distribution, top affected resources)

### Triggers
- Continuous processing (event-driven: fires on each ingest batch)
- Nightly baseline recalculation for all active agent identities

### Inputs
- Incoming action batches from the ingest API
- Policy rules per project
- Behavioral baseline per agent identity

### Outputs
- `anomaly_detections` records
- Instant alerts for high/critical severity
- Daily anomaly digest per organization (emailed to admins)

### Admin interaction
- Users acknowledge or dismiss anomalies from the Anomaly Center
- Users can adjust policy rules to tune sensitivity

---

## Agent 2 — Compliance Intelligence Tracker

**Role:** Monitor EU AI Act regulatory developments, assess their impact on registered systems, and keep the platform up to date.

### What it does
- Fetches and parses monitored sources (see Sources below)
- Filters noise: press opinions, political statements, minor FAQs do not trigger action
- Summarizes confirmed changes in plain language and assesses impact on registered AI systems
- Runs regulatory impact assessment against each active AI system
- Creates draft `regulatory_version` entries pending admin approval
- Once approved: auto-creates `compliance_alerts` for affected systems, sets affected systems to `needs_review`
- Flags when classification prompts or compliance document templates may need updating
- Generates a weekly regulatory briefing for admin review

### Triggers
- Weekly (every Monday at 06:00 UTC)
- Manual trigger from admin dashboard

### Sources monitored

**Primary (trigger regulatory_version creation):**
- EUR-Lex RSS feed — official EU publications
- European AI Office — official guidelines, enforcement decisions
- CEN/CENELEC — harmonised technical standards for high-risk AI systems

**Secondary (briefing only, no regulatory_version creation):**
- EDPB — opinions on AI Act / GDPR intersection
- Bird & Bird, Fieldfisher, Linklaters AI regulatory blogs
- IAPP — AI compliance news
- AlgorithmWatch, Politico Tech

### Key regulatory deadlines (pre-seeded)

| Date | Milestone |
|---|---|
| August 2024 | AI Act enters into force |
| February 2025 | Prohibition on unacceptable-risk systems |
| August 2025 | GPAI model obligations |
| August 2026 | High-risk obligations (Annex III) |
| August 2027 | High-risk obligations (Annex I) |

### Outputs
- Draft `regulatory_version` entries for admin approval
- `compliance_alerts` per affected system (after admin approval)
- Weekly regulatory briefing (admin review, optionally forwarded to partners)

### Admin interaction
- Admin reviews draft `regulatory_version` entries and approves, edits, or rejects
- Admin can forward briefing to partners as a value-add communication

---

## Agent 3 — Platform Guardian

**Role:** Monitor platform health, detect operational anomalies, and ensure availability.

### What it does
- Monitors API response times, error rates, LLM call failures
- Detects unusual activity (spike in failed classifications, ingest failures)
- Monitors Supabase health (storage, query latency)
- Monitors LLM costs and flags budget overruns
- Runs nightly smoke tests on critical flows (signup, SDK ingest, classify, generate doc)

### Triggers
- Continuous monitoring (every 5 minutes for critical metrics)
- Nightly full smoke test

### Outputs
- Instant alert on critical failure
- Daily platform health summary
- Monthly cost and usage report

### Admin interaction
- Alerts via Slack/email with severity + suggested fix
- Admin can trigger manual smoke test

---

## Agent 4 — Customer Success Assistant

**Role:** Proactive, personalized support for users and partners.

### What it does
- Tracks user milestones: SDK installed, first session ingested, first classification, first document generated
- Detects SDK installed but no data flowing (misconfiguration)
- Detects users who signed up but never installed the SDK
- Detects compliance users stuck at classification
- Prepares a daily admin briefing: who to reach out to, who is at risk of churning
- Drafts check-in messages for admin review and send

### Triggers
- Daily

### Outputs
- Daily "Focus list": top 5 users/partners to reach out to today
- Draft messages (admin reviews before sending)
- Monthly churn risk report

### Admin interaction
- Prioritized daily action list
- One-click approve and send for draft messages

---

## Agent 5 — Partner Success Monitor

**Role:** Track partner activity and flag accounts that need attention.

### What it does
- Monitors partner portal activity: logins, client invitations, client activations, co-signatures
- Detects inactive partners (no client invited in 30 days)
- Detects stuck clients (invited but not activated after 7 days)
- Flags partners showing high engagement (potential upsell)
- Generates weekly partner health report

### Triggers
- Daily (alerts)
- Weekly (health report)

### Outputs
- Daily alert for critical issues (partner churning, client stuck)
- Weekly partner health summary
- Suggested actions per partner

### Admin interaction
- Daily digest with one-click actions
- Admin can snooze alerts per partner

---

## Agent 6 — Prescriber Hunter

**Role:** Proactively identify and qualify new potential prescriber partners.

### What it does
- Searches company directories and professional databases for target profiles
- Scores prospects based on fit criteria: sector, SME client base, EU market presence
- Drafts personalized outreach per qualified prospect
- Adds qualified prospects to CRM pipeline

### Triggers
- Weekly
- Also triggered when a new prescriber tier is added to the strategy

### Outputs
- Weekly shortlist of 10 qualified prospects with outreach drafts

### Admin interaction
- Admin reviews and approves/rejects prospects
- Admin approves outreach before sending

---

## Summary Table

| Agent | Role | Frequency | Key Output |
|---|---|---|---|
| Anomaly Detection | Detect behavioral anomalies in customer sessions | Continuous | Anomaly records + alerts |
| Compliance Intelligence Tracker | Track EU AI Act changes | Weekly | Regulatory briefing + platform impact |
| Platform Guardian | Monitor uptime & costs | Continuous / Nightly | Incident alerts + health summary |
| Customer Success Assistant | Proactive user & partner support | Daily | Focus list + draft messages |
| Partner Success Monitor | Track partner health | Daily / Weekly | Partner health report + action alerts |
| Prescriber Hunter | Find & qualify new partners | Weekly | 10 qualified prospects + outreach drafts |

---

## Technical Approach

- All agents built with **Claude API** (claude-opus-4-6 for reasoning-heavy tasks, claude-haiku-4-5 for lightweight monitoring)
- Run as **scheduled serverless functions** (Vercel Cron or Supabase Edge Functions)
- Agent outputs stored in `agent_logs` table
- Anomaly Detection Agent also writes to `anomaly_detections`
- Admin interactions via lightweight **Admin Dashboard** (internal Next.js route, protected)
- Alerts via **Slack webhook** and/or **email** (Resend or SendGrid)
