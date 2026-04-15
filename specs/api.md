# API

## AI Systems

POST   /ai-systems                              — create a new AI system
GET    /ai-systems                              — list all systems with current compliance_status
GET    /ai-systems/:id                          — get system details
PUT    /ai-systems/:id                          — update system (auto-sets needs_review if description changed)
DELETE /ai-systems/:id                          — archive system (soft delete, preserves audit trail)

## Classification

POST   /ai-systems/:id/classify                 — run a new classification (triggered_by: user)
GET    /ai-systems/:id/classifications          — full classification history
GET    /ai-systems/:id/classifications/current  — latest classification with confidence score and obligations

## Obligations

GET    /ai-systems/:id/obligations              — current obligations checklist
PUT    /ai-systems/:id/obligations/:oid         — update obligation status (todo | in_progress | done)

## Compliance Status

GET    /ai-systems/:id/compliance-status        — current status, last classification, confidence score, pending alerts
GET    /organizations/:id/compliance-dashboard  — org-level summary: systems by status, alert count, obligation completion rate

## Documents

POST   /ai-systems/:id/documents                — generate document for current classification
GET    /ai-systems/:id/documents                — list all document versions
GET    /ai-systems/:id/documents/current        — get the current document
GET    /ai-systems/:id/documents/:did           — get a specific historical document

## Document Co-signature

POST   /documents/:id/signature/request         — request co-signature from prescriber partner
GET    /documents/:id/signature                 — get current signature status
POST   /documents/:id/signature/approve         — prescriber approves and signs (partner portal)
POST   /documents/:id/signature/request-changes — prescriber requests changes before signing
POST   /documents/:id/signature/decline         — prescriber declines signing

## Audit Logs

GET    /ai-systems/:id/audit-logs               — full audit trail for a system
GET    /organizations/:id/audit-logs            — org-level audit trail (all systems)

## Regulatory Versions

GET    /regulatory-versions                     — list all tracked regulatory versions
GET    /regulatory-versions/latest              — latest EU AI Act version in effect
GET    /regulatory-versions/:id                 — details + affected use cases

## Compliance Alerts

GET    /organizations/:id/alerts                — list all alerts for the organization
GET    /ai-systems/:id/alerts                   — alerts specific to a system
POST   /ai-systems/:id/alerts/:aid/acknowledge  — acknowledge an alert
POST   /ai-systems/:id/alerts/:aid/dismiss      — dismiss an alert

## Partners

POST   /partners                                — apply to become a partner
GET    /partners/:id                            — get partner profile
PUT    /partners/:id                            — update white-label config, settings

## Partner Clients

GET    /partners/:id/clients                    — list clients with compliance health summary
POST   /partners/:id/clients/invite             — invite a client organization
DELETE /partners/:id/clients/:clientId          — remove a client

## Partner Co-signature Queue

GET    /partners/:id/signature-requests         — list pending documents awaiting co-signature
GET    /partners/:id/signature-requests/:did    — review a specific document before signing

## Organizations

POST   /organizations                           — create organization (on signup)
GET    /organizations/:id                       — get organization details
PUT    /organizations/:id                       — update organization
