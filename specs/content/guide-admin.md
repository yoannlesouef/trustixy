# User Guide — Trustixy Platform Admin

## Who this guide is for

This guide is for the Trustixy platform administrator — the person responsible for running the Trustixy SaaS product. This is an internal role, not accessible to end users or prescriber partners.

The admin dashboard is available at `/admin` (protected route, admin users only).

---

## 1. Admin Dashboard Overview

The admin dashboard gives you a real-time view of the platform:

- **Active organizations**: total registered, by plan, by acquisition channel (direct vs. prescriber-referred)
- **Active partners**: by tier, by status (pending / active / suspended)
- **Platform health**: API uptime, error rate, LLM response times, cost per day
- **AI agents status**: last run time and status for each internal agent
- **Pending actions**: items waiting for your approval (outreach drafts, partner applications, etc.)

---

## 2. Managing Partners

### Approving partner applications
New partner applications appear in **Partners → Pending**. For each application:
1. Review the organization name, type, and requested tier
2. Check fit: sector, SME client base, EU market presence
3. Confirm the applicant accepted the Co-signature Scope Document (checkbox in application)
4. Approve (activates the partner account, sets `scope_accepted_at`, triggers welcome email) or Reject (with reason)

### Editing partner settings
- Upgrade or downgrade tier
- Suspend a partner account (blocks portal access but preserves client data)
- View co-signature transaction history
- View client list linked to the partner

### Monitoring partner health
The Partner Success Monitor agent produces a daily digest and weekly health report. From the admin dashboard:
- View active partners, inactive partners (no client invited in 30 days), high-engagement partners
- One-click actions: send re-engagement email, schedule call, mark as handled

---

## 3. AI Agent Dashboard

Each internal agent logs its runs in the `agent_logs` table. The admin dashboard displays:

### Sensitive File Detector
- Total alerts fired today / this week
- False positive rate (if users are bulk-dismissing)

**Your action:** Monitor dismissal rate. If users dismiss >80% of alerts, review the detection patterns.

### Platform Guardian
- Current status: uptime %, error rate, LLM call failure rate, DB query latency
- Cost tracking: daily LLM spend, trend vs. last 7 days
- Nightly smoke test results: SDK ingest → classify → generate document flow

**Your action:** Respond to critical alerts immediately. For cost overruns, identify which organizations or features are driving volume.

### Customer Success Assistant
- Daily focus list: top 5 users/partners to reach out to today
- Each item: organization name, trigger (onboarding drop-off, classification stuck, etc.), draft message
- Actions: approve and send | edit then send | skip | mark as handled

**Your action:** Every morning, work through the focus list.

### Partner Success Monitor
- Daily alerts: partners at risk of churning, clients stuck after invite
- Weekly health report: partner activity summary, co-signature adoption rate
- Upsell flags: partners showing high engagement

**Your action:** Act on critical daily alerts first. Review weekly report on Mondays.

### Prescriber Hunter
- Weekly prospect shortlist: 10 qualified prospects with outreach drafts
- Each prospect: firm name, sector, SME client base estimate, fit score
- Actions: approve outreach | edit | reject | mark as already contacted

**Your action:** Review the weekly shortlist. Approve outreach for best-fit prospects.

---

## 4. Managing Organizations

### View all organizations
**Organizations** lists all registered organizations with: plan, acquisition channel, agent count, compliance health, last activity.

### Organization detail
Click any organization to see:
- Users and their emails
- Agents and compliance statuses
- Partner association (if prescriber-referred)
- Subscription plan and billing status
- Audit log

### Interventions
- Upgrade or downgrade plan (e.g. for a trial extension)
- Associate or reassociate with a partner
- Export the organization's full compliance report

---

## 5. Platform Monitoring

### Health metrics (Platform Guardian)
Available at **Platform → Health**:
- API response time (p50, p95, p99)
- Error rate by endpoint
- LLM call success rate and latency
- Supabase DB health: storage used, query latency

### Cost monitoring
Available at **Platform → Costs**:
- Daily LLM spend (classification calls vs. document generation calls)
- Cost per organization (to identify high-usage accounts)
- Monthly projection vs. budget

### Smoke test results
The Platform Guardian runs a nightly smoke test:
1. SDK ingest call
2. Agent auto-discovery
3. Classification (LLM call)
4. Document generation (LLM call)
5. PDF export

Results logged in **Platform → Smoke tests**. Failed tests trigger an immediate Slack + email alert.

---

## 6. Frequently Asked Questions

**What should I do when the Platform Guardian reports a critical failure?**
Check the alert for severity and suggested fix. For LLM unavailability, check the Anthropic status page. For DB issues, check the Supabase dashboard.

**How do I handle a partner who is approving co-signatures too fast?**
Check the partner's audit trail in the partner detail view. If approvals show no "request changes" events and unusually fast turnaround, flag the partner for a quality review conversation. In severe cases, suspend the account.

**What if a user disputes a classification result?**
Remind them the classification is AI-assisted and not a legal determination. If the confidence score was low, recommend requesting a prescriber review. If the agent description was incomplete, encourage them to update it and reclassify.

**How do I update an internal agent's logic or prompts?**
Agents are serverless functions deployed on Vercel Cron or Supabase Edge Functions. See `specs/ai-agents.md` for architecture. LLM prompt updates (classification, document generation) should be reviewed by an EU AI Act expert before deployment.
