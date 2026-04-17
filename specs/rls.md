# Row Level Security (RLS) Policies

All tables have RLS enabled. The principle: every query is scoped to the requesting user's `organization_id`, which is extracted from the JWT claim `org_id`.

Helper function used in all policies:

```sql
create or replace function auth.org_id() returns uuid language sql stable as $$
  select (auth.jwt() ->> 'org_id')::uuid;
$$;

create or replace function auth.user_role() returns text language sql stable as $$
  select auth.jwt() ->> 'role';
$$;
```

---

## SDK Layer

### projects

```sql
alter table projects enable row level security;

create policy "org_read" on projects for select
  using (organization_id = auth.org_id());

create policy "org_write" on projects for insert
  with check (organization_id = auth.org_id());

create policy "org_update" on projects for update
  using (organization_id = auth.org_id());

create policy "org_delete" on projects for delete
  using (organization_id = auth.org_id());
```

### sdk_tokens

```sql
alter table sdk_tokens enable row level security;

-- Users access tokens only through their project (join required)
create policy "org_read" on sdk_tokens for select
  using (
    project_id in (select id from projects where organization_id = auth.org_id())
  );

create policy "org_write" on sdk_tokens for insert
  with check (
    project_id in (select id from projects where organization_id = auth.org_id())
  );

create policy "org_delete" on sdk_tokens for delete
  using (
    project_id in (select id from projects where organization_id = auth.org_id())
  );
```

### agents

```sql
alter table agents enable row level security;

create policy "org_read" on agents for select
  using (organization_id = auth.org_id());

-- Partners can read agents for their clients
create policy "partner_read" on agents for select
  using (
    auth.user_role() = 'partner' and
    organization_id in (
      select client_organization_id from partner_clients
      where partner_id in (
        select id from partners
        where organization_id = auth.org_id()
      )
    )
  );

create policy "org_write" on agents for insert
  with check (organization_id = auth.org_id());

create policy "org_update" on agents for update
  using (organization_id = auth.org_id());

-- No hard delete — archived_at only
```

### sessions

```sql
alter table sessions enable row level security;

create policy "org_read" on sessions for select
  using (
    project_id in (select id from projects where organization_id = auth.org_id())
  );

-- Partners can read sessions for their clients
create policy "partner_read" on sessions for select
  using (
    auth.user_role() = 'partner' and
    project_id in (
      select p.id from projects p
      join partner_clients pc on pc.client_organization_id = p.organization_id
      join partners pa on pa.id = pc.partner_id
      where pa.organization_id = auth.org_id()
    )
  );

-- Ingest API uses service role — bypasses RLS
-- No insert policy for normal users
```

### actions

```sql
alter table actions enable row level security;

create policy "org_read" on actions for select
  using (
    project_id in (select id from projects where organization_id = auth.org_id())
  );

create policy "partner_read" on actions for select
  using (
    auth.user_role() = 'partner' and
    project_id in (
      select p.id from projects p
      join partner_clients pc on pc.client_organization_id = p.organization_id
      join partners pa on pa.id = pc.partner_id
      where pa.organization_id = auth.org_id()
    )
  );

-- Ingest uses service role only
```

### sensitive_file_alerts

```sql
alter table sensitive_file_alerts enable row level security;

create policy "org_read" on sensitive_file_alerts for select
  using (
    project_id in (select id from projects where organization_id = auth.org_id())
  );

create policy "org_update" on sensitive_file_alerts for update
  using (
    project_id in (select id from projects where organization_id = auth.org_id())
  );
```

---

## Organization & Users

### organizations

```sql
alter table organizations enable row level security;

create policy "self_read" on organizations for select
  using (id = auth.org_id());

create policy "self_update" on organizations for update
  using (id = auth.org_id());

-- platform_admin bypass handled via service role in server actions
```

### users

```sql
alter table users enable row level security;

create policy "org_read" on users for select
  using (organization_id = auth.org_id());

create policy "org_write" on users for insert
  with check (organization_id = auth.org_id());

create policy "org_update" on users for update
  using (organization_id = auth.org_id());
```

---

## Compliance Layer

### classifications

```sql
alter table classifications enable row level security;

create policy "org_read" on classifications for select
  using (
    agent_id in (select id from agents where organization_id = auth.org_id())
  );

create policy "partner_read" on classifications for select
  using (
    auth.user_role() = 'partner' and
    agent_id in (
      select a.id from agents a
      join partner_clients pc on pc.client_organization_id = a.organization_id
      join partners pa on pa.id = pc.partner_id
      where pa.organization_id = auth.org_id()
    )
  );

create policy "org_write" on classifications for insert
  with check (
    agent_id in (select id from agents where organization_id = auth.org_id())
  );
```

### obligations

```sql
alter table obligations enable row level security;

create policy "org_read" on obligations for select
  using (
    agent_id in (select id from agents where organization_id = auth.org_id())
  );

create policy "org_update" on obligations for update
  using (
    agent_id in (select id from agents where organization_id = auth.org_id())
  );
```

### documents

```sql
alter table documents enable row level security;

create policy "org_read" on documents for select
  using (
    agent_id in (select id from agents where organization_id = auth.org_id())
  );

create policy "partner_read" on documents for select
  using (
    auth.user_role() = 'partner' and
    agent_id in (
      select a.id from agents a
      join partner_clients pc on pc.client_organization_id = a.organization_id
      join partners pa on pa.id = pc.partner_id
      where pa.organization_id = auth.org_id()
    )
  );

create policy "org_write" on documents for insert
  with check (
    agent_id in (select id from agents where organization_id = auth.org_id())
  );
```

### document_signatures

```sql
alter table document_signatures enable row level security;

-- End user reads their own documents' signatures
create policy "org_read" on document_signatures for select
  using (
    document_id in (
      select d.id from documents d
      join agents a on a.id = d.agent_id
      where a.organization_id = auth.org_id()
    )
  );

-- Partner reads and writes signatures for their clients
create policy "partner_rw" on document_signatures for all
  using (
    auth.user_role() = 'partner' and
    partner_id in (
      select id from partners where organization_id = auth.org_id()
    )
  );
```

### audit_logs

```sql
alter table audit_logs enable row level security;

-- Immutable: no update or delete policies
create policy "org_read" on audit_logs for select
  using (organization_id = auth.org_id());

-- Written via service role only (never by client)
```

---

## Partner & Billing Layer

### partners

```sql
alter table partners enable row level security;

create policy "self_read" on partners for select
  using (organization_id = auth.org_id());

create policy "self_write" on partners for insert
  with check (organization_id = auth.org_id());

create policy "self_update" on partners for update
  using (organization_id = auth.org_id());
```

### partner_clients

```sql
alter table partner_clients enable row level security;

create policy "partner_read" on partner_clients for select
  using (
    partner_id in (select id from partners where organization_id = auth.org_id())
  );

create policy "partner_write" on partner_clients for insert
  with check (
    partner_id in (select id from partners where organization_id = auth.org_id())
  );

create policy "partner_delete" on partner_clients for delete
  using (
    partner_id in (select id from partners where organization_id = auth.org_id())
  );
```

### cosignature_transactions

```sql
alter table cosignature_transactions enable row level security;

-- Partners see their own transactions
create policy "partner_read" on cosignature_transactions for select
  using (
    partner_id in (select id from partners where organization_id = auth.org_id())
  );

-- End users see transactions on their documents
create policy "org_read" on cosignature_transactions for select
  using (
    client_organization_id = auth.org_id()
  );

-- Written via service role only (Stripe webhook handler)
```

---

## Ingest API

The ingest endpoint runs with the **Supabase service role key** (bypasses all RLS). It validates the SDK token manually before writing. Never expose the service role key client-side.

---

## Platform Admin

Platform admin server actions use the service role key and add an explicit `role = 'platform_admin'` check before executing. RLS is bypassed by service role — the check is done in application code, not database policy.

---

## Testing RLS

Before shipping, test each policy with:
1. A user from org A attempting to read org B's agents → should return empty
2. A partner attempting to read a non-client org's documents → should return empty
3. An unauthenticated request → should return empty (not 403 — RLS returns empty sets, not errors)
4. Ingest API with invalid token → 401 before any DB write
