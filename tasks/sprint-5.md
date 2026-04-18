# Sprint 5 — Next.js: Partner Portal

**Goal:** The full prescriber experience. Done when a partner can apply, get approved, invite a client, review a co-signature request, approve it, and receive a Stripe payout.

Spec refs: `specs/prescriber-partnership.md` · `specs/content/guide-prescriber.md` · `specs/stripe.md` · `specs/emails.md` · `specs/auth.md`

---

## Partner application flow (public-facing)

- [ ] Application form at `/join/partner` — org name, type, contact email, SME client count estimate, EU market presence
- [ ] Tier selection: Per-act co-signature · Integrator license
- [ ] Co-signature Scope Document — inline read + acceptance checkbox (sets `scope_accepted_at` on approval)
- [ ] Submit → insert `partners` row with `status = pending` → send "Application received" email to applicant
- [ ] Confirmation screen: "We'll review within 48 hours"

---

## Partner auth

- [ ] Partners sign in at `/login` — same Supabase Auth, JWT `role = partner`
- [ ] Middleware routes `role = partner` → `/partner/*`
- [ ] Partner account created by platform admin on approval (email invite flow)

---

## Partner portal layout

- [ ] Sidebar: **Queue** (badge: pending count) · **Clients** · **Settings**
- [ ] User menu (partner org name, sign out)

---

## Partner home — Signature queue (`/partner`)

- [ ] List of pending co-signature requests — client org name · agent name · risk level · submitted at
- [ ] Badge on Queue nav item showing count
- [ ] Click → co-signature review page
- [ ] Empty state: "No pending requests"

---

## Co-signature review page (`/partner/queue/[docId]`)

Two-panel layout (desktop); stacked on mobile:

**Left panel — document**
- [ ] Full compliance document preview (rendered from stored content)
- [ ] Risk level badge · confidence score · regulatory version
- [ ] Obligations list
- [ ] SDK evidence section (if evidence_session_id linked): session count · action types · environments · affected resources

**Right panel — review**
- [ ] System name, description, use case, data used, autonomy level
- [ ] Professional notes field (optional, recommended for High risk)
- [ ] Three action buttons:
  - **[Approve & Sign]** → confirm modal → insert `document_signatures` row (action: `approved`) → trigger payment → send completion emails
  - **[Request Changes]** → notes required → insert row (action: `changes_requested`) → notify client org
  - **[Decline]** → reason required → insert row (action: `declined`) → notify client org

---

## Co-signature payment (on approval)

- [ ] Charge client org's saved payment method (Stripe PaymentIntent)
- [ ] Transfer to partner's Stripe Connect account minus €25 Trustixy commission
- [ ] Insert `cosignature_transactions` row (status: `completed`)
- [ ] On payment failure: do not record signature; notify org; re-queue request for 48h
- [ ] Free plan org requesting co-signature: trigger SetupIntent first to save payment method

---

## Clients page (`/partner/clients`)

- [ ] List all linked clients — org name · status (invited/active) · agent count · compliance health
- [ ] [Invite client →] → enter client email → create `partner_clients` row → send invite email with `ref` link
- [ ] Click client → client detail

---

## Client detail (`/partner/clients/[id]`)

- [ ] List of client's agents — name · risk level · document status · last classified
- [ ] Compliance health summary (count by status)
- [ ] [Export compliance report →] → generate org compliance report PDF (all agents, statuses, document trail)

---

## Partner settings

**General** (`/partner/settings`)
- [ ] Organization name · contact email — editable

**Team** (`/partner/settings/team`)
- [ ] List team members; invite additional co-signers
- [ ] Each team member can co-sign — their name appears on documents they approve

**Billing** (`/partner/settings/billing`)
- [ ] Per-act model: [Connect Stripe account →] → Stripe Connect Express onboarding link
- [ ] After onboarding: show connected status (`charges_enabled = true`)
- [ ] Set co-signature fee (€ input) — displayed to clients at request time
- [ ] Transaction history: date · client · agent · amount · Trustixy commission · net payout
- [ ] Integrator model: subscription status (managed by platform admin)

---

## Email sequences (partner-specific)

- [ ] Application received — sent on submission
- [ ] Application approved — sent by platform admin action; includes portal link + onboarding steps
- [ ] New co-signature request — sent to prescriber when client submits; includes direct review link (24-hour signed URL)
- [ ] Co-signature completed — sent to client org when prescriber approves; includes PDF download link
- [ ] Changes requested — sent to client org with prescriber notes
- [ ] Co-signature declined — sent to client org with reason

---

## Prescriber-referred client signup

- [ ] Invite link format: `trustixy.com/join?ref=[partner-slug]&token=[raw-token]`
- [ ] `/join` page: validate token hash + expiry → pre-fill signup with partner linked
- [ ] On account creation: set `partner_clients.client_organization_id`, `accepted_at`
- [ ] Client's prescriber pre-linked — co-signature step in dashboard defaults to this prescriber

---

## Verification

- [ ] Submit partner application → "received" email arrives
- [ ] Platform admin approves (Sprint 6) → partner receives "approved" email → logs into partner portal
- [ ] Partner invites a client → client receives invite email → creates account → linked to partner in DB
- [ ] Client submits co-signature request → partner receives email with review link → opens two-panel review
- [ ] Partner approves → payment charged to client → net amount transferred to partner Stripe account → client receives "co-signed" email with PDF link
- [ ] Partner requests changes → client receives prescriber notes → updates and resubmits
- [ ] Partner transaction history shows completed payout
