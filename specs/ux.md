# UX

## Style

* Clean (Notion-like)
* Minimal
* Fast

---

## Pages

### Landing

* Headline: "Your AI systems, compliant by design"
* Sub-headline: "Register, classify, and monitor your AI systems against the EU AI Act — with human validation when it matters."
* CTA (primary): Start free
* CTA (secondary): Become a partner
* Trust signals: partner logos, EU AI Act badge
* Three value pillars (icons):
  * **Structured registry** — a living inventory, not a one-time document
  * **Full audit trail** — every change, every classification, timestamped and versioned
  * **Human-validated** — prescriber co-signature for legally stronger documents

---

### Onboarding — Identification Wizard

Triggered automatically after account creation (first login). Skippable but prominently re-surfaced from the dashboard until completed ("Discover your AI systems →").

**Step 1 — Sector**
* Single-select: Healthcare | Finance & Insurance | HR & Recruitment | Legal | Education | Retail & E-commerce | Manufacturing | Public Sector | Other
* Subtitle: "We'll use this to show you the tools most relevant to your industry."

**Step 2 — Tool Inventory**
* Categorized checklist of common AI-powered tools, filtered by selected sector:
  * Productivity & development (Copilot, GitHub Copilot, Notion AI…)
  * Customer service (Intercom AI, Zendesk AI, custom chatbots…)
  * HR & Recruitment (CV screening tools, Workday AI, LinkedIn Recruiter…)
  * Sales & CRM (Salesforce Einstein, HubSpot AI, lead scoring…)
  * Finance (fraud detection, credit scoring, forecasting tools…)
  * Custom: free-text field — "Add a tool not listed"
* Helper text: "Check all tools your organization uses, even if you're not sure they're in scope. We'll sort it out in the next step."

**Step 3 — Analysis Results**
* Loading state: "Analyzing your tools against the EU AI Act…" (~3–5 seconds)
* Results card per in-scope tool:
  * Tool name + likely risk level badge (color-coded)
  * Brief explanation (1–2 sentences) of why it's in scope
  * Pre-filled system name and description (editable inline)
* Out-of-scope tools listed in a collapsed section with explanation
* "I have another system not listed" — opens manual add form

**Step 4 — Confirm & Create**
* Summary: "We'll create X systems in your registry"
* User can edit names/descriptions before confirming
* Description quality is checked on confirm; low-quality descriptions show inline warning with suggestions
* CTA: "Create my registry" — batch creates all confirmed systems
* Skip option for individual systems: "Add later"

**Post-wizard dashboard state**
* All created systems shown with status `Unclassified`
* Nudge banner: "Your registry is ready. Start by classifying your highest-risk systems first."
* Suggested first action: classify the system with the highest likely risk level

---

### Dashboard (End User)

* Alert banner at top if any systems have pending compliance alerts or unacknowledged regulatory updates
* **Wizard nudge** (if `wizard_completed_at` is null): "Discover your AI systems — answer 3 questions to identify which tools need compliance attention. Takes 2 minutes."
* Compliance summary bar: X compliant · Y needs review · Z unclassified · W at risk
* AI systems table:
  * Name
  * Use case
  * Risk level (latest classification)
  * Compliance status badge: `Compliant` | `Needs Review` | `At Risk` | `Unclassified`
  * Obligations progress (e.g. 3/5 done)
  * Signature status badge: `Unsigned` | `Pending` | `Co-signed`
  * Last classified (relative date)
  * Actions
* Button: Add system
* Organization context shown if accessed via a partner (partner name/logo if white-labeled)

---

### System Page

* System info (name, description, use case, owner) — editable inline
* **Description quality indicator** — shown below the description field:
  * Green: "Description quality: Good" (score ≥ 60)
  * Orange: "Description quality: Needs improvement" + inline suggestions (score < 60)
* **Compliance Status badge** (prominent, color-coded)
* **Expert review warning banner** — shown when `requires_expert_review: true`:
  * "This classification has low confidence (X%). We recommend requesting prescriber review before generating a compliance document."
  * CTA: Request expert review

#### Current Classification section
* Risk level badge + explanation
* Regulatory version used (e.g. "EU AI Act — April 2026")
* Confidence score (shown as percentage with color indicator: green ≥ 70%, orange 50–69%, red < 50%)
* Assessed on date + triggered by (user / regulatory change)

#### Obligations Checklist section
* Actionable list of obligations for this classification
* Each item: checkbox, title, regulatory reference, optional due date
* **"How to implement" expandable section** per obligation — shows `implementation_guide`
* Completion progress bar
* Status updatable inline by the user

#### Active Alerts section (if any)
* Alert message + what changed in the regulation
* Actions: Re-classify now | Acknowledge | Dismiss

#### Documents section
* Current document card: generation date, regulatory version, confidence score, signature status
* Previous document versions (marked as superseded)
* CTA: Generate new document | Request co-signature (if unsigned)

#### Classification History section
* Timeline of all past classifications
* Each entry: date, risk level, confidence score, trigger type, regulatory version, linked document

#### Buttons
* Classify (re-run assessment) — blocked with warning if description quality score < 60
* Generate document (enabled only when a current classification exists)

---

### Classification Questionnaire

* 10 structured questions covering:
  * Use case and sector
  * Affected population (who is impacted by decisions)
  * Degree of decision autonomy (human override possible?)
  * Data sensitivity (personal, biometric, financial)
  * Human oversight mechanisms in place
  * Deployment context (internal tool vs. public-facing)
* Progress indicator (question X of 10)
* Answers stored and shown alongside the classification result

---

### Document Page

* Document content (structured, professional)
* Header: generated on [date] · based on classification of [date] · regulatory version [label] · confidence [score]
* **Disclaimer block** (always visible, non-dismissable):
  > *"This document was generated by Trustixy using AI. It does not constitute legal advice. For legally binding compliance, request co-signature from your prescriber partner."*
* **Signature block**:
  * If unsigned: "Request co-signature" CTA + explanation of what co-signature means
  * If pending: "Awaiting review by [partner firm]"
  * If co-signed: Verified badge + prescriber name, firm, certification badge, date + professional notes (if any)
  * If changes requested: Review notes from prescriber + "Update and re-submit" CTA
* Superseded banner if a newer classification exists without a regenerated document
* Export button (PDF) — includes disclaimer and signature block

---

### Co-signature Request Flow (End User)

1. User clicks "Request co-signature" on the document page
2. Modal: select prescriber partner (pre-filled if org is linked to one partner)
3. Note: if the linked prescriber is not certified or certification expired, a warning is shown — "Your prescriber's certification has expired. Co-signed documents require a certified prescriber."
4. Optional message to the prescriber
5. Confirmation: "Your request has been sent to [Partner Firm]. You will be notified when they respond."
6. Document status set to `pending_signature`

---

### Prescriber Co-signature Page (Partner Portal)

* Blocked with full-screen prompt if prescriber is not certified: "Complete your certification to co-sign documents → Start certification"
* Accessed from partner portal "Signature Requests" queue
* Shows: client organization, system name, classification summary, confidence score, sector, obligations list
* Full document preview
* **Professional notes field** (optional): free-text area — "Add your reviewer comments, analysis, or caveats. These will appear in the document and PDF as a 'Reviewer Comments' section."
* Helper text: "Your notes strengthen the document's professional standing and protect you by documenting the scope of your review."
* Three actions:
  * **Approve & Sign** — confirmation modal; prescriber name, firm, and certification badge auto-filled; professional notes included if written
  * **Request Changes** — text field for notes sent back to the client
  * **Decline** — with reason
* On approval: document marked `co-signed`, client notified by email, audit log updated

---

### Prescriber Certification Flow (Partner Portal)

**Entry points:**
* Partner portal home if `is_certified: false` — full banner: "Complete your certification to unlock co-signature"
* Partner portal settings → Certification tab

**Step 1 — Introduction**
* What the certification covers (EU AI Act fundamentals, classification criteria, co-signature scope)
* Time commitment: ~2 hours for initial certification, ~30 minutes for renewal
* What they get: certification badge on all co-signed documents, access to co-signature queue

**Step 2 — Co-signature Scope Document**
* Full text of the scope document (what they confirm when signing, what is excluded)
* Must scroll to the bottom before accepting
* Checkbox: "I have read and understood the co-signature scope"
* Accept button — records `cosignature_scope_version` accepted

**Step 3 — Training Modules**
* 5 modules, each ~20–25 minutes:
  1. EU AI Act overview — structure, timeline, key definitions
  2. Risk classification — criteria, examples by sector
  3. Obligations by risk level — what each level requires
  4. How to review a Trustixy classification — what to verify, red flags
  5. Sector deep-dives — Healthcare, Finance, HR (user selects relevant ones)
* Progress saved — user can resume across sessions
* Each module ends with 3–4 comprehension questions

**Step 4 — Final Assessment**
* 20 questions, 80% pass threshold
* Immediate result: pass (certification issued) or fail (can retry after 24h)
* On pass: `certified_at` and `expires_at` (+ 12 months) stored

**Certification status in partner profile**
* Badge: "Certified AI Compliance Reviewer — valid until [date]"
* Renewal reminder: 60 days before expiry (email + in-app banner)
* Expired certification: co-signature queue blocked, warning shown

---

### Alerts Center (Organization-level)

* List of all active compliance alerts across all systems
* Filterable by: status (pending / acknowledged / dismissed), system, alert type
* Bulk acknowledge / dismiss
* Alert detail: what changed in the regulation, which systems are affected, recommended action

---

### Compliance History Page (per system)

* Full timeline: all classifications and document versions
* Each classification card: date, trigger, risk level, confidence score, regulatory version, linked document, obligations completion
* Exportable as PDF for audit purposes

---

### Partner Portal

* Overview: clients count, systems count, documents generated, pending signature requests
* **Certification status banner** (prominent if not certified or expiring soon)
* **Compliance health summary**: % of client systems compliant vs. needs review vs. at risk
* **Signature queue**: documents awaiting co-signature (blocked if not certified)
* Client list: name, compliance health, last activity, link to client dashboard
* Invite client button (sends branded onboarding email)
* Revenue share dashboard (referrals, commissions)
* White-label settings (logo, colors, custom domain)

---

### Partner Onboarding

* Apply to become a partner (form)
* Select tier: Referral | Reseller | Embedded | Group
* Agreement acceptance (includes co-signature responsibility scope)
* Post-approval: redirect to certification flow

---

### Static Pages

* **Home** — see `specs/content/home.md`
* **User Guide (End User)** — see `specs/content/guide-end-user.md`
* **User Guide (Prescriber)** — see `specs/content/guide-prescriber.md`
* **CGU (Terms of Service)** — see `specs/content/cgu.md`
* All static pages are accessible unauthenticated (public)
