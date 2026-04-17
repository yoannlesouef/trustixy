# Infrastructure

## Stack

| Layer | Choice | Reason |
|---|---|---|
| Frontend + API routes | Next.js 14 (App Router) | SSR, server actions, API routes in one project |
| Database + Auth | Supabase | Postgres + RLS + Auth + Storage in one service |
| Styling | Tailwind CSS | Utility-first, fast iteration |
| AI | Anthropic Claude API | Classification + document generation |
| Email | Resend | Native Next.js/Vercel integration, simple SDK |
| Payments | Stripe | Subscriptions + Connect for partner payouts |
| Deployment | Vercel | Zero-config Next.js deployment |
| Internal agents | Vercel Cron + Supabase Edge Functions | Scheduled serverless |

---

## Project Structure

```
trustixy/
├── app/
│   ├── (dashboard)/              # Authenticated org users
│   │   ├── layout.tsx            # Requires auth, role=admin
│   │   ├── page.tsx              # Activity (home)
│   │   ├── agents/
│   │   │   ├── page.tsx          # Agents list
│   │   │   └── [id]/
│   │   │       └── page.tsx      # Agent detail (scrollable)
│   │   ├── alerts/
│   │   │   └── page.tsx          # Sensitive file alerts
│   │   └── settings/
│   │       ├── page.tsx          # General settings
│   │       ├── team/page.tsx
│   │       ├── billing/page.tsx
│   │       └── tokens/page.tsx
│   │
│   ├── (partner)/                # Prescriber portal
│   │   ├── layout.tsx            # Requires auth, role=partner
│   │   ├── page.tsx              # Partner home (queue + client list)
│   │   ├── clients/
│   │   │   ├── page.tsx
│   │   │   └── [id]/page.tsx
│   │   ├── queue/
│   │   │   └── [docId]/page.tsx  # Co-signature review
│   │   └── settings/
│   │       ├── page.tsx
│   │       ├── billing/page.tsx
│   │       └── team/page.tsx
│   │
│   ├── admin/                    # Internal admin
│   │   ├── layout.tsx            # Requires role=platform_admin
│   │   ├── page.tsx              # Admin dashboard
│   │   ├── organizations/page.tsx
│   │   ├── partners/page.tsx
│   │   └── agents/page.tsx       # Internal AI agent logs
│   │
│   ├── (auth)/
│   │   ├── login/page.tsx
│   │   ├── signup/page.tsx
│   │   ├── reset-password/page.tsx
│   │   └── join/
│   │       └── invite/page.tsx   # Team member invitation landing
│   │
│   ├── verify/
│   │   └── [docId]/page.tsx      # Public document verification
│   │
│   ├── api/
│   │   ├── v1/
│   │   │   └── ingest/
│   │   │       └── route.ts      # SDK ingest endpoint
│   │   ├── webhooks/
│   │   │   ├── stripe/
│   │   │   │   └── route.ts
│   │   │   └── resend/
│   │   │       └── route.ts
│   │   └── cron/                 # Vercel Cron targets
│   │       ├── platform-guardian/route.ts
│   │       ├── customer-success/route.ts
│   │       ├── partner-success/route.ts
│   │       └── prescriber-hunter/route.ts
│   │
│   ├── layout.tsx                # Root layout
│   └── globals.css
│
├── components/
│   ├── ui/                       # Shadcn/ui base components
│   ├── dashboard/                # Dashboard-specific components
│   ├── partner/                  # Partner portal components
│   └── shared/                   # Shared across both portals
│
├── lib/
│   ├── supabase/
│   │   ├── client.ts             # Browser client
│   │   ├── server.ts             # Server client (server components/actions)
│   │   └── admin.ts              # Service role client (ingest, webhooks)
│   ├── anthropic.ts              # Claude API client
│   ├── stripe.ts                 # Stripe client
│   ├── resend.ts                 # Resend client
│   ├── pdf.ts                    # PDF generation
│   └── sensitive-patterns.ts     # File pattern matching
│
├── actions/                      # Next.js server actions
│   ├── agents.ts
│   ├── classifications.ts
│   ├── documents.ts
│   ├── signatures.ts
│   ├── partners.ts
│   └── billing.ts
│
├── types/
│   └── database.ts               # Generated Supabase types
│
├── middleware.ts                 # Route protection
├── .env.local                    # Local development (gitignored)
├── .env.example                  # Committed template
└── vercel.json                   # Cron schedule
```

---

## Environment Variables

### `.env.example`

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
STRIPE_PRO_PRICE_ID=price_...            # €99/month recurring
STRIPE_INTEGRATOR_PRICE_ID=price_...     # €399/month recurring

# Email (Resend)
RESEND_API_KEY=re_...
RESEND_FROM_EMAIL=noreply@trustixy.com
RESEND_REPLY_TO=support@trustixy.com

# Internal alerts (Platform Guardian)
SLACK_WEBHOOK_URL=https://hooks.slack.com/...

# App
NEXT_PUBLIC_APP_URL=https://trustixy.com
CRON_SECRET=...                          # Shared secret to authenticate cron routes

# Admin
ADMIN_EMAIL=yoann@trustixy.com           # Bootstrapped platform admin email
```

---

## Ingest API Architecture

The ingest endpoint is the highest-traffic endpoint. Architecture for reliability and performance:

### Route: `POST /api/v1/ingest`

```typescript
// Runs on Vercel Edge Runtime for lower latency
export const runtime = 'edge';

// Flow:
// 1. Validate Authorization header (SDK token)
// 2. Hash token, lookup in sdk_tokens (service role)
// 3. Upsert session record
// 4. Bulk insert actions
// 5. Update agent.last_seen_at + total counters (single UPDATE)
// 6. Run sensitive file pattern matching synchronously (fast, no LLM)
// 7. If matches found: insert sensitive_file_alerts + queue email notification
// 8. Return 200 { received: N }
```

### Limits

| Parameter | Limit |
|---|---|
| Max actions per batch | 100 |
| Max request body size | 1 MB |
| Rate limit per SDK token | 100 requests/minute |
| Rate limit response | 429 with `Retry-After: 60` header |

### SDK retry behavior (spec for SDK implementation)

- On 429: exponential backoff, start at 1s, max 30s, max 5 retries
- On 5xx: exponential backoff, start at 500ms, max 10s, max 3 retries
- On 4xx (except 429): no retry — bad request or auth failure
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
    },
    {
      "path": "/api/cron/customer-success",
      "schedule": "0 7 * * *"
    },
    {
      "path": "/api/cron/partner-success",
      "schedule": "0 8 * * *"
    },
    {
      "path": "/api/cron/prescriber-hunter",
      "schedule": "0 9 * * 1"
    }
  ]
}
```

All cron routes validate `Authorization: Bearer [CRON_SECRET]` before executing. Vercel sends this automatically if configured in the dashboard.

---

## Supabase Storage

Used for PDF storage only.

Buckets:
- `documents` — compliance document PDFs (private, accessed via signed URLs)

Signed URL expiry: 1 hour for downloads, 24 hours for co-signature review links.

No user-uploaded files in v1.

---

## Supabase Type Generation

After any schema change:

```bash
npx supabase gen types typescript --project-id [id] > types/database.ts
```

Run this as part of the development workflow. Commit the generated types.

---

## Local Development

```bash
# 1. Clone and install
npm install

# 2. Copy env template
cp .env.example .env.local
# Fill in Supabase local or remote credentials

# 3. Run migrations
npx supabase db push

# 4. Start dev server
npm run dev
```

For local Supabase:
```bash
npx supabase start        # starts local Postgres + Auth + Studio
npx supabase db reset     # re-runs all migrations (destructive)
```

---

## Database Migrations

Migrations live in `supabase/migrations/`. Each migration is a `.sql` file prefixed with a timestamp.

Convention:
```
supabase/migrations/
  20260417_001_initial_schema.sql
  20260417_002_rls_policies.sql
  20260417_003_auth_hook.sql
```

Never edit a migration after it has been applied to production. Add a new migration instead.

---

## Deployment

Vercel auto-deploys on push to `main`. No separate staging environment in v1 — use `environment: "staging"` in SDK config to separate staging traffic in the data.

Production environment variables set in Vercel dashboard. Never committed.

Stripe webhooks: register `https://trustixy.com/api/webhooks/stripe` in Stripe dashboard.
