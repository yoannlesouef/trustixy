# UX

## Design Principles

* **One primary action per screen** — at any point, the user knows exactly what to do next
* **Value in under 3 minutes** — a new user must see a classification result before any paywall or mandatory setup
* **Progressive disclosure** — advanced features (history, alerts, audit trail, co-signature) appear after the user has used the core flow at least once
* **Never block, always guide** — warnings help; hard blockers frustrate. Only block when technically impossible, not to enforce best practices
* **Mobile-first** — all critical flows (classify, view result, check obligations, co-sign) must work on a phone

---

## Navigation

**End user app** (3 items max in nav):
- Dashboard (home)
- Systems (list)
- Alerts (badge with count when active)

**Partner portal** (3 items max in nav):
- Overview
- Clients
- Signature requests (badge with count)

Settings, history, audit trail, export → accessible via system/document page menus, not primary nav.

---

## Empty States (onboarding entry points)

### First login — no systems yet
Full-page empty state, two paths side by side:

**Path A — Quick assess (primary, recommended)**
> "Classify your first AI system in 3 minutes"
> [Single input: "Name your AI tool, e.g. 'Zendesk chatbot'"] → [Start →]
> *No account setup required. See your risk level immediately.*

**Path B — Discover all your systems**
> "Not sure which tools are in scope?"
> [Identify my systems →] (launches wizard)
> *Takes ~5 minutes. We'll analyze your tool stack.*

Both paths are available; Path A is visually dominant. The user chooses their own pace.

---

## Quick Classification Path (Path A — 3 minutes)

This is the fastest route to value. No wizard, no setup. Works from first login.

**Step 1 — Name the system**
Single field: "What AI tool do you want to assess?" with placeholder examples.
CTA: Start →

**Step 2 — 5 essential questions** (not 10)
Shown one at a time, with a progress bar:
1. What does this system do? (short description, 2–3 sentences)
2. Who is affected by its decisions? (users / employees / customers / general public)
3. Can a human override its output? (always / sometimes / never)
4. What type of data does it process? (checkboxes: personal data / financial / health / biometric / none)
5. Is it deployed publicly or internally? (public-facing / internal only)

"Want a more precise assessment? Add 5 more details →" (collapsed, optional — expands the 5 additional questions in place)

**Step 3 — Result screen** (immediate, no page load feel)
- Risk level badge (color-coded, large)
- 2-sentence plain-language explanation
- Top 3 obligations (expandable to full list)
- Confidence score shown subtly (not as the headline)

Primary CTA: **Generate compliance document →**
Secondary CTA: Save to registry

If unacceptable risk: full-screen alert with clear explanation before showing actions.

---

## Dashboard (End User)

**When the user has systems:**
- Single-line next action nudge at top: "Next: Classify [System Name] — your highest-risk unclassified system →"
- Alert banner (only shown when there are active alerts — not as a permanent fixture)
- Compact summary strip: X compliant · Y need attention · Z not assessed
- Systems table — 5 columns only:
  | Name | Risk level | Status | Next action | Last updated |
  Status badge: `Compliant` · `Needs attention` · `Not assessed`
  Next action: one contextual link ("Classify" / "Complete obligations" / "View alert" / "—")
- Add system button (secondary, not competing with the table)

**When the user has no systems:**
Show the empty state (see above).

**Wizard nudge** — shown once as a dismissable card (not permanent banner):
> "Want to find all your in-scope AI tools at once? The identification wizard takes 5 minutes. →"
> [Dismiss] — once dismissed, accessible from the "Add system" menu only

---

## System Page

**Above the fold — always visible:**
- System name (editable inline)
- Status badge (large, color-coded)
- One primary action button (context-aware):
  - If not classified: **Classify this system**
  - If classified, no document: **Generate compliance document**
  - If document exists, unsigned: **Request expert review** (co-signature)
  - If compliant: **Re-classify** (secondary)

**Description area:**
- Description text (editable inline)
- Quality indicator as a subtle helper text below the field: "Good description" (green dot) or "Could be more specific — [see suggestions]" (amber dot, suggestions in a tooltip)
- Never blocks classification. The user decides.

**Tabbed sections** (reduce cognitive load — one tab active at a time):

**Tab 1 — Classification** (default)
- Risk level + explanation
- Regulatory version (shown as date, e.g. "Assessed under EU AI Act — April 2026")
- Confidence score (shown as a small indicator, not headline)
- "How was this classified?" expandable — shows questionnaire answers

**Tab 2 — Obligations** (shown after first classification)
- Top 3 most critical obligations shown by default
- "Show all X obligations" toggle
- Each item: checkbox + title + "How to implement ▸" (expandable)
- Progress bar (X of Y complete)

**Tab 3 — Document** (shown after first document generated)
- Current document card with status (Unsigned / Pending review / Expert-reviewed)
- Generate / regenerate button
- Export PDF button
- Previous versions collapsed under "Version history"

**Tab 4 — History** (secondary — not shown until user has 2+ classifications)
- Timeline of classifications and documents
- Each entry: date, risk level, trigger, confidence

Active alerts are shown as a non-intrusive banner above the tabs (not a full tab):
> "⚠ Regulatory update may affect this system. [Re-classify now] or [View details]"

---

## Classification Questionnaire

**5 essential questions** (shown by default):
1. What does this system do? (description field — pre-filled if already entered)
2. Who is affected by its decisions?
3. Can a human override its output?
4. What type of data does it process?
5. Is it deployed publicly or internally?

**5 optional refinements** (collapsed under "Make my assessment more precise"):
6. What sector is this system used in?
7. How many people are affected monthly?
8. Is the system's decision-making process explainable?
9. Has the system been tested for bias or errors?
10. Is this system part of a regulated product or service?

Progress bar visible. Answers saved automatically — user can leave and return.
Result shown immediately after submitting the 5 essential questions; optional questions improve confidence score.

---

## Document Page

Clean, document-first layout. The compliance content is front-and-center.

**Header bar** (compact, 1 line):
Generated [date] · [Risk level] · [Regulatory version date] · [Status badge]

**Expert review block** — shown below the header:
- Unsigned: subtle inline prompt — "Strengthen this document with expert review. [Learn more ▸]" — not a warning, an invitation
- Pending: "Awaiting review by [Partner Firm] — sent [date]"
- Reviewed: Green badge — "[Name], [Firm] · Reviewed [date]" + "View reviewer comments ▸" (if notes exist)
- Changes requested: amber banner — "[Name] has feedback. [View feedback and update →]"

**Disclaimer** — shown as a clean footer block (always present, non-intrusive):
> *AI-assisted document · Does not constitute legal advice · [Full disclaimer ▸]*

**Export button** — always visible, top right.

---

## Expert Review Request Flow (End User)

Triggered by "Request expert review" from document page or system page.

1. If org has a linked prescriber → pre-filled modal:
   - "Send to [Partner Firm]?"
   - Optional message field
   - [Send request] → done in one click

2. If org has no linked prescriber → helpful modal:
   - "You don't have an expert partner yet."
   - Option A: "Find a certified partner →" (partner directory, filtered by sector)
   - Option B: "Download document with disclaimer →" (close the loop — user gets their PDF immediately with clear disclaimer)
   - No dead end.

---

## Identification Wizard (Path B — optional)

Accessible from: empty state Path B, dashboard "Add system" menu, "Discover my AI systems" nudge.

**2 visible steps** (Step 3 is instant, not a separate screen):

**Step 1 — Sector + tools (combined, one screen)**
- Sector selector at top (single-select, 8 options)
- Tool checklist filtered by sector (shown immediately on sector select, no page reload)
- Free-text field at bottom: "My tool isn't listed"
- CTA: "Analyze these tools →"

**Step 2 — Review and confirm (one screen)**
- Loading inline (~3s): "Checking against EU AI Act…"
- Results shown as cards: in-scope tools with risk level badge and editable description
- Out-of-scope tools in a collapsed "Not in scope (X)" section
- Each card has a toggle: include / exclude
- One confirm button: "Add X systems to my registry →"
- Descriptions are editable inline before confirming

Post-wizard: redirected to dashboard with systems listed. First suggested action: "Classify [highest-risk system] →"

---

## Prescriber Certification Flow

**Two levels — user chooses on entry:**

### Level 1 — Scope Acceptance (15 minutes)
For prescribers who want to start immediately.
- Read the Co-signature Scope Document (scrollable, no forced scroll-to-bottom)
- Single checkbox: "I confirm I have read and understood the co-signature scope"
- Accept → **Scope-accepted** badge on co-signed documents
- Unlocks co-signature for Minimal and Limited risk systems

### Level 2 — Full Certification (~90 minutes, reduced from 2h)
For prescribers who want the full badge and can sign High-risk systems.
- 4 focused modules (cut from 5, merged sector deep-dives into module 4):
  1. EU AI Act — risk levels and key obligations (25 min)
  2. How to review a Trustixy classification (20 min)
  3. Co-signature responsibilities and professional liability (20 min)
  4. Sector quick-guides — Healthcare, Finance, HR (25 min, user selects 1–2)
- 15-question assessment, 75% pass threshold (reduced from 80%)
- Immediate retry allowed (no 24h delay)
- **Certified** badge on all co-signed documents; can sign all risk levels

**Entry point UX — no full-screen blocker:**
- New partner portal landing: normal dashboard with a persistent (non-blocking) banner:
  > "Complete your certification to co-sign documents. Takes 15 min (basic) or 90 min (full). [Start →]"
- Partner can explore the portal, invite clients, set up white-label before certifying
- Signature queue is visible but shows a lock icon with tooltip: "Certification required to sign. [Start now →]"

**Renewal:**
- Reminder banner 60 days before expiry (email + in-app)
- Renewal = 20-min module (changes since last cert) + 5-question quiz
- No hard expiry wall — expired prescribers see a banner but are not locked out of the portal; only new co-signatures are blocked

---

## Prescriber Co-signature Review Page

Clean and fast — a prescriber reviewing 5 documents a week needs efficiency.

**Layout:** two-column on desktop (document left, review panel right), single column on mobile.

**Left — Document preview**
Scrollable document content. Risk level and confidence score shown prominently at top.

**Right — Review panel**
- System: name, sector, use case (3 lines max)
- Classification: risk level badge + 1-sentence justification
- Obligations: count + top 3 listed (expandable)
- Client message (if any)
- **Professional notes** (optional text area): placeholder — "Add caveats, sector context, or scope notes. Optional but recommended for High-risk systems."
- Three action buttons (clear hierarchy):
  - **[Approve & sign]** (primary, green)
  - **[Request changes]** (secondary)
  - **[Decline]** (tertiary, text link)

Approval: single-click confirmation modal — "Signing as [Name], [Firm]" — no redundant form fields.

---

## Partner Portal

**Home / Overview:**
- Certification status (top of page if not certified — banner, not blocker)
- 3 key numbers: Active clients · Pending signatures · This month's commission
- Signature queue (top 5, with "View all" link)
- Client list (compact: name, compliance health dot, last activity)

**Clients tab:**
- List with compliance health indicator per client
- Filter by: needs attention / all compliant / inactive
- Invite client button

**Signature Requests tab:**
- Queue, sorted by urgency
- Each request: client name, system name, risk level, received date
- Click to open review page

---

## Alerts Center

Accessible from the nav badge (only when there are active alerts). Otherwise hidden — don't prime users to expect noise.

- Simple list: system name, what changed, recommended action
- Bulk "Acknowledge all" button
- Dismissed alerts archived (accessible via "Show dismissed")

---

## Partner Onboarding

1. Apply form (name, org, type, tier preference) — one page
2. Approval email with portal access link
3. First login: normal portal with certification banner (not blocked)
4. Certification when ready (Level 1 in 15 min to start co-signing quickly)

---

## Mobile Experience

All critical flows work on mobile:
- Quick classification (5 questions, one at a time)
- View classification result and top obligations
- Update obligation status (checkbox)
- Review and co-sign a document (two-column → single column layout)
- View compliance alerts

Non-critical on mobile (desktop-recommended):
- Wizard (tool checklist is easier on desktop)
- Full document editing
- Compliance history export

---

## Static Pages

* **Home** — see `specs/content/home.md`
* **User Guide (End User)** — see `specs/content/guide-end-user.md`
* **User Guide (Prescriber)** — see `specs/content/guide-prescriber.md`
* **CGU (Terms of Service)** — see `specs/content/cgu.md`
* All static pages accessible unauthenticated
