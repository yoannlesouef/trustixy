# UX

## Design Principles

* **SDK first** — the web platform delivers zero value without instrumented data. The onboarding must get the user to their first SDK call in under 5 minutes.
* **One primary action per screen** — at any point, the user knows exactly what to do next
* **Progressive disclosure** — install SDK → see action log → explore replay → add compliance → request co-signature
* **Never block, always guide** — warnings help; hard blockers frustrate
* **Audience-aware navigation** — developers and security teams use a different primary view than compliance officers; the platform adapts based on role
* **Mobile-compatible** — critical flows (session list, anomaly alerts, obligation checkbox, co-sign) must work on a phone

---

## Navigation

**Developer / Security view** (default for SDK-connected orgs):
- Activity (home — session timeline)
- Agents (registry of discovered agent identities)
- Anomalies (badge with count when active)

**Compliance view** (accessible from top nav):
- Dashboard
- Systems
- Alerts (badge)

**Partner portal:**
- Overview
- Clients
- Signature requests (badge)

Settings, history, export, policy rules → accessible via page menus, not primary nav.

Switching between developer view and compliance view via a toggle in the top bar (not separate apps).

---

## Onboarding (New Organization)

### Step 1 — Choose your entry point

Full-page empty state, two paths:

**Path A — Install the SDK (primary, recommended)**
> "See what your AI agents are doing in 5 minutes"
> Shows framework selector: Anthropic · OpenAI · Vercel AI · LangChain · Claude Code · Other
> On select: shows exact code snippet (3 lines) + API key
> CTA: [Copy & continue →]

**Path B — Start with compliance (no SDK)**
> "Classify your first AI system for EU AI Act"
> [Classify a system →]

Both paths available; Path A is visually dominant. Path B launches the quick classification path.

---

## Developer View

### Activity (Session Timeline)

Home screen for developers and security teams.

**When sessions exist:**
- Header strip: total sessions today · total actions · active anomalies (linked to anomaly center)
- Session list — 5 columns:
  | Agent | Triggered by | Actions | Duration | Started |
- Click a session → session detail page
- Filter bar: agent · action type · time range · status · human identity
- Search: resource path or session metadata

**When no sessions yet (SDK not installed):**
Full-page empty state — SDK quick-start (Path A from onboarding, always accessible).

---

### Session Detail Page

**Header:**
- Session ID (truncated, copyable)
- Agent name · Framework badge · Environment badge
- Human identity (if captured)
- Duration · Total actions · Total tokens · Status badge

**Action timeline** (primary content):
- Ordered list of all actions, numbered by sequence
- Each row: `[N] type · resource · duration · status`
- Expandable inline: shows input/output summary (or full content if `captureContent=true`)
- Color-coded by type: LLM call (blue) · file write (orange) · file read (grey) · API call (purple) · shell (red) · error (red background)

**Sidebar (right):**
- Top affected resources (sorted by frequency)
- Token usage breakdown
- Anomaly badges (if any detected for this session)
- [Replay this session →] CTA (primary)
- [Export as PDF →] secondary CTA

---

### Session Replay

Clean, focused view. Not a raw log — a structured walkthrough.

**Layout:**
- Left: step navigator (numbered action list, click to jump)
- Center: current step detail (3 panels stacked):
  1. **Context** — what the agent had available at this point
  2. **Action** — what it executed (type, input summary, affected resources)
  3. **Outcome** — output summary, status, duration
- Right: metadata panel (tokens, model, timestamp)

**Controls:**
- ← Previous / Next → (keyboard: arrow keys)
- Jump to step N
- Filter: show only [file writes] [LLM calls] [errors]
- Compare mode: open a second session alongside for diff view

---

### Agents Page

List of all discovered agent identities.

**Table:**
| Name | Framework | Sessions | Last active | Status | Compliance |
- Status: `discovered` · `registered` · `archived`
- Compliance: linked system badge, or "Not registered" with [Register →] CTA

**Agent detail:**
- Usage stats: sessions over time chart, top action types, top affected resources
- Session list (filtered to this agent)
- Policy rules configured for this agent
- Linked compliance record (if registered)

---

### Anomaly Center

Accessible from nav badge (only when active anomalies exist).

- List: severity badge · type · agent · session · detected at · status
- Click → anomaly detail: description, evidence, linked session (with direct link to the triggering action)
- Bulk: acknowledge all · dismiss all
- Filter by severity / type / status

---

## Compliance View

### Dashboard

**Header strip:**
- X compliant · Y need attention · Z not assessed
- Active regulatory alerts (if any)

**Systems table:**
| Name | Risk level | Status | SDK source | Next action | Last updated |
- SDK source: green dot if linked to an agent identity; grey dot if manual
- Next action: one contextual link ("Classify" / "Complete obligations" / "View alert" / "—")

**Discover nudge** (shown until all discovered agents are registered):
> "[N] agent(s) detected via SDK but not registered for compliance. [Review →]"

---

### Agent-to-System Registration Flow

Triggered when user clicks "Register" on a discovered agent identity or responds to the dashboard nudge.

1. **Review detected behavior** — shows a summary card: framework, total sessions, top action types, sample affected resources
2. **Pre-filled system form** — name, description, autonomy level, data types, and deployment scope are pre-filled from SDK behavioral data. User can edit all fields.
3. **Confirm** — creates the `ai_system` record linked to the `agent_identity`
4. **Classify now** CTA — launches the quick classification path, pre-filled

---

### System Page

Same as before, with one addition: the **SDK Activity tab**.

**Above the fold:**
- System name (editable inline)
- Status badge (large, color-coded)
- SDK source indicator: "Live data from [agent-name]" or "Manual entry"
- One primary action button (context-aware)

**Tabbed sections:**

**Tab 1 — Classification** (default)
**Tab 2 — Obligations**
**Tab 3 — Document**
**Tab 4 — SDK Activity** *(new — only shown if linked to an agent identity)*
- Total sessions · Total actions · Last active
- Last 5 sessions list with link to session detail
- "Behavioral change detected" alert if the anomaly agent flagged a change since the last classification
**Tab 5 — History**

---

## Quick Classification Path (Compliance)

Identical to prior spec — 5 essential questions, optional 5 refinements, immediate result.

When launched from the agent-to-system registration flow: all pre-filled fields are presented for confirmation, not re-entry. The user reviews, adjusts if needed, and submits.

---

## Document Page

Same as prior spec with one addition:

**Evidence trail block** (shown if classification has a linked session):
> "This classification is backed by operational data from [N] sessions."
> [View evidence trail →] — links to the relevant sessions in the activity view

---

## Prescriber Certification Flow

Unchanged from prior spec:
- Level 1: 15-minute scope acceptance → unlocks Minimal/Limited co-signature
- Level 2: 90-minute full certification → unlocks all risk levels
- Non-blocking portal: full portal accessible before certification

---

## Prescriber Co-signature Review Page

Two-panel layout: document (left) + review panel (right).

Same as prior spec with one addition in the review panel:

**SDK Evidence section:**
- If the document has a linked session: "Backed by [N] sessions, [M] actions"
- Expandable: shows top affected resources and action type breakdown
- This context helps the prescriber understand the actual operational behavior of the system they're co-signing

---

## Partner Portal

Same as prior spec — unchanged.

---

## Alerts Center

Same as prior spec — unchanged.

---

## Static Pages

* **Home** — see `specs/content/home.md`
* **User Guide (End User)** — see `specs/content/guide-end-user.md`
* **User Guide (Prescriber)** — see `specs/content/guide-prescriber.md`
* **CGU (Terms of Service)** — see `specs/content/cgu.md`
