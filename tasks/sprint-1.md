# Sprint 1

## Goal
Deliver the core compliance registry: auth, AI systems CRUD, versioned classification, obligations checklist, document generation with disclaimer, and audit log.

## Tasks

### Setup
* Setup Next.js app (App Router)
* Setup Supabase (DB + Auth)
* Setup Tailwind CSS
* Configure Claude API (claude-sonnet-4-6)
* Seed `regulatory_versions` table with current EU AI Act version

### Auth & Multi-tenancy
* User signup / login (Supabase Auth)
* Organization creation on signup
* All queries scoped to organization_id

### Database Schema
* organizations, users
* ai_systems (with compliance_status, archived_at)
* classifications (with confidence_score, requires_expert_review, regulatory_version_id, triggered_by, is_current)
* obligations (linked to classification)
* documents (with classification_id, version, disclaimer_version, signature_status, is_current)
* audit_logs (with organization_id)
* regulatory_versions (seed with current version)

### Identification Wizard
* Sector selection (stored on organization)
* Tool inventory checklist (categorized by sector — static list for V1)
* Wizard Analysis LLM call: assess in-scope tools, return suggested systems with pre-filled descriptions
* Results review screen: editable suggestions, out-of-scope tools collapsed
* Batch system creation from confirmed wizard results
* wizard_completed_at stored on organization
* Wizard nudge on dashboard until completed

### Description Quality Check
* LLM quality check triggered on every system save
* description_quality_score and description_quality_feedback stored on ai_system
* Inline quality indicator on system page (green / orange)
* Classification blocked with warning if score < 60

### AI Systems CRUD
* Create / edit / archive (soft delete)
* compliance_status auto-set to `unclassified` on creation
* Status degrades to `needs_review` on description update
* source field: wizard | manual

### Classification
* 10-question questionnaire UI
* LLM call (Claude API) with classification prompt
* Store: risk_level, explanation, obligations, confidence_score, requires_expert_review
* Link to regulatory_version_id (current version)
* Update ai_system.compliance_status and last_classified_at
* Expert review warning banner if requires_expert_review: true

### Obligations Checklist
* Generate obligations from classification result
* Inline status update (todo → in_progress → done)
* Completion progress bar on system page and dashboard

### Document Generation
* LLM call (Claude API) with document prompt
* Link document to classification_id
* Embed disclaimer (disclaimer_version)
* Version increment per system
* Set previous document is_current: false

### Audit Log
* Log all actions: create, update, archive, classify, obligation_updated, generate_document, status_changed

### Dashboard UI
* Compliance summary bar (by status)
* AI systems table with status badges, obligation progress, last classified
* Add system button

### Export
* PDF export with disclaimer block, regulatory version, classification date

### Landing Page
* Headline + sub-headline reflecting registry + traceability + human validation
* Dual CTA: Start free / Become a partner
