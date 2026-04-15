# API

## Identification Wizard

POST   /wizard/analyze                          — submit tool checklist + sector; returns suggested systems with pre-filled descriptions and likely risk levels
POST   /wizard/confirm                          — batch-create confirmed systems from wizard suggestions
GET    /wizard/tool-catalog                     — list of known AI tools organized by category and sector

## Description Quality

POST   /ai-systems/:id/check-description        — run description quality LLM check; returns score and improvement suggestions
                                                  (called automatically on save; can be re-triggered manually)

## AI Systems

POST   /ai-systems                              — create a new AI system (triggers description quality check)
GET    /ai-systems                              — list all systems with current compliance_status
GET    /ai-systems/:id                          — get system details
PUT    /ai-systems/:id                          — update system (triggers description quality check + auto-sets needs_review if description changed)
DELETE /ai-systems/:id                          — archive system (soft delete, preserves audit trail)

## Classification

POST   /ai-systems/:id/classify                 — run a new classification (triggered_by: user)
GET    /ai-systems/:id/classifications          — full classification history
GET    /ai-systems/:id/classifications/current  — latest classification with confidence score and obligations

## Obligations

GET    /ai-systems/:id/obligations              — current obligations checklist (includes implementation_guide per item)
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
POST   /documents/:id/signature/approve         — prescriber approves and signs (with optional professional_notes)
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

## Prescriber Certification

GET    /partners/:id/certification              — get current certification status and expiry
POST   /partners/:id/certification/start        — start the certification training module
POST   /partners/:id/certification/complete     — submit quiz answers; returns pass/fail and score
POST   /partners/:id/certification/renew        — start renewal (shorter module)
GET    /partners/:id/cosignature-scope          — get the current co-signature scope document
POST   /partners/:id/cosignature-scope/accept   — prescriber accepts the scope document (versioned)

## Partners

POST   /partners                                — apply to become a partner
GET    /partners/:id                            — get partner profile (includes is_certified, certification expiry)
PUT    /partners/:id                            — update white-label config, settings

## Partner Clients

GET    /partners/:id/clients                    — list clients with compliance health summary
POST   /partners/:id/clients/invite             — invite a client organization
DELETE /partners/:id/clients/:clientId          — remove a client

## Partner Co-signature Queue

GET    /partners/:id/signature-requests         — list pending documents awaiting co-signature (blocked if not certified)
GET    /partners/:id/signature-requests/:did    — review a specific document before signing

## Organizations

POST   /organizations                           — create organization (on signup)
GET    /organizations/:id                       — get organization details (includes sector, plan, wizard_completed_at)
PUT    /organizations/:id                       — update organization (sector, name)

## Partner Billing (Model 1 — Per-act Co-signature)

PUT    /partners/:id/billing                    — set cosignature_fee and connect Stripe account
GET    /partners/:id/billing/transactions       — list co-signature transactions with amounts and commission
POST   /partners/:id/billing/stripe-connect     — initiate Stripe Connect onboarding flow

## Federations (Model 2 — Licence Fédération)

POST   /federations/:id/members                 — add a member organization (grants federation_member plan)
GET    /federations/:id/members                 — list member organizations with compliance health
DELETE /federations/:id/members/:orgId          — remove a member organization
GET    /federations/:id/compliance-summary      — aggregate compliance health across all members (count by status, alert count)

## Integrator Projects (Model 3 — Licence Intégrateur)

POST   /integrator/projects                     — create a client project (links integrator partner to client org)
GET    /integrator/projects                     — list all projects with compliance status
GET    /integrator/projects/:id                 — project details + compliance summary
POST   /integrator/projects/:id/report          — generate PDF compliance report for the project (white-labeled)
GET    /integrator/projects/:id/report          — retrieve the latest generated report
