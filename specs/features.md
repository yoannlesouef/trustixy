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

### Session lifecycle

Sessions are server-managed — the SDK does not call an endpoint to close them.

| Status | Set when |
|---|---|
| `running` | First ingest batch received for this session |
| `completed` | No ingest received for 15 minutes after last batch |
| `error` | Final action in the session has `status = error` |
| `timeout` | Session open for >24 hours without activity |

The Platform Guardian cron (every 5 min) handles the `running → completed/timeout` transitions.

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
* Classification history preserved per agent (`is_current` flag; server sets old classification to `is_current = false` on new classification)
* Low-confidence warning (<70%): invitation to request expert review — not a blocker

### Reclassification

When a user clicks [Reclassify]:
1. A new classification is created; previous set to `is_current = false`
2. Previous obligations are soft-archived (`archived_at = now()`)
3. New obligations checklist generated from new classification
4. If a co-signed document existed: `signature_status` reset to `unsigned` — the old co-signature is preserved as a historical record but the agent is no longer co-signed
5. Audit log entry: `classify`

### Expert review request

When confidence score < 70%, the agent page shows: *"Consider requesting expert review from your prescriber partner."*
Clicking [Request review] sends an email notification to the linked prescriber (if any) with a link to the document. No separate table — this is a one-time email trigger, not a queued workflow in v1.

---

## Obligations Checklist

* Generated from each classification — **only for Limited, High, and Unacceptable risk**
* Minimal risk → shows "No specific obligations under the EU AI Act" message instead
* Each item: title · description · implementation guide · regulatory reference · `done` checkbox
* Top 3 shown by default — "Show all X" toggle
* Completion count visible on agent page and dashboard
* All checked → compliance status upgrades to `compliant`
* New classification creates a new checklist; previous archived

---

## Compliance Status

DB value → UI display mapping:

| `compliance_status` | UI badge | Color |
|---|---|---|
| `unclassified` | Not assessed | Grey |
| `needs_review` | Needs attention | Amber |
| `at_risk` | At risk | Red |
| `compliant` | Compliant | Green |

`at_risk` is set manually by a platform admin or future automation. `needs_review` is auto-set when description is updated post-classification or when `last_classified_at < now() - 12 months` (nightly Supabase job).

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
* **Free plan:** co-signature button not shown at all (not a paywall modal)
* **Pro plan:** co-signature button shown; requires a linked prescriber partner

### Certification

Scope acceptance is part of partner signup — no separate flow or page.

When applying to become a partner, the applicant reads and accepts the Co-signature Scope Document (checkbox). On approval, `scope_accepted_at` is set and co-signature is immediately unlocked for all risk levels.

---

## PDF Footer

Every generated PDF (signed and unsigned) includes a non-removable footer:

> *"Compliance record managed with Trustixy · trustixy.com"*

Enterprise plan can white-label (no footer).

---

## Feedback & Community

Full spec: [`/specs/feedback-community.md`](feedback-community.md)

In-app feedback board accessible to all authenticated users at `/feedback`.

* Users submit bugs or enhancement proposals (type + title + description)
* Users upvote any item (one vote per user, togglable)
* Platform admin manages status (`Open` → `Planned` → `In Progress` → `Done` | `Declined`) and posts team responses
* Admin can push any item to GitHub as an issue (labeled `community` + type) via GitHub API — works on private repos
* Optional community social link (Discord, Telegram, etc.) set by platform admin; hidden from UI when not configured

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
