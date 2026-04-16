# Trustixy

**Every AI action, fully traced.**

Trustixy is an AI action audit platform. It captures every action taken by AI agents — LLM calls, tool executions, file writes, API calls, shell commands — and makes them searchable, replayable, and auditable. For developers who need to debug agent behavior, security teams who need to know what AI touched, and compliance teams who need EU AI Act evidence grounded in real operational data.

---

## The Problem

AI agents now write code, call APIs, modify files, and deploy infrastructure. But when something goes wrong — or when an auditor asks — you have no reliable answer to:

- *What exactly did the agent do between 14:00 and 14:32?*
- *Was this file changed by a human or an AI agent? Which one?*
- *Can you replay the sequence of actions that caused this regression?*
- *What's the blast radius if this agent session was compromised?*
- *Which AI systems in your org are in scope for the EU AI Act — and can you prove how they operate?*

Git shows commits. Logs show lines. Neither gives you a structured, agent-attributed, replayable record of what AI actually did.

---

## How It Works

### 1. Instrument once with the SDK

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

Supports: **Anthropic SDK · OpenAI · Vercel AI SDK · LangChain · LlamaIndex · CrewAI · Claude Code hooks · MCP**

### 2. Every action is captured

Each SDK call records: agent identity, session, action type, inputs, outputs, affected resources, token usage, duration, timestamp. Immutable. Structured. Searchable.

### 3. Three audiences, one data source

| Audience | What they get |
|---|---|
| **Developer** | Timeline of every agent action · Session replay · Debug why an agent did what it did |
| **Security team** | Attribution (human vs agent) on every resource change · Anomaly detection · Blast radius analysis |
| **Compliance / Legal** | EU AI Act registry auto-populated from SDK data · Compliance documents backed by real operational evidence · Prescriber co-signature |

---

## What Trustixy Provides

| Capability | What it means |
|---|---|
| **Developer SDK** | npm + pip libraries that wrap any AI framework — one line, zero behavior change |
| **Action audit log** | Every LLM call, tool execution, file write, API call — structured, immutable, queryable |
| **Session replay** | Step through any agent session: context seen → decision made → action taken → outcome |
| **Agent registry** | Auto-discovered from SDK data — no manual entry. Every AI system, how it's used, who triggered it |
| **Anomaly detection** | Flags unusual agent behavior: unexpected file access, scope violations, volume spikes |
| **EU AI Act compliance** | Registry + risk classification + obligations checklist, pre-filled from SDK operational data |
| **Prescriber co-signature** | Compliance documents backed by real action logs, co-signed by certified experts |
| **Regulatory alerts** | Automatic re-assessment when EU AI Act updates affect registered systems |

---

## SDK Integration

```typescript
// Anthropic SDK
const client = trustixy.wrap(new Anthropic(), { project: "...", agent: "..." });

// OpenAI
const openai = trustixy.wrap(new OpenAI(), { project: "...", agent: "..." });

// Vercel AI SDK
import { wrapAI } from "@trustixy/sdk/vercel";
const result = await wrapAI(generateText, { project: "...", agent: "..." })({ ... });

// LangChain
import { TrustixyCB } from "@trustixy/sdk/langchain";
chain.invoke(input, { callbacks: [new TrustixyCB({ project: "...", agent: "..." })] });
```

```python
# Python — LangChain, CrewAI, LlamaIndex
from trustixy import wrap
client = wrap(anthropic.Anthropic(), project="...", agent="...")
```

```jsonc
// Claude Code hook — instruments every Claude Code session automatically
// .claude/settings.json
{
  "hooks": {
    "PreToolUse":  [{ "command": "trustixy hook pre  --project my-app" }],
    "PostToolUse": [{ "command": "trustixy hook post --project my-app" }]
  }
}
```

Full SDK reference: [`/specs/sdk.md`](specs/sdk.md)

---

## Compliance Layer

The compliance layer runs on top of the action data the SDK collects. AI systems are auto-discovered — no manual registry entry. Classifications are pre-filled from observed behavior. Every compliance document is backed by a real operational evidence trail, not a self-declared form.

Prescribers (accounting firms, law firms, IT integrators) review and co-sign compliance documents, turning AI-generated output into professionally endorsed deliverables. The prescriber model is unchanged — and now stronger because the documents are grounded in real data.

Full compliance spec: [`/specs/product.md`](specs/product.md)  
Prescriber model: [`/specs/prescriber-partnership.md`](specs/prescriber-partnership.md)

---

## Go-to-Market

**Developer adoption (bottom-up):** Free SDK tier drives instrumentation across engineering teams. Every `npm install @trustixy/sdk` is a distribution event.

**Security / Platform teams (expansion):** Once the SDK is in, security teams see the value in the audit timeline and anomaly detection. Upgrade to Pro or Enterprise.

**Compliance teams (top-down):** Compliance officers use the web platform to run EU AI Act assessments. The registry is already populated from SDK data — no cold start.

**Prescriber channel (B2B2B):** Prescribers distribute Trustixy to SME clients and co-sign compliance documents. Three models: per-act co-signature, federation license, integrator license.

### Three prescriber revenue models

| Model | For | Trustixy cost | Prescriber revenue |
|---|---|---|---|
| **Per-act co-signature** | Accounting firms, consultants | €25 commission/act | Prescriber sets own fee (€150–500); direct Stripe payout |
| **Federation license** | Professional federations, trade associations | €3 000–10 000/year | Distribute Pro access to all members under one contract |
| **Integrator license** | IT integrators, ESN/SSII | €299–599/month | Bundle compliance into project deliverables at their own price |

---

## Monetization

**SDK (developer tier):**
- **Free:** 30-day log retention, 1 project, 1M actions/month
- **Pro:** 1-year retention, unlimited projects, replay, anomaly detection, €49/month
- **Enterprise:** unlimited retention, SSO, SIEM export, SLA, API access

**Platform (compliance tier):**
- **Free:** up to 3 AI systems, unsigned compliance documents with disclaimer
- **Pro:** unlimited systems, full regulatory alert history, co-signature requests, obligation tracking
- **Enterprise:** org compliance report, API access, advanced audit export, SLA

---

## Roadmap

| Phase | Weeks | Key deliverables |
|---|---|---|
| SDK & Audit Core | 1–4 | npm + pip SDK, ingest API, action log, session timeline, replay |
| Compliance Layer | 5–9 | Auto-discovered registry, EU AI Act classification, obligations, documents, regulatory alerts |
| Prescriber & Partners | 10–14 | Co-signature flow, prescriber certification, per-act billing, federation + integrator models |
| AI Agents & Scale | 15+ | Anomaly detection agent, SIEM export, marketplace listings, multi-regulation expansion |

Full details: [`/tasks/roadmap.md`](tasks/roadmap.md)

---

## Project Structure

```
trustixy/
  /specs          → Product & technical specifications
  /tasks          → Development roadmap and sprints
  /app            → Next.js app (web platform)
  /packages
    /sdk-js       → @trustixy/sdk (npm)
    /sdk-python   → trustixy (pip)
  /components     → UI components
  /lib            → API & utilities
```

---

## Documentation

| Document | Audience | Location |
|---|---|---|
| Product definition | All | `specs/product.md` |
| SDK reference | Engineering | `specs/sdk.md` |
| Features spec | All | `specs/features.md` |
| Data model | Engineering | `specs/data-model.md` |
| API spec | Engineering | `specs/api.md` |
| UX spec | Design / Engineering | `specs/ux.md` |
| LLM prompts | Engineering | `specs/llm-prompts.md` |
| AI agents | Engineering | `specs/ai-agents.md` |
| Prescriber partnership | Business | `specs/prescriber-partnership.md` |

---

## Contact

For early access or partnerships: [yoann.lesouef@gmail.com](mailto:yoann.lesouef@gmail.com)

---

**Trustixy — Every AI action, fully traced.**
