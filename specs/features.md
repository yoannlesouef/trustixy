# Features

---

## SDK Integration

The SDK is the entry point for all platform data. Installed once, it instruments every AI framework call automatically.

* Supported frameworks: Anthropic SDK, OpenAI SDK, Vercel AI SDK, LangChain (JS + Python), LlamaIndex, CrewAI, Claude Code hooks, MCP server
* One-line wrap: `trustixy.wrap(client, { project, agent })`
* Zero behavior change — SDK is transparent to the underlying framework
* Automatic session grouping (per process / per request context)
* Configurable content capture: off by default (metadata only), opt-in for full content with redaction patterns
* CLI for Claude Code hook integration
* Self-hosted ingest endpoint available on Enterprise

Full SDK reference: [`/specs/sdk.md`](sdk.md)

---

## Action Audit Log

The core data store. Every action taken by every agent, structured and immutable.

### What is captured per action

* `session_id` — groups actions from one agent run
* `agent_identity` — which agent (name + framework)
* `human_identity` — who triggered the session (if known)
* `sequence` — order within the session
* `type` — `llm_call` · `tool_call` · `file_read` · `file_write` · `file_delete` · `api_call` · `shell_command` · `bash` · `mcp_call` · `custom`
* `timestamp` — UTC, millisecond precision
* `duration_ms` — execution time
* `affected_resources` — file paths, URLs, or identifiers accessed or modified
* `model` — LLM model used (for `llm_call` actions)
* `token_usage` — input/output/cache tokens
* `status` — success · error · timeout

### Log views

* **Timeline** — chronological list of all actions across all sessions, filterable by agent, action type, resource, time range
* **Session view** — all actions within a specific session, ordered by sequence
* **Resource view** — all actions that touched a specific file, URL, or resource identifier
* **Agent view** — all sessions and actions attributed to a specific agent identity

### Search and filter

* Filter by: project · agent · action type · affected resource · time range · status · human identity
* Full-text search on resource paths and metadata
* Exportable as JSON or CSV

---

## Session Replay

Step-by-step reconstruction of any agent session. Not a log dump — a structured walkthrough.

### What replay shows

For each action in sequence:

1. **Context** — what the agent had available at this point (prior messages, tool results, memory state if captured)
2. **Decision** — what the agent was asked to do / what it inferred
3. **Action** — what it executed (tool call, file write, LLM prompt, etc.)
4. **Outcome** — what the result was (output, error, affected resources)

### Use cases

* **Debugging** — understand why an agent produced unexpected output
* **Incident response** — reconstruct what an agent did during an anomalous session
* **Onboarding** — walk new team members through how an agent works in practice
* **Compliance audit** — present a structured evidence trail of agent operations to an auditor

### Replay controls

* Step forward / backward through actions
* Jump to a specific action by type or resource
* Compare two sessions side by side (diff view for affected resources)
* Export replay as a PDF session report

---

## Agent Registry

Auto-discovered from SDK data. No manual entry required.

### Auto-discovery flow

1. SDK reports agent identity + framework on first call
2. Platform creates a draft `agent_identity` record for the project
3. Dashboard surfaces new agent identities as "discovered" with a count of sessions and actions
4. User promotes a discovered identity to the full compliance registry in one click
5. The compliance classification form is pre-filled from observed behavior (autonomy level from tool usage, data types from action inputs, affected population from deployment context)

### Registry fields (per agent identity)

* Name, slug, framework
* Project association
* First seen / last seen / total sessions / total actions
* Human identities that triggered sessions (list)
* Top affected resources (most frequently touched files/URLs)
* Linked compliance record (if promoted to AI system registry)
* Status: `discovered` | `registered` | `archived`

---

## Anomaly Detection

Monitors agent behavior patterns and flags deviations.

### Anomaly types

| Type | Description |
|---|---|
| `unexpected_scope` | Agent accessed a resource outside its expected operational scope |
| `sensitive_file_access` | Agent read or modified a file matching a sensitive pattern (e.g., `.env`, `secrets/`, `*.pem`) |
| `policy_violation` | Agent performed an action explicitly prohibited by the project's policy rules |
| `volume_spike` | Session contains significantly more actions than the agent's historical baseline |
| `error_rate_spike` | Session has an unusually high ratio of errored actions |
| `exfiltration_pattern` | Agent read a large number of files followed by an outbound API call |
| `unusual_shell_commands` | Shell commands outside the agent's normal profile |

### Anomaly severities

`low` · `medium` · `high` · `critical`

### Policy rules

Define what agents are permitted to do:

```json
{
  "agent": "code-assistant",
  "rules": [
    { "deny": "file_write", "paths": ["**/.env", "**/secrets/**"] },
    { "deny": "api_call", "hosts": ["!*.internal.company.com"] },
    { "alert": "shell_command", "severity": "high" }
  ]
}
```

Rules are configured in the project settings. Violations create anomaly records and trigger alerts.

### Alert delivery

* In-platform notification (banner + anomaly center)
* Email notification
* Slack webhook (configurable per project)
* SIEM export (JSON / CEF)

---

## EU AI Act Compliance Registry

Same as before — but now auto-populated from SDK data instead of manual entry.

* Create / edit / archive AI systems (soft-delete only — audit trail preserved)
* Fields: name, description, use_case, data_used, autonomy_level, owner, sector
* `source` field: `sdk_discovered` | `manual` | `wizard`
* `linked_agent_identity_id` — links to the SDK agent identity that feeds this record
* `compliance_status` per system: `unclassified` | `compliant` | `needs_review` | `at_risk`
* Status auto-degrades to `needs_review` when:
  * A regulatory update affects the system's use case or risk category
  * The system description is updated
  * More than 12 months have passed since the last classification
  * The anomaly detection agent flags significant behavioral change

---

## Risk Classification

* Pre-filled from SDK behavioral data where available (autonomy level, data types, deployment scope)
* **5 essential questions** (required): description, affected population, human override, data sensitivity, deployment context
* **5 optional refinements**: sector, scale, explainability, bias testing, regulated product
* LLM classification aligned to current regulatory version
* Every classification stores: risk level, explanation, obligations, confidence score, regulatory version, trigger
* Classification history accessible per system
* Low-confidence warning (<70%) surfaced as an invitation to request expert review — not a blocker

---

## Obligations Checklist

* Generated from each classification
* Each item: title, description, implementation_guide, regulatory_reference, status (todo | in_progress | done), optional due date
* Top 3 most critical obligations shown by default — "Show all X" toggle
* Completion percentage visible on system page and dashboard
* Completing all obligations → status upgrades to `compliant`
* Checklist versioned: new classification creates new checklist (previous archived)

---

## Compliance Status

* Per-system badge: `Compliant` | `Needs attention` | `Not assessed`
* Organization summary: count by status
* Partner portal shows health per client organization

---

## Regulatory Change Alerts

* Triggered when Compliance Intelligence Tracker detects a confirmed EU AI Act update
* Affected systems auto-flagged as `needs_review`
* Alert shown as non-intrusive banner on system page + badge in nav
* Email notification
* Workflow: pending → acknowledged → dismissed (all logged)

---

## Compliance Document Generator

* Linked to a specific `classification_id` and optionally to a `session_id` (for evidence linking)
* Mandatory disclaimer block (non-removable, compact footer style)
* Header: date · risk level · regulatory version date · status
* Versioned per system; older versions accessible in history
* Exportable as PDF
* When linked to a session: includes "Evidence Trail" section showing the action log summary

---

## Prescriber Certification

Two levels allowing prescribers to start quickly and upgrade when ready.

### Level 1 — Scope Acceptance (15 minutes)
* Read the Co-signature Scope Document
* Unlocks co-signature for Minimal and Limited risk systems
* Badge: "Scope-accepted review"

### Level 2 — Full Certification (~90 minutes)
* 4 modules: EU AI Act risk levels, how to review a classification, co-signature responsibilities, sector quick-guides
* 15-question assessment, 75% pass threshold
* Unlocks co-signature for all risk levels including High
* Badge: "Certified AI Compliance Reviewer"
* Valid 12 months; renewal: 20-min module + 5-question quiz

---

## Prescriber Co-signature

* Prescriber reviews the classification, compliance document, and linked action log evidence
* Actions: approve & sign | request changes | decline
* On approval: prescriber name, firm, certification level, date embedded in document
* Optional professional notes — included in document as "Reviewer Comments"
* No linked prescriber → user offered partner directory + "Download with disclaimer" fallback

---

## Audit Log (Platform-level)

Separate from the SDK action log — records compliance platform actions.

* Immutable, always running
* Actions logged: `system_create` · `system_update` · `system_archive` · `classify` · `obligation_updated` · `generate_document` · `signature_requested` · `signature_approved` · `signature_declined` · `alert_acknowledged` · `alert_dismissed` · `status_changed` · `certification_completed` · `cosignature_scope_accepted`
* Exportable as PDF or CSV

---

## Export

* Export compliance document (PDF) — disclaimer + signature block + regulatory version + optional evidence trail
* Export session replay (PDF) — structured walkthrough of an agent session
* Export action log (JSON / CSV) — filterable, time-ranged
* Export organization compliance report — all systems, statuses, obligation completion
* SIEM export (JSON / CEF) — for anomaly events
