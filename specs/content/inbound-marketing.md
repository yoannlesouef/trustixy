# Inbound Marketing Plan

## Goal

Generate qualified signups at zero paid acquisition cost for the first 6 months. Target: 500 free signups → 60 paying customers by month 6.

Two audiences, two funnels:
- **Developers / tech teams** → SDK install → free plan → Pro upgrade
- **Prescribers (accountants, lawyers, IT integrators)** → partner page → application → client referrals

---

## Phase 1 — Pre-launch (weeks -6 to 0)

Goal: build an audience before the product ships. Arrive at launch with 200+ people waiting.

### Waitlist

Landing page at `trustixy.com` before full launch. Single CTA: "Get early access".

Collect: email, role (developer / compliance / prescriber), organization size.

Offer: 3 months Pro free for the first 100 signups. Creates urgency without discounting permanently.

### EU AI Act deadline content

Publish 2 pillar articles before launch (see SEO plan). These rank organically and capture search traffic that converts to waitlist.

Priority articles:
1. "EU AI Act deadlines: complete timeline for 2025–2027" — targets recurring search traffic as deadlines approach
2. "EU AI Act risk classification: which category is your AI system?" — direct product entry point

### LinkedIn founder posts (personal account)

Start posting 2–3x/week 6 weeks before launch. Build an audience around the EU AI Act problem, not the product. See social network strategy (`specs/content/social.md`).

### Prescriber pre-outreach

Identify 20 accounting firms and IT integrators personally. Direct outreach via LinkedIn — not a mass campaign. Goal: 5 committed prescriber partners ready to sign up on launch day.

Template message:
> "Hi [name] — I'm building a tool that lets your clients document their AI systems for the EU AI Act in 15 minutes, with your co-signature adding professional validity. Looking for 5 accounting firms to try it first. Would a 15-minute call make sense?"

---

## Phase 2 — Launch (week 0)

### Launch sequence

**Day -1:**
- Email waitlist: "Trustixy launches tomorrow. Your early access is ready."
- Post on LinkedIn (personal): "We're launching tomorrow..."

**Day 0 (Tuesday — best day for HN and Product Hunt):**
- 8:00 AM: "Show HN: Trustixy – one-line audit trail for AI agents, with EU AI Act compliance built on top"
- 9:00 AM: LinkedIn post (personal + company page)
- 9:00 AM: Post in relevant communities (see social strategy)
- Send welcome emails to all waitlist signups

**Show HN post structure:**
```
Show HN: Trustixy – one-line audit trail for AI agents

I built Trustixy because I couldn't answer a simple question after a 
bad agent session: what exactly did it do?

npm install @trustixy/sdk
const client = trustixy.wrap(new Anthropic(), { project, agent });
// Every LLM call, file write, API call — traced automatically

The platform turns that log into EU AI Act compliance evidence: 
5-question risk classification (pre-filled from what the SDK observed), 
obligations checklist, and a compliance document you can get 
professionally co-signed.

Free for 3 agents. https://trustixy.com
```

**Day 1–7:**
- Respond to every HN comment personally
- Share any press or community reactions on LinkedIn
- Email nurture sequence fires for new signups

---

## Lead Magnets

### 1. EU AI Act Obligations Checklist (primary)

**Format:** PDF download, gated behind email  
**URL:** `trustixy.com/checklist`  
**Content:** Complete obligations by risk level (high risk + limited risk), with implementation guidance, regulatory references, and a completion tracker

**Why it converts:** Compliance teams need this anyway. It's immediately useful without requiring a product signup. The email captured feeds into the nurture sequence.

**Promotion:** CTA at the bottom of every blog post, LinkedIn posts linking to it, SEO for "EU AI Act compliance checklist"

### 2. "Is your AI system in scope?" self-assessment (secondary)

**Format:** 5-question interactive quiz on the website (no gate — instant result)  
**URL:** `trustixy.com/scope-check`  
**Content:** Mirrors the Trustixy classification questionnaire. Result shows risk level + "See your full obligations in Trustixy →"

**Why it converts:** Gives value instantly. Creates a natural handoff to the product. No friction — no email required to get the result, but email is offered to save the result.

---

## Content Calendar (months 1–6)

Publish 2 articles/month minimum. Priority order based on search volume and conversion proximity.

### Month 1
- "EU AI Act deadlines: complete timeline" (pillar — drives recurring traffic)
- "EU AI Act risk classification guide: minimal, limited, high, unacceptable" (pillar — drives trial)

### Month 2
- "What counts as a high-risk AI system under the EU AI Act?" (SEO — mid funnel)
- "How to document an AI system for EU AI Act compliance" (conversion — bottom funnel)

### Month 3
- "EU AI Act for SMEs: what you actually need to do" (prescriber audience)
- "Instrumenting your Anthropic SDK calls for production observability" (developer audience — links to npm)

### Month 4
- "EU AI Act for accounting firms: advising clients on AI compliance" (prescriber acquisition)
- "OpenAI SDK monitoring: what to log and why" (developer SEO)

### Month 5
- "EU AI Act penalties: what fines can you face?" (high-intent, urgency)
- "How to audit AI agent actions: a practical guide" (developer, brand awareness)

### Month 6
- Customer story / case study (if a customer agrees — most powerful conversion asset)
- "EU AI Act 6-month update: what changed and what it means for your AI systems"

---

## Nurture Funnel

### Funnel: blog reader → free signup → Pro upgrade

```
Blog post / SEO
  ↓
Lead magnet CTA ("Download the EU AI Act checklist →")
  ↓
Email capture → checklist delivered immediately
  ↓
Email 1 (day 0): checklist + "See if your AI agents are in scope: [Start free →]"
Email 2 (day 3): "Most teams are surprised which systems qualify as high-risk. Here's why."
Email 3 (day 7): "How [company type] is using Trustixy to prepare for EU AI Act audits."
  ↓
Free signup
  ↓
Activation sequence (see specs/emails.md)
  ↓
Upgrade nudge at day 14
```

### Funnel: prescriber prospect → partner

```
LinkedIn post / SEO / direct outreach
  ↓
Partner landing page (trustixy.com/partners)
  ↓
Partner application
  ↓
Approval email → portal access
  ↓
Onboarding sequence (see specs/emails.md)
  ↓
First client invite
```

---

## Distribution Partnerships

Target for months 2–4. Each partnership is a one-time effort with recurring traffic.

| Partner type | What to offer | What you get |
|---|---|---|
| EU AI Act newsletter (e.g. Future of Life, AI Act newsletter) | Guest post or tool feature | Access to their subscriber list |
| Accounting body (CSOEC, IFAC) | Free tool for members | Prescriber referrals at scale |
| Developer newsletter (TLDR, Bytes.dev) | Sponsored or organic mention | SDK installs |
| AI builder communities (Latent Space, AI Engineers) | Tutorial or talk | Developer signups |
| French tech media (Maddyness, Frenchweb) | Story pitch — EU AI Act + French startup | French market awareness |

---

## Metrics

| Metric | Month 1 | Month 3 | Month 6 |
|---|---|---|---|
| Waitlist / lead magnet emails | 200 | 500 | 1,000 |
| Free signups | 50 | 150 | 400 |
| Blog organic sessions/month | 200 | 800 | 2,000 |
| Partner applications | 3 | 8 | 20 |
| Email → free signup conversion | — | 5% | 8% |
| Free → Pro conversion rate | — | 10% | 15% |

---

## Budget

Zero paid acquisition in v1. Every euro goes into product, not ads.

Exception: €500–1,000 for a sponsored slot in one developer newsletter (TLDR or Bytes.dev) at month 3 if organic growth is below target. One-time test, not a recurring channel.
