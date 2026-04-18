# Infrastructure

## Architecture verdict

The core stack (Next.js + Supabase + Vercel + Anthropic + Stripe + Resend) is the right choice. No layer needs replacing. However, **three critical technical gaps** exist that will cause production failures at any real traffic level, and two workloads are wrongly placed on Edge Runtime.

---

## Stack

| Layer | Choice | Reason |
|---|---|---|
| Frontend + API routes | Next.js 14 (App Router) | SSR, server actions, API routes in one project |
| Database + Auth | Supabase | Postgres + RLS + Auth + Storage in one service |
| Styling | Tailwind CSS | Utility-first, fast iteration |
| AI | Anthropic Claude API | Classification + document generation |
| Email | Resend | Native Next.js/Vercel integration, simple SDK |
| Payments | Stripe | Subscriptions + Connect for partner payouts |
| Deployment | Vercel Pro | Required — 60s function timeout for LLM + PDF generation |
| Rate limiting | Postgres counter on `sdk_tokens` | Atomic per-token rate limit; no external service needed |
| Scheduled jobs | Vercel Cron | Platform Guardian every 5 min |

**Vercel Pro is mandatory**, not optional. The free/hobby timeout (10s) is too short for LLM classification (~10–30s) and document generation + PDF render (~15–35s). Specify this in the budget.

---

## Critical Gap 1 — Rate limiting had no shared state (fixed without new service)

**Problem:** In-process rate limiters don't work across Vercel's multiple serverless instances.

**Fix:** Postgres atomic counter on `sdk_tokens` — no Upstash Redis, no extra service. See `specs/auth.md` → SDK Token Rate Limiting for the RPC function. One extra DB roundtrip per ingest request, negligible at v1 scale.

---

## Critical Gap 2 — bcrypt wrong for SDK tokens (fixed, simpler)

**Problem:** bcrypt takes 100–300ms per comparison and doesn't run on Edge Runtime.

**Fix:** SHA-256 for SDK token hashing — <1ms, works everywhere, industry standard for API tokens. See `specs/auth.md` → SDK Token Auth. No caching needed, no Upstash Redis.

Move ingest to Node.js runtime:
```typescript
export const runtime = "nodejs";
export const maxDuration = 30;
```

---

## Critical Gap 3 — `actions` table will degrade without indexes

**Problem:** The `actions` table is the highest-volume table. Every SDK ingest adds rows. Without the right indexes, queries on the dashboard (agent activity, session detail, action search) will do full table scans as soon as the table has >100K rows.

**Required indexes** (add to the first migration):

```sql
-- Most common query: all actions for an agent, newest first
CREATE INDEX idx_actions_agent_created ON actions (agent_id, created_at DESC);

-- Session detail view
CREATE INDEX idx_actions_session_sequence ON actions (session_id, sequence ASC);

-- Project-level queries (activity feed)
CREATE INDEX idx_actions_project_created ON actions (project_id, created_at DESC);

-- Sensitive file detection: filter by affected_resources (GIN for array)
CREATE INDEX idx_actions_resources ON actions USING GIN (affected_resources);

-- Free plan nightly purge: find sessions to delete by org + age
CREATE INDEX idx_sessions_org_created ON sessions (organization_id, created_at);

-- Agent last_seen_at lookups
CREATE INDEX idx_agents_org_status ON agents (organization_id, compliance_status)
  WHERE archived_at IS NULL;
```

Add to `supabase/migrations/` — not optional.

---

## Runtime split — which routes run where

| Route | Runtime | Reason |
|---|---|---|
| `POST /api/v1/ingest` | **Node.js** | bcrypt (native module); 30s max duration |
| `POST /api/v1/classify` | **Node.js** | LLM call up to 30s; 60s max duration |
| `POST /api/v1/documents` | **Node.js** | LLM + PDF render; 60s max duration |
| `GET /api/v1/*` (reads) | **Node.js** | DB reads; no benefit to Edge here |
| `POST /api/webhooks/stripe` | **Node.js** | Webhook signature validation |
| `POST /api/webhooks/resend` | **Node.js** | Standard |
| `GET /api/cron/platform-guardian` | **Node.js** | Supabase queries + Anthropic calls |

No routes use Edge Runtime. The latency benefit of Edge (<50ms cold start) does not outweigh the incompatibility with native Node.js modules (bcrypt, @react-pdf/renderer).

---

## PDF Generation — timeout management

PDF generation = LLM call (10–30s) + @react-pdf/renderer render (~1s). Total: 11–31s.

With Vercel Pro (60s max duration), this fits synchronously. Set explicitly:

```typescript
// app/api route handler for document generation
export const maxDuration = 60;
```

Show a loading state in the UI (the server action will stream progress or poll). Do not attempt to move PDF generation to Edge Runtime.

`@react-pdf/renderer` is the correct library — no Puppeteer in serverless. Keep it.

---

## Document generation — loading UX

Because generation takes 10–30s, the UI must handle this gracefully:

1. User clicks [Generate document] → server action fires → UI shows spinner + "Generating compliance document…"
2. Server action completes (synchronous, up to 60s) → returns signed URL
3. UI shows download button + document preview

Do not use a background job queue for v1 — synchronous with proper loading state is simpler and sufficient. If generation regularly exceeds 60s at scale (unlikely for v1), move to Upstash QStash in v2.

---

## Project Structure

```
trustixy/
├── app/
│   ├── (dashboard)/              # Authenticated org users
│   │   ├── layout.tsx
│   │   ├── page.tsx              # Activity (home)
│   │   ├── agents/
│   │   │   ├── page.tsx
│   │   │   └── [id]/page.tsx
│   │   ├── alerts/page.tsx
│   │   ├── feedback/
│   │   │   ├── page.tsx
│   │   │   └── [id]/page.tsx
│   │   └── settings/
│   │       ├── page.tsx
│   │       ├── team/page.tsx
│   │       ├── billing/page.tsx
│   │       ├── tokens/page.tsx
│   │       └── privacy/page.tsx  # GDPR: data export + delete account
│   │
│   ├── (partner)/                # Prescriber portal
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   ├── clients/
│   │   │   ├── page.tsx
│   │   │   └── [id]/page.tsx
│   │   ├── queue/[docId]/page.tsx
│   │   └── settings/
│   │       ├── page.tsx
│   │       ├── billing/page.tsx
│   │       └── team/page.tsx
│   │
│   ├── admin/
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   ├── organizations/page.tsx
│   │   ├── partners/page.tsx
│   │   ├── agents/page.tsx
│   │   └── feedback/page.tsx
│   │
│   ├── (auth)/
│   │   ├── login/page.tsx
│   │   ├── signup/page.tsx
│   │   ├── reset-password/page.tsx
│   │   └── join/invite/page.tsx
│   │
│   ├── api/
│   │   ├── v1/
│   │   │   └── ingest/route.ts   # Node.js runtime, maxDuration: 30
│   │   ├── webhooks/
│   │   │   ├── stripe/route.ts
│   │   │   └── resend/route.ts
│   │   └── cron/
│   │       └── platform-guardian/route.ts
│   │
│   ├── layout.tsx
│   └── globals.css
│
├── components/
│   ├── ui/                       # Shadcn/ui base components
│   ├── dashboard/
│   ├── partner/
│   └── shared/
│
├── lib/
│   ├── supabase/
│   │   ├── client.ts
│   │   ├── server.ts
│   │   └── admin.ts              # Service role client
│   ├── anthropic.ts
│   ├── stripe.ts
│   ├── resend.ts
│   ├── pdf.ts
│   └── sensitive-patterns.ts
│
├── actions/
│   ├── agents.ts
│   ├── classifications.ts
│   ├── documents.ts
│   ├── signatures.ts
│   ├── partners.ts
│   ├── billing.ts
│   └── feedback.ts
│
├── types/
│   └── database.ts               # Generated Supabase types
│
├── middleware.ts
├── .env.local
├── .env.example
└── vercel.json
```

---

## Environment Variables

```bash
# Supabase
NEXT_PUBLIC_SUPABASE_URL=https://[project].supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJ...
SUPABASE_SERVICE_ROLE_KEY=eyJ...         # Server-only — never expose client-side

# Anthropic
ANTHROPIC_API_KEY=sk-ant-...

# Stripe
STRIPE_SECRET_KEY=sk_live_...
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...
STRIPE_PRO_PRICE_ID=price_...
STRIPE_INTEGRATOR_PRICE_ID=price_...

# Email (Resend)
RESEND_API_KEY=re_...
RESEND_FROM_EMAIL=noreply@trustixy.com
RESEND_REPLY_TO=support@trustixy.com

# Internal alerts (Platform Guardian)
SLACK_WEBHOOK_URL=https://hooks.slack.com/...

# App
NEXT_PUBLIC_APP_URL=https://trustixy.com
CRON_SECRET=...

# Admin
ADMIN_EMAIL=yoann@trustixy.com

# GitHub (feedback → issues integration)
GITHUB_TOKEN=ghp_...
GITHUB_REPO_OWNER=trustixy
GITHUB_REPO_NAME=platform
```

---

## Ingest API Architecture

### Route: `POST /api/v1/ingest`

```typescript
export const runtime = "nodejs"; // bcrypt requires Node.js
export const maxDuration = 30;

// Flow:
// 1. Extract Bearer token from Authorization header
// 2. SHA-256 hash the token; look up in sdk_tokens (<1ms)
// 3. If not found or revoked → 401
// 4. Atomic rate limit check via Postgres RPC (check_rate_limit) → 429 if exceeded
// 5. Upsert session record
// 6. Bulk insert actions
// 7. Update agent.last_seen_at + total counters (single UPDATE)
// 8. Run sensitive file pattern matching synchronously (fast, no LLM)
// 9. If matches found: insert sensitive_file_alerts + queue email notification
// 10. Return 200 { received: N }
```

### Limits

| Parameter | Limit |
|---|---|
| Max actions per batch | 100 |
| Max request body size | 1 MB |
| Rate limit per SDK token | 100 requests/minute (enforced via Upstash Redis) |
| Rate limit response | 429 with `Retry-After` header |
| Function max duration | 30s |

### SDK retry behavior

- On 429: exponential backoff, start at 1s, max 30s, max 5 retries
- On 5xx: exponential backoff, start at 500ms, max 10s, max 3 retries
- On 4xx (except 429): no retry
- Failed batches queued in-memory and retried on next flush interval

---

## Vercel Cron Schedule

`vercel.json`:

```json
{
  "crons": [
    {
      "path": "/api/cron/platform-guardian",
      "schedule": "*/5 * * * *"
    }
  ]
}
```

Cron route validates `Authorization: Bearer [CRON_SECRET]` before executing.

---

## Database Migrations

Migrations in `supabase/migrations/`. Each file prefixed with a timestamp.

```
supabase/migrations/
  20260417_001_initial_schema.sql
  20260417_002_rls_policies.sql
  20260417_003_auth_hook.sql
  20260417_004_indexes.sql        ← required: see Critical Gap 3 above
```

Never edit a migration after it has been applied to production. Add a new migration instead.

---

## Supabase Storage

Bucket: `documents` (private)

Path: `documents/[organization-id]/[document-id].pdf`

Access: signed URLs generated server-side.
- Download: 1-hour expiry
- Co-signature review link: 24-hour expiry

---

## Local Development

```bash
npm install
cp .env.example .env.local
# Fill in Supabase local or remote credentials + Upstash credentials

npx supabase db push
npm run dev
```

For local Supabase:
```bash
npx supabase start
npx supabase db reset  # destructive
```

For local rate limiting: Upstash Redis has a free tier — use a real Upstash instance even in dev, or set `UPSTASH_REDIS_REST_URL=local` and mock the client.

---

## Deployment

Vercel auto-deploys on push to `main`.

**Required Vercel plan: Pro** — 60s function timeout needed for document generation.

No separate staging environment in v1 — use `environment: "staging"` in SDK config to tag staging traffic in the data.

Stripe webhooks: register `https://trustixy.com/api/webhooks/stripe` in Stripe dashboard.

---

## Supabase Type Generation

After any schema change:

```bash
npx supabase gen types typescript --project-id [id] > types/database.ts
```

Commit the generated types.
