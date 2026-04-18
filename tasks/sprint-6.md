# Sprint 6 — Next.js: Platform Admin

**Goal:** Internal admin dashboard — org management, partner approval, platform health, feedback moderation. Done when Platform Guardian cron runs every 5 min, smoke test passes nightly, and all platform operations can be performed from `/admin`.

Spec refs: `specs/content/guide-admin.md` · `specs/ai-agents.md` · `specs/feedback-community.md` · `specs/privacy.md`

---

## Admin auth + layout

- [ ] `/admin` layout: requires `role = platform_admin` (checked in middleware + layout server component)
- [ ] Non-admin access → redirect to `/dashboard`
- [ ] Admin account bootstrap: on signup with `ADMIN_EMAIL`, auth hook sets `role = platform_admin`
- [ ] Sidebar: **Dashboard** · **Organizations** · **Partners** · **Feedback** · **Agents** · **Settings**

---

## Admin dashboard home (`/admin`)

- [ ] KPI row: total orgs · active Pro orgs · active Integrator orgs · total partners · open feedback items
- [ ] Platform health summary (pulled from latest Platform Guardian `agent_logs` row): API error rate · LLM failure rate · DB latency · daily LLM cost
- [ ] Pending actions panel: partner applications awaiting approval · feedback items with no admin response
- [ ] Recent signups (last 7 days)

---

## Organizations (`/admin/organizations`)

- [ ] Table: org name · plan · created at · agent count · last activity · partner association
- [ ] Filter by plan, date range
- [ ] Search by org name
- [ ] Click org → org detail

**Org detail**
- [ ] Users list (email, joined date)
- [ ] Agents list (name, compliance status, last classified)
- [ ] Billing status: plan · Stripe customer ID · subscription status · `grace_period_ends_at`
- [ ] Partner association (if prescriber-referred)
- [ ] Audit log (last 20 entries)
- [ ] Admin actions:
  - [ ] Override plan (upgrade/downgrade without Stripe — for trials/exceptions)
  - [ ] Associate/reassociate partner
  - [ ] [Export compliance report →] — org-level PDF

---

## Partners (`/admin/partners`)

- [ ] Table: org name · tier · status · client count · co-signature count · created at
- [ ] Filter by status (pending/active/suspended)
- [ ] Click partner → partner detail

**Partner detail**
- [ ] Application info: org type, SME client base, tier requested
- [ ] Scope acceptance status + version
- [ ] Client list with compliance health per client
- [ ] Co-signature transaction history
- [ ] Stripe Connect status

**Approval flow**
- [ ] [Approve] → set `status = active`, `scope_accepted_at = now()` → create partner auth account (email invite) → send "Application approved" email
- [ ] [Reject] → reason field → send rejection email
- [ ] [Suspend] → set `status = suspended` → blocks portal access, preserves all data
- [ ] [Reactivate] → set `status = active`

---

## Feedback (`/admin/feedback`)

- [ ] Table: title · type · votes · status · submitted by (org name) · created at · GitHub issue link
- [ ] Sort by votes (default), date
- [ ] Filter by type (bug/enhancement), status

**Per item actions**
- [ ] [Change status] → dropdown: Open · Planned · In Progress · Done · Declined
- [ ] [Post response] → text input → saved as `admin_response`; `Declined` requires response
- [ ] [Push to GitHub] → server action:
  - Calls GitHub API: `POST /repos/{owner}/{repo}/issues`
  - Title: `[Bug]` or `[Enhancement]` + feedback title
  - Labels: `community` + type
  - Body: vote count · org plan · description · admin response (if any) · link back to `/feedback/[id]`
  - Stores `github_issue_number` + `github_issue_url` in `feedback` row
  - Button becomes link to GitHub issue (no duplicate creation)

---

## Agent logs (`/admin/agents`)

- [ ] Table: agent name · run at · status · summary
- [ ] Filter by agent name, status, date
- [ ] Expand row: full `output` JSON

---

## Platform Guardian — full implementation (`/api/cron/platform-guardian`)

Replace Sprint 3 stub with full implementation:

**Every 5-minute run**
- [ ] Query Vercel/Supabase for: API error rate (last 5 min), LLM call failure rate, DB query p95 latency
- [ ] Compare daily LLM cost against 7-day average — flag if >20% over
- [ ] If any threshold exceeded: send Slack webhook alert + email to `ADMIN_EMAIL`
- [ ] Insert `agent_logs` row: status, summary, output JSON

**Nightly smoke test (02:00 UTC — separate cron or time-gated within guardian)**
- [ ] Step 1: POST to `/api/v1/ingest` with a test SDK token
- [ ] Step 2: Verify session created in DB
- [ ] Step 3: Trigger classification LLM call (Claude Haiku, test agent)
- [ ] Step 4: Trigger document generation
- [ ] Step 5: Verify PDF upload to Supabase Storage + signed URL returned
- [ ] On any step failure: immediate Slack + email alert with which step failed
- [ ] Insert smoke test result in `agent_logs`

---

## Platform settings (`/admin/settings`)

- [ ] **Community link** — URL input (Discord, Telegram, etc.); saved to `platform_settings.community_social_url`; empty = hidden from all user-facing UI
- [ ] **Admin email** — display only (set via env var)

---

## Community social link (user-facing)

- [ ] Read `platform_settings.community_social_url` on dashboard load
- [ ] If set: show "Join our community →" link in sidebar (below Feedback) + on `/feedback` page header
- [ ] If null/empty: link completely hidden — no empty anchor, no placeholder

---

## Slack integration

- [ ] `lib/slack.ts` — `sendAlert(message: string)` → POST to `SLACK_WEBHOOK_URL`
- [ ] Called by Platform Guardian on threshold breach and smoke test failure
- [ ] Graceful failure if Slack webhook is unreachable (log error, do not crash guardian)

---

## Verification

- [ ] Non-admin JWT → `/admin` redirects to `/dashboard`
- [ ] Platform admin signs in → `/admin` loads correctly
- [ ] Partner application approved → partner receives email + can log into partner portal
- [ ] Platform Guardian cron runs (trigger manually) → `agent_logs` row inserted
- [ ] Threshold breach simulation → Slack message received
- [ ] Smoke test triggered manually → all 5 steps pass → `agent_logs` row with `status = success`
- [ ] Feedback item pushed to GitHub → issue created with `community` label → `github_issue_url` stored → button becomes link
- [ ] Community URL set in admin settings → link appears in user dashboard sidebar
- [ ] Community URL cleared → link disappears from all user-facing UI
