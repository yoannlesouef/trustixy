# Launch Checklist — Platform Admin

Complete in order. Do not open to users until all **[BLOCKING]** items are done.

---

## 1. Legal & GDPR [BLOCKING]

- [ ] **Sign Anthropic DPA** — confirm Standard Contractual Clauses (SCCs) cover EU→US transfer of user data sent to the Claude API. Required before any user data is processed. Contact: privacy@anthropic.com
- [ ] **Sign Vercel DPA** — confirm SCCs for EU→US data transfer. Available in Vercel dashboard → Settings → Legal
- [ ] **Sign Resend DPA** — confirm SCCs for email delivery. Available at resend.com/legal
- [ ] **Stripe DPA** — pre-signed via Stripe standard agreements; verify it is active on your account
- [ ] **Update CGU Art. 9.6** — add all sub-processors: Stripe, Resend, Slack, GitHub (currently only lists Supabase, Vercel, Anthropic)
- [ ] **Update CGU Art. 9.3** — add explicit lawful basis per processing activity (see `specs/privacy.md` section 2)
- [ ] **Update CGU Art. 9.5** — add right to restriction and right to object (currently missing)
- [ ] **Update CGU Art. 7** — add obligation for orgs using `human_identity` SDK field to notify their own users
- [ ] **Update CGU Art. 4** — add Trustixy's own EU AI Act status and transparency disclosure
- [ ] **Publish privacy policy page** — a user-friendly privacy page at trustixy.com/privacy (separate from CGU). See `specs/privacy.md` for content
- [ ] **Publish cookie policy** — cookie banner on first visit; only strictly necessary cookies set by default (see `specs/privacy.md` section 8)
- [ ] **Have a lawyer review the CGU** — the CGU is a working draft and must be validated before publication
- [ ] **Register with CNIL** — if required for your processing activities (check with your legal counsel)
- [ ] **Set up DPO inbox** — dpo@trustixy.com must be actively monitored; GDPR rights requests must be responded to within 30 days

---

## 2. Infrastructure [BLOCKING]

- [ ] **Vercel plan: Pro** — upgrade from Hobby; 60s function timeout required for LLM classification and PDF document generation (Hobby limit is 10s, which will timeout on most classification calls)
- [ ] **Supabase project region: EU only** — create the Supabase project in `eu-west-1` (Ireland) or `eu-central-1` (Frankfurt). Never US. This is a GDPR hard requirement
- [ ] **Supabase Auth email templates** — customize signup confirmation, password reset, and team invite emails with Trustixy branding
- [ ] **Supabase email confirmation** — ensure "Confirm email" is enabled in Auth settings (required before first login)
- [ ] **Run all database migrations** — `npx supabase db push` in production; verify all tables and RLS policies are applied
- [ ] **Generate TypeScript types** — `npx supabase gen types typescript --project-id [id] > types/database.ts`; commit to repo
- [ ] **Verify RLS policies** — run the test suite in `specs/rls.md`; confirm cross-org access is denied
- [ ] **Supabase Storage bucket** — create `documents` bucket (private); confirm signed URL config (1h downloads, 24h co-signature links)
- [ ] **Set all environment variables in Vercel dashboard** — every variable in `.env.example` must be set in production (never committed to repo):
  - `NEXT_PUBLIC_SUPABASE_URL`
  - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
  - `SUPABASE_SERVICE_ROLE_KEY`
  - `ANTHROPIC_API_KEY`
  - `STRIPE_SECRET_KEY`
  - `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY`
  - `STRIPE_WEBHOOK_SECRET`
  - `STRIPE_PRO_PRICE_ID`
  - `STRIPE_INTEGRATOR_PRICE_ID`
  - `RESEND_API_KEY`
  - `RESEND_FROM_EMAIL`
  - `RESEND_REPLY_TO`
  - `SLACK_WEBHOOK_URL`
  - `NEXT_PUBLIC_APP_URL`
  - `CRON_SECRET`
  - `ADMIN_EMAIL`
  - `GITHUB_TOKEN`
  - `GITHUB_REPO_OWNER`
  - `GITHUB_REPO_NAME`
- [ ] **Vercel Cron** — confirm `vercel.json` cron route is deployed and `CRON_SECRET` header is configured in Vercel dashboard
- [ ] **Custom domain** — trustixy.com pointed to Vercel; SSL certificate active; www → apex redirect configured
- [ ] **Deploy to production** — push to `main`; confirm Vercel build passes with zero errors

---

## 3. Stripe [BLOCKING]

- [ ] **Switch from test mode to live mode** — all configuration below is for live mode
- [ ] **Create Pro product and price** — €99/month recurring; store price ID in `STRIPE_PRO_PRICE_ID`
- [ ] **Create Integrator product and price** — €399/month recurring; store price ID in `STRIPE_INTEGRATOR_PRICE_ID`
- [ ] **Register Stripe webhook** — endpoint: `https://trustixy.com/api/webhooks/stripe`; events to register:
  - `checkout.session.completed`
  - `customer.subscription.updated`
  - `customer.subscription.deleted`
  - `invoice.payment_failed`
  - `invoice.payment_succeeded`
- [ ] **Copy webhook signing secret** — store in `STRIPE_WEBHOOK_SECRET`
- [ ] **Enable Stripe Connect** — activate in Stripe dashboard for partner payouts (Express accounts)
- [ ] **Configure Stripe Customer Portal** — enable self-serve plan management (upgrade, downgrade, cancel, update payment method) at dashboard.stripe.com/settings/billing/portal
- [ ] **Test full payment flow** — use test mode cards one final time before go-live; verify webhook events arrive and org plan updates correctly
- [ ] **Bookmark key Stripe links** (see `specs/content/guide-admin.md` section 4)

---

## 4. Email [BLOCKING]

- [ ] **Verify sending domain** — add Resend DNS records for trustixy.com; confirm domain is verified in Resend dashboard
- [ ] **Verify noreply@trustixy.com** — sending address must be verified
- [ ] **Test all email sequences** — trigger each email manually and confirm delivery, formatting, and links:
  - Activation email 1 (API key)
  - Activation email 2 (24h nudge)
  - Activation email 3 (first session)
  - Activation email 4 (48h classify)
  - Weekly digest
  - Upgrade nudge (agent limit)
  - Upgrade nudge (30-day retention warning)
  - Partner application received
  - Partner application approved
  - Co-signature request (to prescriber)
  - Co-signature completed (to end user)
  - Re-engagement
  - Payment failure (day 1 and day 5)
- [ ] **Confirm unsubscribe links work** — every behavioral email must have a working unsubscribe link that sets `users.marketing_opted_out = true`

---

## 5. Admin Account [BLOCKING]

- [ ] **Set `ADMIN_EMAIL`** — set to yoann@trustixy.com in Vercel environment variables
- [ ] **Create the admin account** — sign up at trustixy.com/signup using `ADMIN_EMAIL`; the auth hook will assign `role = platform_admin`
- [ ] **Verify `/admin` access** — confirm the admin dashboard loads and all sections are accessible (organizations, partners, agents, feedback)
- [ ] **Set community social link** — if Discord/Telegram is ready, set the URL in `/admin` → Settings → Community; leave blank otherwise (link will be hidden from users)

---

## 6. SDK & Developer Docs

- [ ] **Publish `@trustixy/sdk` to npm** — run `npm publish` from the SDK package; confirm it appears at npmjs.com/package/@trustixy/sdk
- [ ] **Test SDK install from npm** — `npm install @trustixy/sdk` in a fresh project; run the quickstart; confirm a session appears in the dashboard within seconds
- [ ] **Publish developer docs** — content from `specs/content/developer-docs.md` must be live at trustixy.com/docs before launch
- [ ] **Verify SDK quickstart code in docs matches published npm package** — version numbers and import paths must match

---

## 7. Platform Guardian — First Run

- [ ] **Trigger Platform Guardian manually** — call `POST /api/cron/platform-guardian` with the correct `CRON_SECRET` header; confirm it runs without error
- [ ] **Verify Slack alert delivery** — confirm the Slack webhook receives the health summary
- [ ] **Verify smoke test passes** — the nightly smoke test runs at 02:00 UTC; confirm the first run completes all steps: SDK ingest → classification → document generation → PDF export
- [ ] **Check `/admin/agents`** — Platform Guardian and Sensitive File Detector logs should appear after first run

---

## 8. Security

- [ ] **Rotate all secrets after first deployment** — `CRON_SECRET`, `SUPABASE_SERVICE_ROLE_KEY`, initial SDK token
- [ ] **Confirm `SUPABASE_SERVICE_ROLE_KEY` is server-only** — must never appear in client-side code or `NEXT_PUBLIC_` variables
- [ ] **Test rate limiting on ingest API** — confirm 429 is returned after 100 requests/minute per SDK token
- [ ] **Test RLS cross-org access** — attempt to access another org's data with a valid JWT; must return empty result or 403
- [ ] **Test `/admin` route protection** — attempt to access `/admin` with a non-admin JWT; must redirect to login
- [ ] **Verify PDF signed URLs expire** — download a PDF, wait >1 hour, confirm the URL is no longer valid
- [ ] **GitHub token scope** — confirm `GITHUB_TOKEN` has only `issues:write` scope, nothing broader

---

## 9. Content & Legal Pages

- [ ] **Publish CGU** — at trustixy.com/cgu (after legal review and all updates from section 1)
- [ ] **Publish Privacy Policy** — at trustixy.com/privacy (user-friendly version; see `specs/privacy.md`)
- [ ] **Publish Cookie Policy** — at trustixy.com/cookies or inline on the cookie banner
- [ ] **Footer links** — all pages must link to CGU, Privacy Policy, and contact email
- [ ] **Home page live** — content from `specs/content/home.md` is published and reviewed

---

## 10. Go-live Verification

Run this sequence manually before announcing launch:

- [ ] Sign up as a new user → confirm email → complete onboarding
- [ ] Install SDK in a test project → send first ingest → confirm session appears in dashboard
- [ ] Create an agent description → run classification → check risk level, confidence score, obligations
- [ ] Generate compliance document → download PDF → verify disclaimer is present and non-removable
- [ ] Trigger a sensitive file alert (access `.env` in a test session) → confirm email arrives to org admin
- [ ] Apply as a prescriber partner → approve from admin → connect Stripe Connect → invite a test client
- [ ] Submit a co-signature request → review as prescriber → approve → download co-signed PDF
- [ ] Upgrade test org to Pro via Stripe Checkout → confirm plan changes in dashboard
- [ ] Submit feedback from dashboard → upvote → respond as admin → push to GitHub issue → confirm issue created
- [ ] Request data export (GDPR Art. 15) → confirm export is complete and downloadable
- [ ] Check `/admin` dashboard → all sections populated → Platform Guardian logs present

---

## 11. Post-Launch (first 48 hours)

- [ ] Monitor Slack for Platform Guardian alerts
- [ ] Check Stripe for first signups and any failed payments
- [ ] Watch npm download count at npmjs.com/package/@trustixy/sdk
- [ ] Check Supabase dashboard: DB size, query latency, auth signups
- [ ] Review any feedback board submissions
- [ ] Confirm weekly digest sends correctly on Monday 08:00
