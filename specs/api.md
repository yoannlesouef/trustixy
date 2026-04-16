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
GET    /projects                                — list all projects for the organization
GET    /projects/:id                            — project details + stats
PUT    /projects/:id                            — update project name/description
DELETE /projects/:id                            — archive project
```

## SDK Tokens

```
POST   /projects/:id/tokens                     — create an SDK token (returns raw token once)
GET    /projects/:id/tokens                     — list tokens (name, last_used_at, status)
DELETE /projects/:id/tokens/:tid                — revoke a token
```

## Agent Identities

```
GET    /projects/:id/agents                     — list discovered agent identities
GET    /projects/:id/agents/:aid                — agent details + usage stats
PUT    /projects/:id/agents/:aid                — update name, link to ai_system
POST   /projects/:id/agents/:aid/register       — promote discovered agent to compliance registry
```

---

## Sessions

```
GET    /projects/:id/sessions                   — list sessions (filterable by agent, time, status, human_identity)
GET    /projects/:id/sessions/:sid              — session details + action summary
GET    /projects/:id/sessions/:sid/actions      — full ordered action list for session
GET    /projects/:id/sessions/:sid/replay       — replay-formatted session: context + decision + action + outcome per step
```

## Actions

```
GET    /projects/:id/actions                    — list all actions (filterable by type, resource, agent, time range, status)
GET    /projects/:id/actions/:aid               — single action details
GET    /projects/:id/actions/by-resource        — actions that touched a specific resource path
                                                  query: ?resource=/src/payments.ts
```

---

## Anomaly Detection

```
GET    /projects/:id/anomalies                  — list anomaly detections (filterable by severity, type, status)
GET    /projects/:id/anomalies/:anid            — anomaly details + linked session/action
POST   /projects/:id/anomalies/:anid/acknowledge — acknowledge anomaly
POST   /projects/:id/anomalies/:anid/dismiss    — dismiss anomaly
```

## Policy Rules

```
GET    /projects/:id/policies                   — list policy rules
POST   /projects/:id/policies                   — create a policy rule
PUT    /projects/:id/policies/:pid              — update a rule
DELETE /projects/:id/policies/:pid              — delete a rule
```

---

## Identification Wizard

```
POST   /wizard/analyze                          — submit tool checklist + sector; returns suggested systems
POST   /wizard/confirm                          — batch-create confirmed systems from wizard suggestions
GET    /wizard/tool-catalog                     — known AI tools organized by category and sector
```

## Description Quality

```
POST   /ai-systems/:id/check-description        — run description quality check; returns score and suggestions
```

---

## AI Systems (Compliance Registry)

```
POST   /ai-systems                              — create a new AI system (manual entry)
GET    /ai-systems                              — list all systems with current compliance_status
GET    /ai-systems/:id                          — system details + linked agent identity + SDK stats
PUT    /ai-systems/:id                          — update system
DELETE /ai-systems/:id                          — archive system (soft delete)
```

## Classification

```
POST   /ai-systems/:id/classify                 — run a new classification
GET    /ai-systems/:id/classifications          — full classification history
GET    /ai-systems/:id/classifications/current  — latest classification with confidence + obligations
```

## Obligations

```
GET    /ai-systems/:id/obligations              — current obligations checklist
PUT    /ai-systems/:id/obligations/:oid         — update obligation status
```

## Compliance Status

```
GET    /ai-systems/:id/compliance-status        — current status, last classification, pending alerts
GET    /organizations/:id/compliance-dashboard  — org-level summary
```

---

## Documents

```
POST   /ai-systems/:id/documents                — generate document for current classification
GET    /ai-systems/:id/documents                — list all document versions
GET    /ai-systems/:id/documents/current        — current document
GET    /ai-systems/:id/documents/:did           — specific historical document
```

## Document Co-signature

```
POST   /documents/:id/signature/request         — request co-signature from prescriber partner
GET    /documents/:id/signature                 — current signature status
POST   /documents/:id/signature/approve         — prescriber approves and signs
POST   /documents/:id/signature/request-changes — prescriber requests changes
POST   /documents/:id/signature/decline         — prescriber declines
```

---

## Audit Logs (Platform-level)

```
GET    /ai-systems/:id/audit-logs               — compliance audit trail for a system
GET    /organizations/:id/audit-logs            — org-level compliance audit trail
```

---

## Regulatory Versions

```
GET    /regulatory-versions                     — list all tracked regulatory versions
GET    /regulatory-versions/latest              — latest EU AI Act version in effect
GET    /regulatory-versions/:id                 — details + affected use cases
```

## Compliance Alerts

```
GET    /organizations/:id/alerts                — all alerts for the organization
GET    /ai-systems/:id/alerts                   — alerts specific to a system
POST   /ai-systems/:id/alerts/:aid/acknowledge  — acknowledge an alert
POST   /ai-systems/:id/alerts/:aid/dismiss      — dismiss an alert
```

---

## Prescriber Certification

```
GET    /partners/:id/certification              — current certification status and expiry
POST   /partners/:id/certification/start        — start certification training
POST   /partners/:id/certification/complete     — submit quiz answers
POST   /partners/:id/certification/renew        — start renewal module
GET    /partners/:id/cosignature-scope          — current co-signature scope document
POST   /partners/:id/cosignature-scope/accept   — prescriber accepts scope document
```

## Partners

```
POST   /partners                                — apply to become a partner
GET    /partners/:id                            — partner profile (is_certified, certification expiry)
PUT    /partners/:id                            — update white-label config, settings
```

## Partner Clients

```
GET    /partners/:id/clients                    — list clients with compliance health summary
POST   /partners/:id/clients/invite             — invite a client organization
DELETE /partners/:id/clients/:clientId          — remove a client
```

## Partner Co-signature Queue

```
GET    /partners/:id/signature-requests         — pending documents awaiting co-signature
GET    /partners/:id/signature-requests/:did    — review a specific document before signing
```

## Organizations

```
POST   /organizations                           — create organization
GET    /organizations/:id                       — organization details
PUT    /organizations/:id                       — update organization
```

---

## Partner Billing (Model 1 — Per-act Co-signature)

```
PUT    /partners/:id/billing                    — set cosignature_fee + connect Stripe account
GET    /partners/:id/billing/transactions       — list co-signature transactions
POST   /partners/:id/billing/stripe-connect     — initiate Stripe Connect onboarding
```

## Federations (Model 2)

```
POST   /federations/:id/members                 — add member organization
GET    /federations/:id/members                 — list member organizations
DELETE /federations/:id/members/:orgId          — remove member
GET    /federations/:id/compliance-summary      — aggregate compliance health
```

## Integrator Projects (Model 3)

```
POST   /integrator/projects                     — create a client project
GET    /integrator/projects                     — list all projects
GET    /integrator/projects/:id                 — project details + compliance summary
POST   /integrator/projects/:id/report          — generate white-labeled PDF compliance report
GET    /integrator/projects/:id/report          — retrieve latest generated report
```

---

## Export

```
GET    /projects/:id/sessions/:sid/export/pdf   — session replay as PDF report
GET    /projects/:id/actions/export             — action log export (JSON or CSV); query: ?format=csv&from=...&to=...
GET    /projects/:id/anomalies/export           — anomaly export (JSON or CEF)
GET    /ai-systems/:id/audit-logs/export        — compliance audit trail (PDF or CSV)
GET    /organizations/:id/compliance-report     — full org compliance report PDF
```
