# Trustixy

**Every AI action, fully traced.**

Trustixy is an AI action audit platform. Install the SDK once — it captures every action your AI agents take (LLM calls, file writes, API calls, shell commands) and turns that operational record into a searchable audit log and defensible EU AI Act compliance evidence.

---

## The Problem

AI agents now write code, call APIs, modify files, and deploy infrastructure. When something goes wrong — or when an auditor asks — you have no structured answer to:

- *What exactly did the agent do, and in what order?*
- *Was this file changed by a human or an AI agent? Which one?*
- *Which AI systems in our org are in scope for the EU AI Act — and can we prove how they operate?*

Git shows commits. Logs show lines. Neither gives you an agent-attributed, structured record of what AI actually did.

---

## How It Works

### 1. Instrument in one line

```typescript
// npm install @trustixy/sdk
import Anthropic from "@anthropic-ai/sdk";
import { trustixy } from "@trustixy/sdk";

const client = trustixy.wrap(new Anthropic(), {
  project: "my-backend",
  agent: "code-assistant"
});

// Identical API — every action traced automatically
const response = await client.messages.create({ ... });
```

```python
# pip install trustixy
from trustixy import wrap
client = wrap(anthropic.Anthropic(), project="my-backend", agent="code-assistant")
```

**v1:** Anthropic · OpenAI · Claude Code hooks — **v2 planned:** Vercel AI SDK · LangChain · LlamaIndex · CrewAI · MCP

### 2. Every action is captured automatically

Agent · session · action type · affected resources · token usage · duration · timestamp. Immutable. Structured. Searchable.

### 3. See what your agents do — then prove it

| What you get | Value |
|---|---|
| **Activity timeline** | Every agent action, every session, filterable and searchable |
| **Sensitive file alerts** | Instant notification when an agent accesses `.env`, secrets, or credentials |
| **Agent registry** | Auto-discovered from SDK data — no manual entry |
| **EU AI Act classification** | 5 questions, pre-filled from observed behavior, confidence-scored |
| **Obligations checklist** | Actionable per-agent to-do list; completion upgrades compliance status |
| **Compliance document** | AI-generated, backed by real operational evidence, disclaimer-embedded |
| **Prescriber co-signature** | Certified expert reviews and endorses your document |

---

## From First Session to Co-signed Document in 15 Minutes

```
Install SDK (3-line snippet)
  → Run agent → first session appears in Activity (~5 min)
    → Banner: "Classify [agent] for EU AI Act? →"
      → 5 questions, pre-filled → risk level + obligations
        → Tick obligations as you implement them
          → Generate document (one click)
            → Request co-signature → certified expert signs
```

---

## Prescriber Channel (B2B2B)

Trustixy is distributed through prescriber partners — accounting firms, law firms, IT integrators — who introduce Trustixy to their clients and co-sign compliance documents.

| Model | For | Trustixy cost | Partner revenue |
|---|---|---|---|
| **Per-act co-signature** | Accounting firms, consultants | €25 commission/act | Partner sets own fee (€150–500); direct Stripe payout |
| **Integrator license** | IT integrators, ESN/SSII | €399/month | Bundle compliance into project deliverables |

---

## Pricing

One plan covers both SDK visibility and compliance — no split billing.

| Plan | Price | What's included |
|---|---|---|
| **Free** | €0 | 3 agents · 30-day log · unsigned documents |
| **Pro** | €99/month | Unlimited agents · 1-year log · co-signature requests · full obligation tracking |
| **Enterprise** | Custom | Unlimited retention · SSO · JSON export · SLA · API |

---

## Specs

| Document | Location |
|---|---|
| Product definition | `specs/product.md` |
| SDK reference | `specs/sdk.md` |
| Features | `specs/features.md` |
| Data model | `specs/data-model.md` |
| API | `specs/api.md` |
| UX | `specs/ux.md` |
| Onboarding | `specs/onboarding.md` |
| LLM prompts | `specs/llm-prompts.md` |
| Internal agents | `specs/ai-agents.md` |
| Prescriber partnership | `specs/prescriber-partnership.md` |

---

## Contact

Early access or partnerships: [yoann.lesouef@gmail.com](mailto:yoann.lesouef@gmail.com)
