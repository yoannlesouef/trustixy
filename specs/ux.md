# UX

## Design Principles

* **SDK first** — the platform delivers zero value without instrumented data. Onboarding must produce the first session in under 5 minutes.
* **One primary action per screen** — at any point, the user knows exactly what to do next
* **Linear workflow, not parallel tabs** — the path is always: activity → classify → obligations → document → co-signature. The UI reflects that order.
* **Never block, always guide** — warnings help; hard blockers frustrate
* **Mobile-readable** — session list and alerts must be readable on a phone; full input is desktop-first

---

## Navigation

Three items. No role toggle. No view switching.

- **Activity** — session timeline (home)
- **Agents** — registry of discovered agent identities + compliance status
- **Alerts** — badge when sensitive file detections are active

Settings, billing, API keys, team members → accessible via Settings page, not primary nav.

---

## Onboarding (New Organization)

### Step 1 — SDK quickstart

Full-page empty state on first login.

> "See what your AI agents are doing in 5 minutes"

Framework selector: Anthropic · OpenAI · Vercel AI · LangChain · Claude Code · Other

On select: shows 3-line code snippet with pre-filled API key.

```typescript
const client = trustixy.wrap(new Anthropic(), {
  project: "my-app",
  agent: "code-assistant"   // ← your agent name
});
```

CTA: **[Copy snippet →]**

No alternative path at this screen. Users who arrive via prescriber invitation skip to step 2 with their prescriber pre-linked in the background.

### Step 2 — First session appears

After the developer runs one agent call, the session appears in Activity automatically.

Banner fires once:
> *"Agent [name] detected. Register it for EU AI Act compliance? [Review →]"*

Clicking the banner starts the registration flow inline — no page change.

### Step 3 — Classify (one screen)

Single screen, all pre-filled from SDK behavioral data, all editable:

1. What does this agent do? *(pre-filled from observed actions)*
2. Who is affected by its outputs?
3. Can a human override its decisions?
4. What types of data does it process?
5. Is it deployed in a regulated sector?

Submit → risk level + obligations checklist rendered immediately on the same page.

*Aha moment: "I know my EU AI Act exposure in under 10 minutes."*

### Step 4 — Obligations checklist

Rendered directly below the classification result. No navigation required.

Top 3 obligations shown by default. Each has a **done checkbox** + implementation guide.

CTA: **[Generate compliance document →]** (appears once ≥1 obligation is checked)

### Step 5 — Document + co-signature

One-click document generation. The document page has two actions:
- **[Download PDF]** — with disclaimer, unsigned
- **[Request co-signature →]** — launches co-signature request flow

No further navigation required to complete the full value loop.

---

## Activity (Session Timeline)

Home screen. Default view on every login after onboarding.

**When sessions exist:**
- Header strip: sessions today · actions today · active alerts (linked)
- Session list:

  | Agent | Triggered by | Actions | Duration | Started |

- Click a session → session detail page
- Filter bar: agent · action type · time range · status
- Search: resource path or session metadata

**When no sessions yet:**
Full-page SDK quickstart (always accessible, same as onboarding step 1).

---

## Session Detail Page

**Header:**
- Agent name · Framework badge · Environment badge
- Human identity (if captured)
- Duration · Total actions · Total tokens · Status badge

**Action timeline:**
- Ordered list of all actions, numbered by sequence
- Each row: `[N] type · resource · duration · status`
- Expandable inline: input/output summary (or full content if `captureContent=true`)
- Color-coded: LLM call (blue) · file write (orange) · file read (grey) · API call (purple) · shell (red) · error (red background)

**Sidebar (right):**
- Top affected resources
- Token usage breakdown
- Sensitive file alert badges (if any)
- [Export as PDF →]

---

## Agents Page

| Name | Framework | Sessions | Last active | Compliance |
- Compliance: risk level badge + obligation progress, or `Unclassified` with [Classify →] CTA inline

Click an agent → Agent page (see below).

---

## Agent Page

Single scrollable page. Replaces the tabbed system page. Sections always appear in this order:

### Recent Activity
- Total sessions · Total actions · Last active
- Last 5 sessions with link to session detail
- [View all sessions →] link to Activity filtered to this agent

### Classification
- Current risk level badge + confidence score
- 5-question form (pre-filled, editable)
- [Reclassify →] if already classified
- Low-confidence warning (<70%): *"Consider requesting expert review"* — not a blocker

### Obligations
- Checklist of obligations for the current classification
- Each item: **done checkbox** · title · implementation guide · regulatory reference
- Completion count: X of Y done
- All checked → status upgrades to `Compliant`

### Document
- Current document status: unsigned · pending co-signature · co-signed
- Evidence note if SDK data is linked: *"Backed by [N] sessions"*
- [Generate document →] or [Regenerate →]
- [Download PDF] · [Request co-signature →]
- Previous versions accessible via "Show history" toggle

---

## Alerts Page

Accessible from nav badge (only when active alerts exist).

- List: agent · file matched · session · detected at · status
- Click → alert detail: file path, linked session, direct link to the triggering action
- Bulk: acknowledge all · dismiss all

---

## Prescriber Co-signature Review Page

Two-panel layout: document (left) + review panel (right).

**Review panel:**
- Classification summary: risk level, confidence, obligations count
- SDK evidence (if linked): "Backed by [N] sessions, [M] actions" · expandable resource breakdown
- Actions: **[Approve & sign]** · [Request changes] · [Decline]
- Optional professional notes field

On approval: prescriber name, firm, date embedded in document. Co-signed badge appears on document.

---

## Partner Portal

Separate surface for external prescribers only.

- **Overview** — active clients, pending signature requests
- **Clients** — list of linked client organizations with compliance health
- **Signature requests** — queue of documents awaiting review (badge in nav)

Scope acceptance is completed during partner signup — no separate certification page. Portal is fully accessible immediately after approval.
