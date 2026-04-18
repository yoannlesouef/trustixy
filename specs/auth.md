# Auth

## Provider

Supabase Auth. No third-party auth service needed — Supabase handles JWTs, sessions, and email delivery.

---

## Sign-up Method

**Email + password only for v1.** No magic link, no OAuth.

Rationale: compliance users need a stable account they can share with a team. Magic links expire and cause friction in shared workspaces.

---

## Sign-up Flow

### New organization

1. User visits `/signup`
2. Fills in: first name, last name, email, password, organization name, sector
3. Submit → Supabase `auth.signUp()` creates the auth user
4. Server action creates:
   - `organizations` row (name, sector, plan = `free`)
   - `users` row (organization_id, email)
   - Default `projects` row (name = "default", slug = `default`)
   - First SDK token (returned to user on the quickstart screen)
5. Redirect → `/onboarding` (SDK quickstart screen)
6. Confirmation email sent by Supabase (email confirmation required before first login)

### Prescriber-referred entry

1. Prescriber sends invite → client receives email with link: `trustixy.com/join?ref=[partner-slug]&token=[invite-token]`
2. Client clicks link → `/signup?ref=[partner-slug]` — same signup form, partner slug pre-stored in session
3. On account creation: `partner_clients` row is created linking the new org to the partner
4. Client lands on `/onboarding` — same SDK quickstart path

---

## Login Flow

1. User visits `/login`
2. Email + password → Supabase `auth.signInWithPassword()`
3. On success: redirect to `/` (Activity page)
4. On failure: show inline error — "Invalid email or password"
5. "Forgot password" → Supabase `auth.resetPasswordForEmail()` → email with reset link → `/reset-password`

---

## Session Management

- Supabase handles JWT issuance and refresh automatically
- Session stored in cookies (SSR-compatible via `@supabase/ssr`)
- JWT contains: `sub` (user id), `org_id` (organization id), `role` (`admin` | `partner` | `platform_admin`)
- Custom JWT claims set via Supabase Auth Hook (database function) on sign-in

### JWT custom claims

```sql
-- auth hook: set org_id and role on JWT
create or replace function auth.custom_access_token_hook(event jsonb)
returns jsonb language plpgsql as $$
declare
  claims jsonb;
  user_org_id uuid;
  user_role text;
begin
  select u.organization_id, 'admin' into user_org_id, user_role
  from users u where u.id = (event->>'user_id')::uuid;

  -- check if partner
  if exists (select 1 from partners p
    join users u on u.organization_id = p.organization_id
    where u.id = (event->>'user_id')::uuid) then
    user_role := 'partner';
  end if;

  -- check if platform admin
  if exists (select 1 from platform_admins where user_id = (event->>'user_id')::uuid) then
    user_role := 'platform_admin';
  end if;

  claims := event->'claims';
  claims := jsonb_set(claims, '{org_id}', to_jsonb(user_org_id));
  claims := jsonb_set(claims, '{role}', to_jsonb(user_role));
  return jsonb_set(event, '{claims}', claims);
end;
$$;
```

---

## Roles

**v1 has one role per context — no per-user role configuration needed.**

| Role | Who | Access |
|---|---|---|
| `admin` | Any org user | Full access to their organization's data |
| `partner` | Prescriber partner users | Partner portal only — their clients' data via `partner_clients` |
| `platform_admin` | Trustixy internal | `/admin` route — all organizations |

All org users are `admin` — no viewer/editor split in v1.

---

## Route Protection

### Next.js middleware (`middleware.ts`)

```typescript
// Protected route groups:
// /app/(dashboard)/* → requires auth + role = admin
// /app/(partner)/*   → requires auth + role = partner
// /app/admin/*       → requires auth + role = platform_admin
// /app/verify/*      → public, no auth required
// /app/api/v1/ingest → SDK token auth (not Supabase JWT)
```

Middleware checks the Supabase session cookie. Unauthenticated requests redirect to `/login?next=[path]`.

---

## Team Member Invitation

1. Admin visits Settings → Team → [Invite member]
2. Enters email address
3. Server creates a `pending_invitations` record (email, organization_id, token, expires_at)
4. Invitation email sent with link: `/join/invite?token=[token]`
5. Recipient clicks link → `/signup` pre-filled with email, org name shown, password required
6. On signup: `users` row created with the inviter's `organization_id`; invitation marked used

`pending_invitations` table:
- id, organization_id, email, token (uuid), invited_by (user_id), expires_at (48h), used_at

---

## Partner Portal Auth

Same Supabase project. Partners log in at `trustixy.com/login` — same login form.

After login, JWT role is `partner` → middleware routes to `/partner/*` instead of `/`.

Partners who are also org users (e.g. a firm that also uses Trustixy for their own agents) have role `partner` — they access their own org data through the partner portal's "My organization" link, not through the main dashboard.

---

## Admin Route

`/admin` is a Next.js route group protected by `role = platform_admin`.

`platform_admins` table:
- id, user_id (references auth.users), created_at

Platform admin accounts are created manually via Supabase dashboard — no self-serve signup.

---

## Password Requirements

- Minimum 8 characters
- No complexity requirements beyond length (avoid frustrating users)
- Enforced client-side and via Supabase Auth config

---

## SDK Token Auth (Ingest API)

The ingest endpoint (`POST /api/v1/ingest`) does **not** use Supabase JWTs. It uses SDK tokens.

**Hashing: SHA-256, not bcrypt.** API tokens are long random strings — they don't need bcrypt's slowness (which is designed for short user passwords). SHA-256 takes <1ms and is the industry standard for API token hashing (GitHub, Stripe, Linear all use it).

Validation:
1. Extract `Authorization: Bearer txk_live_[token]` header
2. Hash with SHA-256: `createHash('sha256').update(rawToken).digest('hex')`
3. Look up `sdk_tokens` where `token_hash = hash AND revoked_at IS NULL`
4. If not found → 401
5. If found → attach `project_id` and `token_id` to request context
6. Update `sdk_tokens.last_used_at` + run rate limit check (see below)

Token format: `txk_live_` prefix + 32 random bytes as hex. Generated server-side, shown once.

```typescript
import { createHash, randomBytes } from 'crypto';

// Token creation
const rawToken = `txk_live_${randomBytes(32).toString('hex')}`;
const tokenHash = createHash('sha256').update(rawToken).digest('hex');
// Store tokenHash in sdk_tokens.token_hash; return rawToken to user once

// Token validation (ingest route)
const tokenHash = createHash('sha256').update(rawToken).digest('hex');
const { data: token } = await supabase
  .from('sdk_tokens')
  .select('id, project_id, revoked_at')
  .eq('token_hash', tokenHash)
  .is('revoked_at', null)
  .single();
```

## SDK Token Rate Limiting

Enforced via a Postgres counter on `sdk_tokens` — no external service needed.

```sql
-- Columns added to sdk_tokens
requests_this_minute  integer    default 0,
minute_window         timestamptz default now()
```

Atomic check-and-increment via a Supabase RPC function:

```sql
CREATE OR REPLACE FUNCTION check_rate_limit(p_token_id uuid, p_limit integer)
RETURNS boolean AS $$
DECLARE
  current_window timestamptz := date_trunc('minute', now());
BEGIN
  UPDATE sdk_tokens SET
    requests_this_minute = CASE
      WHEN minute_window = current_window THEN requests_this_minute + 1
      ELSE 1
    END,
    minute_window = current_window
  WHERE id = p_token_id;

  RETURN (
    SELECT requests_this_minute <= p_limit
    FROM sdk_tokens WHERE id = p_token_id
  );
END;
$$ LANGUAGE plpgsql;
```

Returns `false` → respond 429 with `Retry-After: 60`. At v1 scale this is reliable and requires no additional infrastructure.
