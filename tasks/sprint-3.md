# Sprint 3 — Next.js: Auth + Ingest API

**Goal:** Project scaffold, Supabase auth, all API routes. No dashboard UI. Done when the SDK running against production sends sessions, auth signup/login/invite works, and Stripe webhook receives events.

Spec refs: `specs/auth.md` · `specs/infra.md` · `specs/api.md` · `specs/stripe.md` · `specs/errors.md`

---

## Project setup

- [ ] `npx create-next-app@14` — App Router, TypeScript, Tailwind CSS
- [ ] Install Shadcn/ui — init with default theme
- [ ] Install dependencies: `@supabase/ssr`, `@supabase/supabase-js`, `@anthropic-ai/sdk`, `stripe`, `resend`, `@react-pdf/renderer`, `zod`
- [ ] `.env.example` — all variables from `specs/infra.md`
- [ ] `.env.local` — filled for local development
- [ ] `types/database.ts` — copy generated Supabase types from Sprint 1

---

## Supabase clients

- [ ] `lib/supabase/client.ts` — browser client (`createBrowserClient`)
- [ ] `lib/supabase/server.ts` — server client (`createServerClient`, cookie-based)
- [ ] `lib/supabase/admin.ts` — service role client (ingest, webhooks, admin routes)

---

## Middleware

- [ ] `middleware.ts` — validate Supabase session on every request
- [ ] Redirect unauthenticated → `/login` (except public routes: `/login`, `/signup`, `/reset-password`, `/join/*`)
- [ ] Redirect `role=partner` away from `/dashboard/*` → `/partner`
- [ ] Redirect `role=platform_admin` to `/admin` when accessing `/`
- [ ] Refresh session cookie on every response

---

## Auth pages (UI — minimal, functional)

- [ ] `/signup` — first name, last name, email, password, organization name, sector selector → Supabase `auth.signUp()` → server action creates org + user + default project + first SDK token → redirect `/onboarding`
- [ ] `/login` — email + password → redirect to appropriate portal by role
- [ ] `/reset-password` — request reset email → Supabase sends link → update password form
- [ ] `/join/invite` — team member invite landing (pre-filled email, password only) → joins existing org
- [ ] Email confirmation gate — show "check your email" screen; resend link

---

## Auth server actions

- [ ] `signUp(formData)` — create org, user, default project, first SDK token; return token to show once
- [ ] `signIn(email, password)`
- [ ] `signOut()`
- [ ] `resetPassword(email)`
- [ ] `updatePassword(newPassword)`
- [ ] `inviteTeamMember(email)` — create `partner_clients`-style invite, send email via Resend
- [ ] `acceptInvite(token)` — validate token, create user in existing org

---

## Ingest API — `POST /api/v1/ingest`

- [ ] `export const runtime = "nodejs"` and `export const maxDuration = 30`
- [ ] Extract `Authorization: Bearer txk_live_...` header
- [ ] SHA-256 hash token; look up `sdk_tokens` (service role, bypass RLS)
- [ ] 401 if token not found or `revoked_at IS NOT NULL`
- [ ] Call `check_rate_limit(token_id, 100)` RPC → 429 with `Retry-After: 60` if false
- [ ] Validate request body: max 100 actions, max 1MB
- [ ] Upsert session record
- [ ] Bulk insert actions
- [ ] Single UPDATE on agent: `last_seen_at`, `total_sessions`, `total_actions`
- [ ] Auto-create agent record if slug not seen before (`first_seen_at = now()`)
- [ ] Sensitive file pattern matching on `affected_resources` — patterns: `**/.env`, `**/secrets/**`, `**/*.pem`, `**/credentials*`, `**/.ssh/**`
- [ ] If matches: insert `sensitive_file_alerts` (severity: `high`)
- [ ] Return `200 { received: N }`
- [ ] All errors logged; never return 500 silently to SDK

---

## SDK Token management (server actions, no UI yet)

- [ ] `createToken(projectId, name)` — generate `txk_live_` + 32 random bytes, SHA-256 hash, store hash, return raw token once
- [ ] `listTokens(projectId)` — return tokens (never raw token)
- [ ] `revokeToken(tokenId)` — set `revoked_at = now()`; delete from any cache

---

## Stripe webhook — `POST /api/webhooks/stripe`

- [ ] Verify Stripe signature (`stripe.webhooks.constructEvent`)
- [ ] Handle `checkout.session.completed` → set org `plan`, `stripe_customer_id`, `stripe_subscription_id`
- [ ] Handle `customer.subscription.updated` → update `plan`, `subscription_status`
- [ ] Handle `customer.subscription.deleted` → downgrade to `free`, set `subscription_status = canceled`
- [ ] Handle `invoice.payment_failed` → set `subscription_status = past_due`, set `grace_period_ends_at = now() + 7 days`
- [ ] Handle `invoice.payment_succeeded` → reset `subscription_status = active`, clear `grace_period_ends_at`
- [ ] Always return 200 to Stripe (log errors internally, never fail the webhook)

---

## Resend webhook — `POST /api/webhooks/resend`

- [ ] Verify Resend signature
- [ ] Handle `email.bounced` → log; flag user email as bounced if needed
- [ ] Return 200

---

## Platform Guardian cron stub — `GET /api/cron/platform-guardian`

- [ ] Validate `Authorization: Bearer [CRON_SECRET]` header → 401 if missing/wrong
- [ ] Stub that logs "Platform Guardian: OK" and inserts a row in `agent_logs`
- [ ] Full implementation in Sprint 6

---

## Vercel configuration

- [ ] `vercel.json` — cron schedule for platform-guardian (`*/5 * * * *`)
- [ ] Confirm all env vars are set in Vercel dashboard before first deploy

---

## Verification

- [ ] Sign up → email confirmation → first login → org + user + project + token created in DB
- [ ] SDK (from Sprint 2) pointing at production URL → sends session → appears in `sessions` + `actions` tables
- [ ] Invalid SDK token → 401
- [ ] 101st request in one minute → 429 with `Retry-After: 60`
- [ ] Sensitive file access in SDK payload → `sensitive_file_alerts` row created
- [ ] Stripe test checkout → `invoice.payment_succeeded` webhook → org plan updated
- [ ] `/api/cron/platform-guardian` with wrong secret → 401
