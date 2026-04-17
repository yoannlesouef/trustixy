# Error Handling

## Principles

- Never show raw error messages or stack traces to users
- Every error has a user-facing message and a logged internal message
- Distinguish: user errors (fixable by user), system errors (fixable by us), external errors (third-party dependency)
- Fail open for read operations, fail closed for write operations
- Ingest errors must never silently drop data

---

## SDK Ingest Errors

The SDK is the critical data path. Errors here mean lost audit trail.

| Error | HTTP status | SDK behavior | User impact |
|---|---|---|---|
| Invalid token | 401 | Log to console, no retry | Silent until dev checks console |
| Token revoked | 401 | Log to console, no retry | Same |
| Batch too large (>100 actions) | 400 | Split and retry each half | Transparent |
| Rate limited | 429 | Exponential backoff, retry up to 5x | Slight delay |
| Server error (5xx) | 500/503 | Exponential backoff, retry up to 3x | Slight delay |
| Network timeout | — | Retry with same batch | Transparent |
| All retries exhausted | — | Log warning to console: "Trustixy: failed to send [N] actions after [N] retries. Data may be incomplete." | Explicit warning |

SDK never throws — all errors are caught and logged. The wrapped client always returns the underlying framework response regardless of SDK failures.

---

## Classification Errors

| Error | User-facing message | Internal action |
|---|---|---|
| LLM call fails (Anthropic API error) | "Classification failed. Please try again in a few minutes." | Log to agent_logs, alert Platform Guardian |
| LLM returns malformed JSON | "Classification returned an unexpected result. Please try again." | Log full response, do not save partial classification |
| LLM call times out (>30s) | "Classification is taking longer than expected. Please try again." | Log timeout |
| Confidence score 0 (complete failure) | "We couldn't classify this agent with confidence. Please review the description and try again." | Save with `requires_expert_review: true` |

On classification failure: no `classifications` row is created. The agent remains `unclassified`. The user can retry immediately.

---

## Document Generation Errors

| Error | User-facing message | Internal action |
|---|---|---|
| LLM call fails | "Document generation failed. Please try again." | Log to agent_logs |
| PDF render fails | "Could not generate PDF. Please try again." | Log render error |
| Storage upload fails (after 1 retry) | "Document created but could not be saved. Downloading directly." | Stream PDF to client, log storage error |
| No current classification | Blocked in UI — button not shown | N/A |

---

## Authentication Errors

| Error | User-facing message | Behavior |
|---|---|---|
| Invalid credentials | "Invalid email or password." | Stay on login page |
| Unconfirmed email | "Please confirm your email before logging in." | Show resend link |
| Expired session | "Your session has expired. Please log in again." | Redirect to /login?next=[current-path] |
| Unauthorized route access | Silent redirect to /login | No error message shown |

---

## Stripe Errors

### Checkout

| Error | User-facing message |
|---|---|
| Card declined | "Your payment was declined. Please try a different card." |
| Card expired | "Your card has expired. Please update your payment method." |
| Insufficient funds | "Your payment was declined. Please try a different card." |
| General error | "Payment failed. Please try again or contact support." |

Stripe Checkout handles most of these natively on their hosted page. Only post-redirect errors (webhook failures) need custom handling.

### Subscription webhook failure

If a webhook fails to process (our server error):
- Return 500 to Stripe — Stripe will retry for up to 3 days
- Log the failed event with the raw payload
- Do NOT partially update organization plan
- Monitor via Platform Guardian: alert if webhook failure rate >5%

### Co-signature payment failure

If the PaymentIntent for a co-signature charge fails:
1. Do NOT record the signature as approved
2. Notify the end user: "Payment failed. Please update your payment method to complete this request."
3. Re-queue the signature request as `pending_payment`
4. Prescriber's approval is held — not cancelled — for 48h while user fixes payment

---

## Email Delivery Errors

Email is non-critical — failure to send an email should never fail the underlying action.

| Error | Behavior |
|---|---|
| Resend API error | Log error, continue with the main action |
| Invalid email address | Log error, continue |
| Bounce/delivery failure | Resend webhook updates bounce status — disable future emails to that address |

All email sends are fire-and-forget from the request path. Use `waitUntil()` in Next.js or run via Supabase Edge Function to avoid blocking responses.

---

## Partner Co-signature Errors

| Error | User-facing message (partner) | User-facing message (client) |
|---|---|---|
| Document not found | "This request is no longer available." | — |
| Already signed | "This document has already been signed." | — |
| Client not linked | Blocked in UI — not accessible | — |
| Signature saved but email fails | Approval succeeds; email logged as failed | Client sees co-signed status in dashboard |

---

## Rate Limiting

Apply to all API routes (not just ingest):

| Route group | Limit | Window |
|---|---|---|
| `POST /api/v1/ingest` | 100 req / SDK token | 60 seconds |
| Auth routes (`/login`, `/signup`) | 10 req / IP | 60 seconds |
| Classification (`POST /agents/:id/classify`) | 5 req / org | 60 seconds |
| Document generation | 10 req / org | 60 seconds |
| All other authenticated routes | 200 req / org | 60 seconds |

Rate limiting via Vercel's built-in rate limiting or `@upstash/ratelimit` with Redis.

Response: `429 Too Many Requests` with `Retry-After` header.

---

## Error Logging

Use structured logging. Every error log includes:
- `timestamp`
- `error_type` — one of: `auth`, `ingest`, `classification`, `document`, `stripe`, `email`, `rls`
- `organization_id` (if available)
- `user_id` (if available)
- `message` — internal description
- `request_id` — for tracing across logs

In development: log to console.
In production: Vercel logs + Supabase logs. Platform Guardian monitors error rates from these.

For critical errors (classification failure, ingest failure rate >1%, Stripe webhook failure): Platform Guardian sends immediate Slack alert.

---

## User-Facing Error Pages

| Route | Page |
|---|---|
| `/verify/[docId]` — doc not found | "Document not found or has been revoked." (not a 404) |
| Any 404 | "This page doesn't exist." + [Go home →] |
| Any 500 | "Something went wrong on our end. We've been notified." + [Go home →] |
| Auth required | Redirect to `/login?next=[path]` — no error page |

Never show: stack traces, SQL errors, internal IDs, or Supabase error codes to end users.
