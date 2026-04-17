# Onboarding Journey

One path. SDK first. Full value in under 15 minutes.

---

## The Single Path

**Who enters:** Anyone — developer, founder, compliance officer. The SDK is the entry point regardless of role.

**Time to first value:** Under 5 minutes (first session visible).  
**Time to full value:** Under 15 minutes (classified + document generated).

### Step-by-step

1. Sign up → land on SDK quickstart screen
2. Select framework → copy 3-line snippet with pre-filled API key
3. Add snippet to existing AI project → run one agent call
4. First session appears in Activity
   - *Aha moment 1: "I can see exactly what my agent did"*
5. Banner: *"[agent-name] detected. Classify it for EU AI Act compliance? [Classify →]"*
6. Classification screen: 5 questions pre-filled from SDK behavioral data, editable → risk level + obligations checklist rendered immediately
   - *Aha moment 2: "I know my EU AI Act exposure in under 10 minutes"*
7. Tick obligations, generate document → one click
8. Optional: request prescriber co-signature

**Prescriber-referred entry:** Client receives an invitation email from their prescriber. Clicking the link creates their account with the prescriber pre-linked. They land on the SDK quickstart — same path from step 2, prescriber is already connected in the background. Co-signature request in step 8 defaults to that prescriber with no directory search needed.

---

## SDK Parameters

Each SDK call includes three identifiers:

| Parameter | Type | Description |
|---|---|---|
| `apiKey` | string | Identifies the organization |
| `agentId` | string | Developer-assigned agent name (e.g. `"customer-support-bot"`) |
| `environment` | string | `"production"` (default) \| `"staging"` \| `"development"` |

Only `production` sessions feed the compliance layer and document evidence trail. All environments are visible in Activity.

---

## Adding More Agents

As developers instrument additional agents, new agents appear automatically in the Agents page. A dashboard nudge prompts classification:

> *"[N] agent(s) detected. Classify for EU AI Act compliance? [Classify →]"*

Each agent follows the same single-screen flow: classify (5 pre-filled questions) → obligations checklist. No batch flow — classify agents one at a time as they become production-ready.

---

## Team Access

One role in v1: **Admin**. Every team member invited to the organization sees everything and can perform all actions — sessions, classification, documents, co-signature requests.

Invite flow: Settings → Team → [Invite member →] → email → member joins with full access.

External prescribers have their own portal and are not added as team members.

---

## Time-to-Value

| Milestone | Time from signup |
|---|---|
| First session visible in Activity | ~5 min |
| Agent classified + obligations list | ~10 min |
| Compliance document generated | ~12 min |
| Co-signature requested | ~13 min |
| Co-signed document received | Hours (prescriber queue dependent) |
