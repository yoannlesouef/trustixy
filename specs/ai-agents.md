# AI Agents — Trustixy Internal Automation

Two internal agents run as scheduled serverless functions. Outputs stored in `agent_logs`, surfaced in the admin dashboard.

---

## Agent 1 — Sensitive File Detector

**Role:** Flag agent access to credential and secret files in real time.

- Pattern-matches `affected_resources` on each ingest batch against: `**/.env`, `**/secrets/**`, `**/*.pem`, `**/credentials*`, `**/.ssh/**`
- Creates `sensitive_file_alerts` records (severity: `high`)
- Sends instant email to the org admin

**Trigger:** Event-driven — fires on each ingest batch  
**LLM:** None — pure pattern matching, deterministic, no cost

---

## Agent 2 — Platform Guardian

**Role:** Monitor platform health, detect operational anomalies, track costs.

- Monitors API response times, error rates, LLM call failure rate
- Monitors Supabase health (storage, query latency)
- Monitors daily LLM spend and flags budget overruns (>20% above 7-day average)
- Runs nightly smoke test: SDK ingest → classification → document generation → PDF export

**Triggers:** Every 5 minutes (critical metrics) + nightly smoke test  
**Outputs:**
- Instant Slack + email alert on critical failure
- Daily platform health summary
- Monthly cost and usage report

---

## Summary

| Agent | Role | Frequency | Output |
|---|---|---|---|
| Sensitive File Detector | Flag credential file access | Per ingest batch | Alerts + org admin email |
| Platform Guardian | Uptime, cost, smoke tests | Every 5 min / Nightly | Incident alerts + health summary |

---

## Technical Approach

- Sensitive File Detector: no LLM
- Platform Guardian: **Claude API** (`claude-haiku-4-5` for lightweight monitoring tasks)
- Run as **Vercel Cron** (Platform Guardian) and event-driven inline code (Sensitive File Detector)
- All outputs stored in `agent_logs`
- Alerts via Slack webhook + Resend email
