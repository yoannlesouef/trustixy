# API

---

## SDK Ingest

```
POST   /v1/ingest                               — batch ingest actions from the SDK
```

Request body:
```json
{
  "session": {
    "id": "uuid",
    "project_slug": "my-backend",
    "agent": "code-assistant",
    "framework": "anthropic",
    "human_identity": "user@company.com",
    "environment": "production",
    "started_at": "2026-04-16T14:00:00.000Z",
    "metadata": {}
  },
  "actions": [
    {
      "id": "uuid",
      "sequence": 1,
      "type": "llm_call",
      "timestamp": "2026-04-16T14:00:01.234Z",
      "duration_ms": 420,
      "input": { "model": "claude-sonnet-4-6", "message_count": 3 },
      "output": { "stop_reason": "end_turn" },
      "affected_resources": [],
      "model": "claude-sonnet-4-6",
      "token_usage": { "input_tokens": 512, "output_tokens": 128 },
      "status": "success"
    }
  ]
}
```

Authentication: `Authorization: Bearer txk_live_...` (SDK token)

---

## Projects

```
POST   /projects                                — create a project
GET    /projects                                — list projects for the organization
GET    /projects/:id                            — project details + stats
PUT    /projects/:id                            — update project
DELETE /projects/:id                            — archive project
```

## SDK Tokens

```
POST   /projects/:id/tokens                     — create token (returns raw token once)
GET    /projects/:id/tokens                     — list tokens
DELETE /projects/:id/tokens/:tid                — revoke token
```

---

## Agents

One resource. Covers both SDK data and compliance record.

```
GET    /agents                                  — list all agents for the organization
GET    /agents/:id                              — agent details + SDK stats + compliance status
PUT    /agents/:id                              — update name, description, use_case, data_used, autonomy_level, owner, sector
DELETE /agents/:id                              — archive agent (soft delete)
```

---

## Sessions

```
GET    /agents/:id/sessions                     — sessions for this agent
GET    /sessions/:sid                           — session details + action summary
GET    /sessions/:sid/actions                   — full ordered action list
```

## Actions

```
GET    /agents/:id/actions                      — all actions for this agent (filterable)
GET    /actions/:aid                            — single action details
```

---

## Sensitive File Alerts

```
GET    /alerts                                  — list alerts (filterable by status, agent)
GET    /alerts/:alid                            — alert details + linked session/action
POST   /alerts/:alid/acknowledge                — acknowledge
POST   /alerts/:alid/dismiss                    — dismiss
```

---

## Classification

```
POST   /agents/:id/classify                     — run classification
GET    /agents/:id/classifications              — classification history
GET    /agents/:id/classifications/current      — current classification + confidence + obligations
```

## Obligations

```
GET    /agents/:id/obligations                  — current obligations checklist
PUT    /agents/:id/obligations/:oid             — update done status (body: { done: true })
```

## Compliance Status

```
GET    /agents/:id/compliance-status            — status + last classification date
GET    /organizations/:id/compliance-summary    — org-level count by status
```

---

## Documents

```
POST   /agents/:id/documents                    — generate document for current classification
GET    /agents/:id/documents                    — list all versions
GET    /agents/:id/documents/current            — current document
GET    /agents/:id/documents/:did               — specific historical version
```

## Document Co-signature

```
POST   /documents/:id/signature/request         — request co-signature
GET    /documents/:id/signature                 — current signature status
POST   /documents/:id/signature/approve         — prescriber approves and signs
POST   /documents/:id/signature/request-changes — prescriber requests changes
POST   /documents/:id/signature/decline         — prescriber declines
```

---

## Audit Logs

```
GET    /agents/:id/audit-logs                   — compliance audit trail for an agent
GET    /organizations/:id/audit-logs            — org-level audit trail
```

---

## Partners

```
POST   /partners                                — apply to become a partner (includes scope acceptance)
GET    /partners/:id                            — partner profile
PUT    /partners/:id                            — update partner settings
```

## Partner Clients

```
GET    /partners/:id/clients                    — list clients with compliance health
POST   /partners/:id/clients/invite             — invite a client organization
DELETE /partners/:id/clients/:clientId          — remove a client
```

## Partner Co-signature Queue

```
GET    /partners/:id/signature-requests         — pending documents awaiting review
GET    /partners/:id/signature-requests/:did    — review a specific document
```

## Partner Billing (Per-act)

```
PUT    /partners/:id/billing                    — set cosignature_fee + connect Stripe
GET    /partners/:id/billing/transactions       — transaction history
POST   /partners/:id/billing/stripe-connect     — initiate Stripe Connect onboarding
```

## Integrator Projects

```
POST   /integrator/projects                     — create a client project
GET    /integrator/projects                     — list projects
GET    /integrator/projects/:id                 — project details + compliance summary
POST   /integrator/projects/:id/report          — generate PDF compliance report
GET    /integrator/projects/:id/report          — retrieve latest report
```

## Organizations

```
POST   /organizations                           — create organization
GET    /organizations/:id                       — organization details
PUT    /organizations/:id                       — update organization
```

---

## Export

```
GET    /agents/:id/actions/export               — action log (JSON or CSV)
GET    /alerts/export                           — sensitive file alerts (JSON)
GET    /agents/:id/audit-logs/export            — compliance audit trail (PDF or CSV)
GET    /organizations/:id/compliance-report     — full org compliance report PDF
```
