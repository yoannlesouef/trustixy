# AI Agents — Trustixy Internal Automation

## Overview

Trustixy runs internal AI agents for platform monitoring, customer success, and partner growth. All are built with the Claude API and run as scheduled serverless functions. Outputs are stored in `agent_logs` and surfaced via an internal admin dashboard.

---

## Agent 1 — Sensitive File Detector

**Role:** Scan incoming action batches for access to credential and secret files, create alerts.

### What it does
- Processes incoming action batches from the ingest API
- Matches `affected_resources` against sensitive patterns: `**/.env`, `**/secrets/**`, `**/*.pem`, `**/credentials*`, `**/.ssh/**`
- Creates `sensitive_file_alerts` records with `high` severity for every match
- Sends instant email notification to the organization admin

### Triggers
- Event-driven: fires on each ingest batch (no LLM involved — pure pattern matching)

### Inputs
- Incoming action batches from the ingest API

### Outputs
- `sensitive_file_alerts` records
- Instant email notification for each new alert

### No LLM needed
Pattern matching only. Fast, deterministic, no LLM cost.

---

## Agent 2 — Platform Guardian

**Role:** Monitor platform health, detect operational anomalies, ensure availability.

### What it does
- Monitors API response times, error rates, LLM call failures
- Detects unusual activity (spike in failed classifications, ingest failures)
- Monitors Supabase health (storage, query latency)
- Monitors LLM costs and flags budget overruns
- Runs nightly smoke tests on critical flows (signup, SDK ingest, classify, generate doc)

### Triggers
- Continuous (every 5 minutes for critical metrics)
- Nightly full smoke test

### Outputs
- Instant alert on critical failure (Slack + email)
- Daily platform health summary
- Monthly cost and usage report

---

## Agent 3 — Customer Success Assistant

**Role:** Proactive, personalized support for users and partners.

### What it does
- Tracks user milestones: SDK installed, first session ingested, first classification, first document generated
- Detects SDK installed but no data flowing (misconfiguration)
- Detects users who signed up but never installed the SDK
- Detects compliance users who classified but never generated a document
- Prepares a daily admin briefing: who to reach out to, who is at risk of churning
- Drafts check-in messages for admin review and send

### Triggers
- Daily

### Outputs
- Daily "Focus list": top 5 users/partners to reach out to today
- Draft messages (admin reviews before sending)
- Monthly churn risk report

---

## Agent 4 — Partner Success Monitor

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
- Weekly partner health summary with suggested actions

---

## Agent 5 — Prescriber Hunter

**Role:** Proactively identify and qualify new potential prescriber partners.

### What it does
- Searches company directories and professional databases for target profiles
- Scores prospects based on fit criteria: sector, SME client base, EU market presence
- Drafts personalized outreach per qualified prospect
- Adds qualified prospects to CRM pipeline

### Triggers
- Weekly

### Outputs
- Weekly shortlist of 10 qualified prospects with outreach drafts (admin approves before sending)

---

## Summary

| Agent | Role | Frequency | Key Output |
|---|---|---|---|
| Sensitive File Detector | Flag credential file access | Event-driven (per ingest) | Alerts + email |
| Platform Guardian | Monitor uptime & costs | Continuous / Nightly | Incident alerts + health summary |
| Customer Success Assistant | Proactive user & partner support | Daily | Focus list + draft messages |
| Partner Success Monitor | Track partner health | Daily / Weekly | Partner health report |
| Prescriber Hunter | Find & qualify new partners | Weekly | 10 prospects + outreach drafts |

---

## Technical Approach

- Sensitive File Detector: pure pattern matching, no LLM
- All other agents: **Claude API** (`claude-sonnet-4-6` for reasoning tasks, `claude-haiku-4-5` for lightweight monitoring)
- Run as **scheduled serverless functions** (Vercel Cron or Supabase Edge Functions)
- All outputs stored in `agent_logs` table
- Alerts via **Slack webhook** and **email** (Resend or SendGrid)
- Admin interactions via internal Next.js admin route (protected)
