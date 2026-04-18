# Admin Guide — Trustixy Platform

Internal role only. Dashboard at `/admin` (requires `role=platform_admin`).

---

## 1. Daily Routine

Every morning, in order:

1. **Platform health** — check Platform Guardian alert digest (Slack + `/admin`)
2. **Stripe** — scan for failed payments, past-due subscriptions
3. **Pending actions** — approve or reject partner applications
4. **User activity** — scan `/admin/organizations` for orgs with no recent activity
5. **Feedback board** — check `/admin/feedback` for new submissions and votes

Weekly (Monday):
- NPM download trend (npmjs.com dashboard)
- Partner health — manually check partner list for inactive accounts

---

## 2. User & Org Activity

**Where:** `/admin/organizations`

Key signals:
- Orgs with no agent activity in 7+ days → at-risk, reach out manually
- Orgs with stuck classifications → check `agent_logs` for errors
- New signups this week vs. last week

**Org detail view** (click any org):
- Users, agents, compliance statuses
- Billing status and plan
- Partner association
- Audit log
- Actions: upgrade/downgrade plan, reassociate partner, export compliance report

---

## 3. Platform Health

**Where:** `/admin` → Platform section, or Slack alerts

| Metric | Alert threshold |
|---|---|
| API error rate | > 1% triggers alert |
| LLM call failure rate | > 5% triggers alert |
| DB query latency (p95) | > 500ms triggers alert |
| Daily LLM cost | > 20% above 7-day average triggers alert |

**Nightly smoke test** (Platform Guardian, 02:00 UTC):
Runs the full flow: SDK ingest → auto-discovery → classification → document generation → PDF export.
Failed steps trigger immediate Slack + email alert.

**What to do on critical failure:**
- LLM unavailability → check Anthropic status page, no action needed until resolved
- DB issues → check Supabase dashboard
- Cost overruns → check `/admin` → Costs → identify high-usage orgs

---

## 4. Stripe Billing

**Where:** Stripe Dashboard + `/admin/organizations` for per-org billing status

### Revenue monitoring (Stripe Dashboard)
- **MRR** — Stripe Dashboard → Revenue → Monthly Recurring Revenue
- **Active subscriptions** — breakdown by Pro (€99) vs. Integrator (€399)
- **Failed payments** — Stripe Dashboard → Billing → Failed payments (check weekly)
- **Partner payouts** — Stripe Connect → Transfers (per co-signature)

### Failed payment handling
When `invoice.payment_failed` fires:
- Day 1: automated payment failure email sent
- Day 5: automated reminder email
- Day 7: access downgraded to free plan automatically

**Your action:** If a high-value org reaches day 5 with no payment, consider a manual outreach before day 7 downgrade.

### Key Stripe links to bookmark
- Failed payments: `dashboard.stripe.com/billing/failed-payments`
- Connect transfers (partner payouts): `dashboard.stripe.com/connect/transfers`
- Revenue overview: `dashboard.stripe.com/revenue`

---

## 5. NPM Downloads (SDK Adoption)

**Where:** [npmjs.com/package/@trustixy/sdk](https://www.npmjs.com/package/@trustixy/sdk)

Weekly signal: weekly download count trend.

| Signal | What it means |
|---|---|
| Downloads growing | SDK adoption healthy |
| Downloads flat for 2+ weeks | Check docs, SDK changelog, GitHub issues |
| Spike | Track source — blog post, partner integration, or community mention |

---

## 6. Partner Management

**Where:** `/admin/partners`

### Approving applications
1. Review org name, type, requested tier
2. Confirm EU market presence and SME client base
3. Verify Co-signature Scope Document accepted
4. Approve (activates account, triggers welcome email) or Reject (with reason)

### Partner actions
- Upgrade/downgrade tier
- Suspend (blocks portal access, preserves data)
- View co-signature history and client list

### Health signals to check manually (weekly)
- Partners with no client invited in 30 days → send re-engagement email
- Partners with high co-signature volume → flag for upsell conversation

---

## 7. Feedback Board

**Where:** `/admin/feedback`

Ranked by votes. Actions per item:
- **Change status** — `Open` → `Planned` → `In Progress` → `Done` | `Declined`
- **Post team response** — visible to all users on the item
- **Push to GitHub** — creates a GitHub issue tagged `community` (works on private repos)

`Declined` status requires a written admin response explaining why.

---

## 8. AI Agent Logs

**Where:** `/admin/agents`

| Agent | Cadence | Action if issues |
|---|---|---|
| Sensitive File Detector | Per ingest | Monitor dismissal rate; >80% → review patterns |
| Platform Guardian | Every 5 min / Nightly | Respond to critical alerts immediately |

If an agent fails silently (no logs for >2 intervals), check Vercel Cron logs and confirm `CRON_SECRET` is set.

---

## 9. Common Interventions

| Situation | Where | Action |
|---|---|---|
| User disputes classification | Org detail → agent | Explain AI-assisted, not legal determination; suggest prescriber review if confidence low |
| Partner approving co-signatures suspiciously fast | Partner detail → audit trail | Review, flag for quality conversation, suspend if severe |
| LLM cost spike | Admin → Costs | Identify high-usage org, check for classification loop or runaway agent |
| Failed smoke test | Admin → Smoke tests | Check Anthropic + Supabase status first |
| Update agent prompts | `specs/ai-agents.md` | EU AI Act review required before deploying prompt changes |
