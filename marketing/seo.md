# SEO & Content Strategy

Goal: rank for EU AI Act compliance searches and developer AI observability searches. Both audiences are high-intent and currently underserved by quality content.

---

## Target Audiences

**Audience 1 — Compliance decision-makers**
Searching for: what do I need to do to comply with the EU AI Act?
Intent: understand obligations, find a tool or consultant
Converts to: Pro plan or prescriber referral

**Audience 2 — Developers**
Searching for: how do I monitor / audit my AI agents?
Intent: find an instrumentation library
Converts to: SDK install → Free plan → Pro upgrade

**Audience 3 — Prescribers (accounting/legal/IT)**
Searching for: EU AI Act advisory service for clients / AI compliance tool for accountants
Intent: find a tool to offer clients
Converts to: partner application

---

## Primary Keywords

### High-intent, low competition (target immediately)

| Keyword | Monthly searches (est.) | Intent |
|---|---|---|
| EU AI Act risk classification tool | 200–500 | Direct product match |
| EU AI Act compliance checklist | 1,000–3,000 | Top of funnel |
| EU AI Act limited risk obligations | 300–800 | Mid funnel |
| EU AI Act high risk system requirements | 500–1,500 | Mid funnel |
| how to classify AI system EU AI Act | 200–500 | Mid funnel |
| AI agent audit log | 100–300 | Developer, direct match |
| instrument Anthropic SDK observability | 50–150 | Developer, direct match |
| EU AI Act accounting firms | 100–300 | Prescriber |

### Competitive but worth targeting (longer term)

| Keyword | Monthly searches (est.) | Intent |
|---|---|---|
| EU AI Act compliance software | 1,000–3,000 | High intent, competitive |
| EU AI Act tool | 2,000–5,000 | Broad, competitive |
| AI compliance management | 500–2,000 | Broad |

---

## Content Plan

### Pillar pages (evergreen, SEO-first)

**1. EU AI Act Risk Classification Guide**
URL: `/blog/eu-ai-act-risk-classification`
Target keyword: "EU AI Act risk classification"
Content: Complete guide to the 4 risk levels, with examples, decision tree, and obligations per level. Links to classification tool CTA.
Length: 2,000–3,000 words
CTA: "Classify your AI system in 3 minutes →"

---

**2. EU AI Act Obligations Checklist**
URL: `/blog/eu-ai-act-obligations-checklist`
Target keyword: "EU AI Act compliance checklist"
Content: Downloadable checklist (gated behind email) + ungated HTML version. Covers high-risk and limited-risk obligations with implementation guidance.
CTA: "Track obligations automatically with Trustixy →"

---

**3. EU AI Act for SMEs: What You Actually Need to Do**
URL: `/blog/eu-ai-act-sme-guide`
Target keyword: "EU AI Act SME"
Content: Plain-language guide for small businesses. Which AI tools are in scope, what the penalties are, minimum viable compliance approach.
CTA: "See if your AI tools are in scope →"

---

**4. How to Audit AI Agent Actions (Developer Guide)**
URL: `/blog/ai-agent-audit-log`
Target keyword: "AI agent audit log"
Content: Technical guide to instrumenting Anthropic/OpenAI SDK calls for observability. Includes Trustixy SDK code examples.
CTA: "npm install @trustixy/sdk"

---

**5. EU AI Act Deadlines: Complete Timeline**
URL: `/blog/eu-ai-act-deadlines`
Target keyword: "EU AI Act timeline"
Content: All enforcement dates, by obligation category. Updated as regulation evolves. High recurring traffic potential.
CTA: Newsletter signup for regulatory updates + "Check your compliance status →"

---

### Supporting articles (drive topical authority)

- "EU AI Act: difference between high-risk and limited-risk systems"
- "What counts as a 'high-risk AI system' under the EU AI Act?"
- "EU AI Act penalties: what fines can you face?"
- "How to document an AI system for EU AI Act compliance"
- "EU AI Act for accounting firms: advising clients on AI compliance"
- "Instrumenting Claude / Anthropic SDK for production observability"
- "OpenAI SDK monitoring: what to capture and why"

---

## On-Page SEO Requirements

All blog posts must include:

- H1 matching target keyword exactly
- Meta description under 160 characters with keyword + benefit
- Table of contents for posts over 1,500 words
- Internal links to product pages and related posts
- Schema markup: Article + FAQ where applicable
- CTA block at top, middle, and bottom of post
- Last-updated date displayed (important for regulatory content)

---

## Landing Pages (non-blog)

### /eu-ai-act
Dedicated EU AI Act hub page. Explains the regulation, links to all content, CTAs to classify and to become a partner. Primary organic entry point for compliance searchers.

### /sdk
Developer-facing page. SDK install instructions, framework support table, "what gets captured" table. Targets "AI SDK observability" and "AI agent monitoring" searches.

### /partners
Prescriber acquisition page (see `specs/content/partner-landing.md`). Targets "EU AI Act for accountants" and "AI compliance advisory" searches.

---

## Distribution

**npm package page (`@trustixy/sdk`):**
README is the primary discovery surface for developers. Must contain: one-line install, 3-line code example, link to docs, link to trustixy.com. Updated with every release.

**GitHub:**
Public repo for SDK. Stars drive organic developer discovery. Issues and PRs signal activity. README mirrors npm page.

**LinkedIn:**
Primary channel for prescriber and compliance decision-maker audience. Weekly post: one EU AI Act insight or platform update. No fluff.

**Hacker News / dev.to:**
"Show HN: Trustixy — one-line audit trail for Anthropic/OpenAI agents" post on launch. Technical post on how the SDK proxy works.

---

## Metrics to Track

| Metric | Target (month 6) |
|---|---|
| Organic sessions/month | 2,000+ |
| Keyword rankings (top 10) | 5+ |
| Blog → signup conversion rate | 2–4% |
| npm weekly downloads | 500+ |
| Partner page → application rate | 5–10% |
