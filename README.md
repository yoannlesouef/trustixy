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

## Pricing

For organizations using the platform. One plan covers both SDK visibility and compliance — no split billing.

| Plan | Price | Agents | Actions | Retention |
|---|---|---|---|---|
| **Free** | €0 | 3 | 50k/month | 30 days |
| **Pro** | €99/month | Unlimited | 1M/month | 1 year |
| **Enterprise** | Custom | Unlimited | Unlimited | Unlimited |

One plan covers both SDK visibility and compliance — no split billing. Pro includes co-signature requests and full obligation tracking.

---

## Prescriber Channel (B2B2B)

A separate model for professional firms (accounting, legal, IT integrators) who distribute Trustixy to their clients and co-sign compliance documents. Partners have their own portal and pricing — independent from the organization plans above.

| Partner model | Trustixy fee | Partner revenue |
|---|---|---|
| **Per-act co-signature** | €25/act | Partner sets own fee (€150–500); paid via Stripe Connect |
| **Integrator license** | €399/month flat | Bundle compliance into client project deliverables |

---

## Technical Stack

Next.js 14 · Supabase (Postgres + Auth + Storage) · Vercel Pro · Anthropic Claude API · Stripe · Resend

---

## Specs

### Product

| Document | Location |
|---|---|
| Product definition | `specs/product.md` |
| Features | `specs/features.md` |
| UX | `specs/ux.md` |
| Onboarding | `specs/onboarding.md` |
| Prescriber partnership | `specs/prescriber-partnership.md` |
| Feedback & community | `specs/feedback-community.md` |
| Branding | `specs/branding.md` |

### Technical

| Document | Location |
|---|---|
| Infrastructure & project structure | `specs/infra.md` |
| Data model | `specs/data-model.md` |
| API | `specs/api.md` |
| Auth | `specs/auth.md` |
| Row Level Security | `specs/rls.md` |
| SDK reference | `specs/sdk.md` |
| LLM prompts | `specs/llm-prompts.md` |
| Internal agents | `specs/ai-agents.md` |
| Stripe integration | `specs/stripe.md` |
| PDF generation | `specs/pdf.md` |
| Error handling | `specs/errors.md` |
| Email sequences | `specs/emails.md` |

### Legal & Operations

| Document | Location |
|---|---|
| Privacy & GDPR | `specs/privacy.md` |
| Launch checklist | `specs/launch-checklist.md` |
| CGU (Terms of Use) | `specs/content/cgu.md` |

### Content & Guides

| Document | Location |
|---|---|
| Home page | `specs/content/home.md` |
| End-user guide | `specs/content/guide-end-user.md` |
| Prescriber guide | `specs/content/guide-prescriber.md` |
| Admin guide | `specs/content/guide-admin.md` |
| Developer docs | `specs/content/developer-docs.md` |

### Marketing *(not engineering specs)*

| Document | Location |
|---|---|
| SEO strategy | `marketing/seo.md` |
| Inbound marketing plan | `marketing/inbound-marketing.md` |
| Social network strategy | `marketing/social.md` |
| Partner landing page | `marketing/partner-landing.md` |

---

## Contact

Early access or partnerships: [yoann.lesouef@gmail.com](mailto:yoann.lesouef@gmail.com)
