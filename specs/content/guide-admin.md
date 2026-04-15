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
- **AI agents status**: last run time and status for each of the 5 agents
- **Pending actions**: items waiting for your approval (outreach drafts, regulatory updates, etc.)

---

## 2. Managing Partners

### Approving partner applications
New partner applications appear in **Partners → Pending**. For each application:
1. Review the organization name, type, and requested tier
2. Check fit: sector, SME client base, EU market presence
3. Approve (activates the partner account and triggers welcome email) or Reject (with reason)

### Editing partner settings
- Upgrade or downgrade tier
- Suspend a partner account (blocks portal access but preserves client data)
- View commission history
- View client list linked to the partner

### Monitoring partner health
The Partner Success Monitor agent produces a daily digest and weekly health report. From the admin dashboard:
- View the current health report: active partners, inactive partners (no client invited in 30 days), high-engagement partners (upsell candidates)
- One-click actions: send re-engagement email, schedule call, mark as handled
- Snooze alerts for specific partners if you have already followed up

---

## 3. Managing Regulatory Versions

Regulatory versions are the foundation of Trustixy's compliance traceability. Each time the EU AI Act is updated, a new regulatory version entry must exist.

### How regulatory versions are created
The **Compliance Intelligence Tracker** agent automatically creates new `regulatory_version` entries when it detects a published update. These appear in **Regulatory → Pending review** for your approval before they become active.

### Reviewing a new regulatory version
1. Go to **Regulatory → Pending review**
2. Review the agent's summary: what changed, which use cases are affected
3. Review the proposed impact assessment per system category
4. **Approve**: the version becomes active, affected organizations receive compliance alerts, their systems are set to `needs_review`
5. **Edit then approve**: correct the summary or affected use cases before activating
6. **Reject**: if the detected "update" is not materially significant (minor clarification, FAQ, etc.)

### Manual entry
You can also create a regulatory version manually from **Regulatory → Add version**. Use this for urgent updates detected outside the agent's monitoring cycle.

---

## 4. AI Agent Dashboard

Each of the 5 agents logs its runs in the `agent_logs` table. The admin dashboard displays:

### Platform Guardian
- Current status: uptime %, error rate, LLM call failure rate, DB query latency
- Cost tracking: daily LLM spend, trend vs. last 7 days
- Instant alerts: any critical failure triggers a Slack/email notification immediately
- Nightly smoke test results: signup → classify → generate document flow

**Your action:** Respond to critical alerts. For cost overruns, review which organizations or features are driving volume.

### Compliance Intelligence Tracker
- Last run: date, sources checked, updates detected
- Regulatory versions created (pending your approval — see Section 3)
- Organizations and systems flagged this week

**Your action:** Review and approve new regulatory versions before they trigger alerts to users.

### Customer Success Assistant
- Daily focus list: top 5 users/partners to reach out to today
- Each item: organization name, what triggered the flag (onboarding drop-off, classification stuck, etc.), draft message ready to send
- Actions: approve and send | edit then send | skip | mark as handled

**Your action:** Every morning, work through the focus list. Aim to process all items before end of day.

### Partner Success Monitor
- Daily alerts: partners at risk of churning, clients stuck
- Weekly health report: partner activity summary, co-signature adoption rate
- Upsell flags: partners showing high engagement, recommended for tier upgrade conversation

**Your action:** Act on critical daily alerts first (churning partners). Review weekly report on Mondays.

### Prescriber Hunter
- Weekly prospect shortlist: 10 qualified prospects with contact info and outreach draft
- Each prospect: firm name, sector, SME client base estimate, EU presence, fit score
- Actions per prospect: approve outreach | edit outreach | reject | mark as already contacted

**Your action:** Review the weekly shortlist. Approve outreach drafts for the best-fit prospects. Track responses manually in your CRM.

---

## 5. Managing Organizations

### View all organizations
**Organizations** lists all registered organizations with: plan, acquisition channel, systems count, compliance health, last activity.

### Organization detail
Click any organization to see:
- Users (with roles)
- AI systems and compliance statuses
- Partner association (if referred by a prescriber)
- Subscription plan and billing status
- Audit log

### Interventions
- Upgrade or downgrade plan (e.g. for a trial extension)
- Associate or reassociate with a partner
- Send a manual compliance alert
- Export the organization's full compliance report

---

## 6. Platform Monitoring

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
The Platform Guardian runs a nightly smoke test covering:
1. User signup flow
2. AI system creation
3. Classification (LLM call)
4. Document generation (LLM call)
5. PDF export

Results are logged and displayed in **Platform → Smoke tests**. Failed tests trigger an immediate alert.

---

## 7. Sending Regulatory Briefings to Partners

When the Compliance Intelligence Tracker produces its weekly regulatory briefing, you can forward it to partners as a value-add communication.

1. Go to **Regulatory → Briefings**
2. Review the current draft briefing
3. Edit if needed (add context, adjust tone)
4. Select recipients: all active partners / specific tiers / specific partners
5. Click **Send** — partners receive it as a branded email from Trustixy

This positions your partners as informed advisors and increases the likelihood they proactively engage their clients on compliance updates.

---

## 8. Frequently Asked Questions

**What should I do when the Platform Guardian reports a critical failure?**
Check the alert for severity and suggested fix. For LLM unavailability, check the Anthropic status page. For DB issues, check the Supabase dashboard. The Platform Guardian provides a suggested action with each alert.

**How do I handle a partner who is billing clients for co-signature but not actually reviewing documents?**
Check the partner's audit trail in the partner detail view. If co-signatures are being approved without any "request changes" events and with unusually fast turnaround, flag the partner for a quality review conversation. In severe cases, suspend the partner account.

**What if a user disputes a classification result?**
Remind them that the classification is AI-assisted and not a legal determination. If the confidence score was low, recommend requesting a prescriber review. If the system description was incomplete, encourage them to update it and re-classify.

**How do I add a new agent or update an existing one?**
Agents are serverless functions deployed on Vercel Cron or Supabase Edge Functions. See the technical documentation in `specs/ai-agents.md` for the architecture. Prompt updates (e.g. for the Compliance Intelligence Tracker) should be reviewed by an EU AI Act expert before deployment.
