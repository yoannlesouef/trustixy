# Sprint 4 — Next.js: Organization Dashboard

**Goal:** The full experience for developers, security, and compliance teams. Done when a user can go from signup → SDK install → first session → classify → document → co-signature request → Pro upgrade in under 15 minutes.

Spec refs: `specs/ux.md` · `specs/features.md` · `specs/onboarding.md` · `specs/emails.md` · `specs/stripe.md` · `specs/pdf.md` · `specs/feedback-community.md`

---

## Onboarding screen (`/onboarding`)

- [ ] Full-page empty state on first login: "See what your AI agents are doing in 5 minutes"
- [ ] Framework selector: Anthropic · OpenAI · Claude Code · Other
- [ ] On select: 3-line code snippet with pre-filled SDK token
- [ ] Copy button
- [ ] "Waiting for first session…" polling state — redirect to Activity when first session arrives
- [ ] Prescriber-referred users: prescriber pre-linked in background, skip any directory step in co-signature later

---

## Navigation

- [ ] Sidebar: **Activity** · **Agents** · **Alerts** (badge when open alerts exist)
- [ ] Settings link at bottom of sidebar
- [ ] User menu (email, sign out)

---

## Activity page (`/dashboard`)

- [ ] Session list — all sessions across all agents, newest first
- [ ] Columns: agent name · environment badge · started at · duration · action count · status
- [ ] Filter by: agent · environment · status · time range
- [ ] Click session → session detail
- [ ] Empty state: redirect to onboarding if no sessions ever

---

## Session detail (`/dashboard/agents/[id]/sessions/[sid]`)

- [ ] Ordered action timeline — color-coded by type
- [ ] Per action: type badge · timestamp · duration · affected resources · model · token usage · status
- [ ] Expandable action detail (input/output if captureContent was enabled)
- [ ] Human identity shown if present

---

## Agents page (`/dashboard/agents`)

- [ ] List of all discovered agents — name · framework · first seen · last seen · total sessions · compliance status badge
- [ ] Auto-discovery banner: *"[N] agent(s) detected — [Classify →]"* (shown while any agent is `unclassified`)
- [ ] Compliance status badge colors: Not assessed (grey) · Needs attention (amber) · At risk (red) · Compliant (green)
- [ ] Click agent → agent detail

---

## Agent detail page (`/dashboard/agents/[id]`)

Single scrollable page, sections in order:

**SDK section**
- [ ] Agent name (editable inline) · framework · first seen / last seen · total sessions / actions
- [ ] Recent sessions (last 5, link to full list)

**Classification section**
- [ ] If unclassified: [Classify →] CTA
- [ ] If classified: risk level badge · confidence score · classification date · regulatory version · [Reclassify →]
- [ ] Low confidence warning (<70%): *"Consider requesting expert review"*
- [ ] Reclassify flow: confirm modal (warns co-signature will be reset) → classification screen

**Classification screen (inline, 5 questions)**
- [ ] Pre-filled from SDK behavioral data: use case, affected population, human override, data sensitivity, deployment context
- [ ] All fields editable
- [ ] Submit → LLM call (Anthropic) → show result: risk level + confidence + explanation
- [ ] Minimal risk: "No specific obligations under the EU AI Act" message
- [ ] Limited/High/Unacceptable: obligations list generated

**Obligations section** (Limited, High, Unacceptable only)
- [ ] Top 3 obligations shown by default · "Show all [N]" toggle
- [ ] Per item: done checkbox · title · implementation guide · regulatory reference
- [ ] Checking all → `compliance_status → compliant` · audit log entry

**Document section**
- [ ] If no document: [Generate document →] CTA
- [ ] Generate: LLM call → PDF render (@react-pdf/renderer) → upload to Supabase Storage → show download link
- [ ] Loading state: "Generating compliance document…" (can take 10–30s)
- [ ] Disclaimer block mandatory, non-removable
- [ ] Version history: "Show previous versions" toggle
- [ ] [Request co-signature →] (Pro only — hidden on Free)

**Co-signature section** (Pro only)
- [ ] If no prescriber linked: message + link to find a partner (partner directory placeholder for v1)
- [ ] If prescriber linked: [Request co-signature →] → confirm modal showing prescriber name + fee
- [ ] Pending: show "Awaiting review by [Prescriber name]"
- [ ] Co-signed: show prescriber name · firm · date · [Download co-signed PDF →]
- [ ] Changes requested: show prescriber notes · [Update and resubmit →]

---

## Sensitive file alerts page (`/dashboard/alerts`)

- [ ] List of all open alerts — agent · matched pattern · affected resource · created at
- [ ] Actions: Acknowledge · Dismiss
- [ ] Badge in nav when open alerts exist
- [ ] Empty state: "No sensitive file alerts"

---

## Settings pages

**General** (`/dashboard/settings`)
- [ ] Organization name · sector — editable

**Team** (`/dashboard/settings/team`)
- [ ] List members (email, joined date)
- [ ] [Invite member →] → email input → send invite email via Resend
- [ ] Remove member

**Billing** (`/dashboard/settings/billing`)
- [ ] Current plan badge · renewal date
- [ ] [Upgrade to Pro →] → Stripe Checkout (server action)
- [ ] [Manage subscription →] → Stripe Customer Portal
- [ ] Show `past_due` warning if applicable + days remaining in grace period

**API tokens** (`/dashboard/settings/tokens`)
- [ ] List tokens (name, created at, last used at)
- [ ] [Create token →] → name input → show raw token once with copy button
- [ ] [Revoke] per token

**Privacy** (`/dashboard/settings/privacy`)
- [ ] [Export my data →] — triggers `exportUserData()` server action → JSON download (GDPR Art. 15)
- [ ] [Delete account →] — confirm modal → `anonymizeUser()` → sign out (GDPR Art. 17)
- [ ] Email preferences toggle — opt out of behavioral/product emails (`marketing_opted_out`)

---

## Plan enforcement

- [ ] Free: max 3 agents — 4th agent auto-discovered but blocked from classification; banner shown
- [ ] Free: co-signature button not shown (not a paywall modal — just hidden)
- [ ] Free: 30-day session retention (purge job from Sprint 1)
- [ ] `past_due`: show warning, full access for 7-day grace period; downgrade after

---

## Upgrade moments

- [ ] 4th agent detected banner: "Upgrade to track unlimited agents [Upgrade to Pro →]"
- [ ] 27-day retention warning banner on Activity page for Free orgs

---

## Feedback board (`/dashboard/feedback`)

- [ ] List all feedback items — type badge · title · vote count · status badge · upvote button
- [ ] Sorted by votes descending; filter by type and status
- [ ] Upvote toggle (one vote per user, optimistic update)
- [ ] [Submit feedback →] → modal: type (Bug/Enhancement) · title · description
- [ ] Item detail (`/dashboard/feedback/[id]`): full description · admin response (highlighted) · GitHub issue link

---

## Email sequences (all triggered by server-side events)

- [ ] Email 1: API key delivery — sent immediately on signup (Resend)
- [ ] Email 2: 24h nudge — sent if no session after 24h (check via scheduled query or cron)
- [ ] Email 3: first session — triggered when first `sessions` row created for org
- [ ] Email 4: 48h classify nudge — sent if session exists but no classification after 48h
- [ ] Weekly digest — every Monday 08:00, only if activity in last 7 days; suppress if `marketing_opted_out`
- [ ] Upgrade nudge: 4th agent — triggered by ingest auto-discovery
- [ ] Upgrade nudge: 27-day retention — triggered by nightly job
- [ ] Upgrade nudge: 14-day no upgrade — triggered by nightly job
- [ ] Re-engagement: 14 days inactive — triggered by nightly job
- [ ] All behavioral emails: check `marketing_opted_out` before sending; include unsubscribe link

---

## Stripe (end-user flows)

- [ ] Pro checkout: `stripe.checkout.sessions.create()` server action → redirect to Stripe-hosted page
- [ ] Customer portal: `stripe.billingPortal.sessions.create()` server action → redirect
- [ ] Post-checkout success: show "You're on Pro" confirmation banner
- [ ] Free org first co-signature: SetupIntent to save payment method before request submitted

---

## Verification

- [ ] Signup → onboarding → SDK snippet copied → session appears in Activity → banner fires → classify → risk level shown → obligations checklist → generate document → PDF downloads with disclaimer → request co-signature (upgrade prompt on Free)
- [ ] Free plan: 4th agent blocked with banner; co-signature button hidden
- [ ] Pro upgrade via Stripe Checkout → plan updates in DB → co-signature button appears
- [ ] Unsubscribe link in behavioral email → `marketing_opted_out = true` → no further behavioral emails
- [ ] GDPR data export → JSON file downloaded with all org data
