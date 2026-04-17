# Features

---

## SDK Integration

The SDK is the entry point for all platform data. Installed once, it instruments every AI framework call automatically.

* **v1 supported:** Anthropic SDK · OpenAI SDK · Claude Code hooks
* **Planned (v2):** Vercel AI SDK · LangChain · LlamaIndex · CrewAI · MCP
* One-line wrap: `trustixy.wrap(client, { project, agent })`
* Zero behavior change — transparent to the underlying framework
* Automatic session grouping (per process / per request context)
* Configurable content capture: off by default (metadata only), opt-in with redaction patterns
* CLI for Claude Code hook integration
* Self-hosted ingest endpoint available on Enterprise

Full SDK reference: [`/specs/sdk.md`](sdk.md)

---

## Action Audit Log

Every action taken by every agent, structured and immutable.

### Captured per action

* `agent_id` — which agent
* `session_id` — groups actions from one run
* `human_identity` — who triggered the session (if known)
* `sequence` — order within the session
* `type` — `llm_call` · `tool_call` · `file_read` · `file_write` · `file_delete` · `api_call` · `shell_command` · `bash` · `mcp_call` · `custom`
* `timestamp` · `duration_ms` · `affected_resources` · `model` · `token_usage` · `status`

### Views

* **Timeline** — all actions across all sessions, filterable
* **Session view** — all actions within a session, ordered by sequence
* **Agent view** — all sessions attributed to a specific agent

### Search and filter

Filter by: agent · action type · affected resource · time range · status · human identity  
Full-text search on resource paths · Exportable as JSON or CSV

---

## Agents

One concept. The agent is both the SDK-discovered entity and the compliance record. No promotion step — every agent discovered by the SDK is immediately available for classification.

### Auto-discovery

1. SDK reports agent name + framework on first call
2. Platform creates the `agent` record automatically
3. Dashboard nudges: *"[N] agent(s) not yet classified. [Classify →]"*

### Per agent

* Name · Slug · Framework · First seen / Last seen · Total sessions / Total actions
* Compliance status: `unclassified` | `compliant` | `needs_review` | `at_risk`
* Classification + obligations + document — all on the same agent record
* Soft-delete only — agents are never hard-deleted

---

## Sensitive File Alerts

Pattern matching on `affected_resources`. No LLM involved.

**Triggers:** agent access to `.env` · `secrets/` · `*.pem` · `credentials*` · `.ssh/`

**Severity:** `high` (fixed)

**Delivery:** in-platform banner + email to org admin

---

## Risk Classification

* Pre-filled from SDK behavioral data (autonomy level, data types, deployment scope)
* **5 questions** (most pre-filled, all editable): use case, affected population, human override, data sensitivity, deployment context
* LLM classification against current EU AI Act version
* Stores: risk level · explanation · confidence score · regulatory version
* Classification history preserved per agent
* Low-confidence warning (<70%): invitation to request expert review — not a blocker

---

## Obligations Checklist

* Generated from each classification
* Each item: title · description · implementation guide · regulatory reference · `done` checkbox
* Top 3 shown by default — "Show all X" toggle
* Completion count visible on agent page and dashboard
* All checked → compliance status upgrades to `compliant`
* New classification creates a new checklist; previous archived

---

## Compliance Status

* Per-agent badge: `Compliant` | `Needs attention` | `Not assessed`
* Organization summary: count by status
* Partner portal shows health per client organization

---

## Compliance Document Generator

* Linked to a `classification_id` and optionally to a `session_id` (evidence trail)
* Mandatory disclaimer block — non-removable
* Header: date · risk level · regulatory version · status
* Versioned per agent; history accessible via toggle
* Exportable as PDF
* Evidence section when SDK session linked: sessions count, action types, environments

---

## Prescriber Co-signature

* Prescriber reviews the classification, document, and linked SDK evidence
* Actions: approve & sign | request changes | decline
* On approval: prescriber name, firm, date embedded in document
* Optional professional notes — included as "Reviewer Comments"
* No linked prescriber → "Download with disclaimer" fallback

### Certification

Scope acceptance is part of partner signup — no separate flow or page.

When applying to become a partner, the applicant reads and accepts the Co-signature Scope Document (checkbox). On approval, `scope_accepted_at` is set and co-signature is immediately unlocked for all risk levels.

---

## Viral Loop — Document Verification

Every co-signed PDF is a distribution asset. Two mechanisms:

### Footer badge on PDF

Every generated PDF (signed and unsigned) includes a footer:

> *"Compliance record managed with Trustixy · trustixy.com/verify/[doc-id]"*

This is non-removable on Free and Pro plans. Enterprise can white-label (no badge).

### Verification page (`/verify/[doc-id]`)

Public page, no login required. Displays:
- Organization name
- Agent name
- Risk level + classification date
- Co-signer name and firm (if co-signed)
- Document status: `active` | `superseded` | `revoked`
- "Generated with Trustixy" CTA → trustixy.com

Target audience: auditors, regulators, procurement teams receiving the document. CTA converts to awareness and potential signups.

No sensitive data exposed on this page — only the fields listed above.

---

## Audit Log (Platform-level)

Separate from the SDK action log — records compliance platform actions.

* Immutable, always running
* Actions: `agent_created` · `agent_updated` · `agent_archived` · `classify` · `obligation_updated` · `generate_document` · `signature_requested` · `signature_approved` · `signature_declined` · `status_changed` · `scope_accepted`
* Exportable as PDF or CSV

---

## Export

* Compliance document — PDF with disclaimer, signature block, evidence trail
* Action log — JSON or CSV, filterable by time range
* Organization compliance report — all agents, statuses, obligation completion
