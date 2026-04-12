# API

# API

## AI Systems

POST /ai-systems
GET /ai-systems
GET /ai-systems/:id
PUT /ai-systems/:id
DELETE /ai-systems/:id

## Classification

POST /ai-systems/:id/classify

## Documents

POST /ai-systems/:id/generate-doc
GET /ai-systems/:id/document

## Audit Logs

GET /ai-systems/:id/logs

## Partners

POST /partners                        — apply to become a partner
GET /partners/:id                     — get partner profile
PUT /partners/:id                     — update white-label config, settings

## Partner Clients

GET /partners/:id/clients             — list clients managed by partner
POST /partners/:id/clients/invite     — invite a client organization
DELETE /partners/:id/clients/:clientId — remove a client

## Organizations

POST /organizations                   — create organization (on signup)
GET /organizations/:id                — get organization details
PUT /organizations/:id                — update organization

