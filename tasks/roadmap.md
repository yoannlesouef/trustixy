# Roadmap

7 sprints. Build in order — each sprint unblocks the next.

---

## Sprint 1 — Supabase: Schema + RLS
**Everything depends on this.**

All tables, indexes, RLS policies, auth hook, and RPC functions. No application code is written until this is done and tested.

Done when: cross-org data isolation is verified, rate limit RPC works, nightly purge job runs.

---

## Sprint 2 — `@trustixy/sdk` (npm)
**Needed to test the ingest API.**

The JavaScript/TypeScript SDK package — wraps Anthropic, OpenAI, and Claude Code hooks. Batches and ships actions to the ingest endpoint. Published to npm.

Done when: `npm install @trustixy/sdk` + 3-line snippet sends a session to a local ingest endpoint.

---

## Sprint 3 — Next.js: Auth + Ingest API
**Unblocks SDK end-to-end testing.**

Project scaffold, Supabase auth, all API routes (ingest, webhooks, cron stub). No dashboard UI — just the backend that the SDK talks to and the auth layer that gates everything else.

Done when: SDK running against production sends sessions; auth signup/login/invite works; Stripe webhook receives events.

---

## Sprint 4 — Next.js: Organization Dashboard
**Core product. First user value.**

The full experience for developers, security, and compliance teams — activity timeline, agent registry, classification, obligations, document generation, billing, feedback board, and all email sequences.

Done when: a user can go from signup → SDK install → first session → classify → generate document → request co-signature → upgrade to Pro, in under 15 minutes.

---

## Sprint 5 — Next.js: Partner Portal
**Revenue channel.**

The prescriber experience — signature queue, client management, co-signature review, Stripe Connect payouts. Partner application flow and email sequences.

Done when: a partner can apply, get approved, invite a client, review a co-signature request, approve it, and receive a Stripe payout.

---

## Sprint 6 — Next.js: Platform Admin
**Operational control.**

The internal admin dashboard — org management, partner approval, platform health, feedback moderation, GitHub issue push, cost monitoring.

Done when: Platform Guardian cron runs every 5 min, smoke test passes nightly, admin can manage all orgs and partners from `/admin`.

---

## Sprint 7 — `trustixy` (pip)
**Parallel with sprints 4–6.**

Python SDK — same capabilities as the npm package. Wraps Anthropic and OpenAI Python clients. Published to PyPI.

Done when: `pip install trustixy` + 3-line snippet sends a session to the production ingest endpoint.

---

## Post-launch

- Vercel AI SDK integration (npm)
- LangChain / LlamaIndex / CrewAI handlers (npm + pip)
- MCP server (`@trustixy/mcp`)
- SSO (SAML/OIDC) for Enterprise
- Self-hosted ingest endpoint (Enterprise)
- Multi-regulation expansion (GDPR, NIS2, AI Liability Directive)
- Marketplace listings (AWS, Azure, Google Cloud)
