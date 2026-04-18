# Social Network Strategy

## Platform Priority

Three platforms. One primary, two secondary. Don't spread across five — do two well.

| Platform | Audience | Priority | Frequency |
|---|---|---|---|
| **LinkedIn** | Prescribers, compliance officers, CTOs | Primary | 3–4x/week |
| **X (Twitter)** | Developers, AI builders | Secondary | 2–3x/week |
| **YouTube** | Developers (tutorials) | Secondary | 1x/month |

No Instagram, no TikTok, no Facebook in v1. Wrong audience, wrong context.

---

## Voice

**Post as founder (Yoann), not as the Trustixy brand.**

Personal founder accounts outperform brand accounts at this stage by 10–20x on reach. The brand account reposts and maintains presence, but the primary posting surface is personal.

**Tone:**
- Direct and specific — no "excited to announce"
- Show the problem before showing the product
- Technical enough to be credible, plain enough for non-developers
- Opinion-led — take positions on the EU AI Act, don't just summarize it
- French or English: **post in English** — the EU AI Act audience is pan-European and English is the professional lingua franca. Add French subtitles or separate French posts for explicit French market targeting

---

## LinkedIn Strategy

### Goal

Become the go-to voice on EU AI Act compliance for SMEs and prescribers. Build 2,000+ followers before month 6.

### Content pillars (rotate across these)

**1. EU AI Act education (40% of posts)**
Plain-language explanations of the regulation. No jargon. One concept per post.

Examples:
- "The EU AI Act has 4 risk levels. Here's what each one means for your product."
- "Most people think their internal chatbot is minimal risk. Here's why they're wrong."
- "The 3 EU AI Act deadlines every CTO needs to know in 2025."

These build authority with the compliance audience and drive organic reach from people who share regulation content.

**2. Builder insights (30% of posts)**
What you're learning building Trustixy — SDK decisions, architecture choices, go-to-market experiments. Attracts developer audience and signals product credibility.

Examples:
- "We decided to launch with 3 SDK frameworks instead of 8. Here's why."
- "Lesson from building a multi-tenant SaaS: never skip RLS policies."
- "The hardest part of building a compliance tool isn't the compliance — it's the UX."

**3. Product updates (20% of posts)**
Milestone posts: first customer, first co-signature, SDK download count. Show traction.

Format: specific numbers + what it means. Not: "Excited to share that Trustixy hit 100 users!" — Yes: "100 AI agents now audited on Trustixy. The most common risk level so far: Limited (67%). The most ignored obligation: transparency requirements."

**4. Prescriber-specific content (10% of posts)**
Direct to accounting firms and IT integrators. Revenue opportunity framing.

Examples:
- "Your SME clients are asking about the EU AI Act. Here's how to advise them."
- "Co-signing an AI compliance document: what it means, what it doesn't mean."
- "How accounting firms can add €6,000/year per client with AI Act advisory."

### LinkedIn post format

- No hook bait ("I was shocked to discover...")
- Start with the insight, not the setup
- Use line breaks — walls of text don't get read
- One CTA per post, at the end only
- Max 1 image or carousel per post — text-only performs well on LinkedIn
- Tag relevant people and organizations sparingly — only when genuinely relevant

### LinkedIn carousel format (for EU AI Act education posts)

Slide 1: bold claim or question  
Slides 2–6: one concept per slide, visual if possible  
Last slide: CTA → trustixy.com or checklist download

Carousels get 3–5x the reach of plain text posts on LinkedIn. Use for pillar content.

---

## X (Twitter) Strategy

### Goal

Visibility in the developer and AI builder community. Drive SDK installs and HN engagement.

### Content approach

Different tone than LinkedIn — shorter, more technical, more conversational.

**What to post:**
- SDK code snippets with real output ("Here's what Trustixy captures from a Claude session:")
- Reaction to AI news + EU AI Act angle ("GPT-5 is out — here's which EU AI Act category it likely falls under")
- Behind-the-scenes product decisions
- Thread versions of LinkedIn posts — same content, adapted format

**Thread structure for technical posts:**
```
Tweet 1: bold claim or demo
Tweet 2–5: explanation, code, or data
Last tweet: link + CTA
```

**What not to post:**
- Engagement bait
- Vague motivational content
- Retweets without commentary

### X posting schedule

2–3 posts/week. Prioritize quality over frequency. One thread per week, standalone tweets to fill gaps.

---

## YouTube Strategy

### Goal

Searchable tutorials that drive long-tail developer discovery. One video per month is enough.

### Video types

**Tutorial (primary):**
- "How to add an audit trail to your Anthropic SDK in 5 minutes"
- "Instrumenting a Claude Code session with Trustixy hooks"
- "EU AI Act risk classification: walkthrough with Trustixy"

**Format:** screen recording + voiceover. No face cam required. 5–10 minutes max.

**Why YouTube:** tutorial videos rank in Google search results. A well-optimized "how to instrument Anthropic SDK" video can drive developer traffic for years.

### Production standard

Not polished, but clear:
- Clean IDE theme (dark, readable font size)
- No background music
- Script the intro and outro, talk naturally in the middle
- Add captions (YouTube auto-captions, manually corrected)

---

## Community Participation

Don't just broadcast — participate in existing communities. This drives early signups more reliably than posting on cold social accounts.

| Community | Platform | What to do |
|---|---|---|
| Latent Space Discord | Discord | Share EU AI Act insights when relevant; answer questions about AI compliance |
| AI Engineers | Slack/Discord | Technical posts, SDK announcements |
| r/artificial | Reddit | Answer EU AI Act compliance questions (not self-promotional) |
| Indie Hackers | Forum | Launch post, milestone updates |
| EU AI Act LinkedIn groups | LinkedIn | Answer compliance questions, share resources |
| French tech Slack (e.g. Frenchtech Slack) | Slack | French market networking |

**Rule:** give value 5 times before promoting once. Community trust is worth more than one-off traffic spikes.

---

## Launch Day Social Playbook

Coordinated across platforms on the same day (see inbound-marketing.md Phase 2).

**8:00 AM — Hacker News:**
"Show HN: Trustixy – one-line audit trail for AI agents"

**9:00 AM — LinkedIn (personal):**
```
I've been building AI agents for 2 years and realized I couldn't answer 
a basic question when things went wrong:

"What exactly did the agent do?"

Git shows commits. Logs show lines. Neither gives you an agent-attributed 
record of what AI actually did.

So I built Trustixy.

One line of code wraps your Anthropic or OpenAI client. Every LLM call, 
file write, API call, shell command — traced automatically.

And since we capture it all anyway: we turn that log into EU AI Act 
compliance evidence. 5-question classification, obligations checklist, 
co-signed compliance document.

Free for up to 3 agents: trustixy.com

If you build with AI agents, I'd love your feedback.
```

**9:00 AM — X:**
```
Just launched Trustixy.

npm install @trustixy/sdk
const client = trustixy.wrap(new Anthropic(), { project, agent });

Every action your AI agent takes — traced. 
EU AI Act compliance built on top of the same data.

trustixy.com
```

**9:00 AM — Repost from Trustixy brand accounts** on both platforms.

---

## Metrics

| Metric | Month 1 | Month 3 | Month 6 |
|---|---|---|---|
| LinkedIn followers (personal) | 500 | 1,200 | 2,500 |
| LinkedIn post impressions/week | 2,000 | 8,000 | 20,000 |
| X followers | 200 | 500 | 1,000 |
| YouTube subscribers | 50 | 150 | 300 |
| Social → signup conversion/month | 10 | 40 | 100 |

Social metrics are secondary to signup and revenue metrics. If a platform isn't converting, reduce posting frequency — don't double down.

---

## What Not to Do

- Do not post the same content on every platform simultaneously without adapting it
- Do not chase follower count at the expense of quality
- Do not engage in controversy about competitors
- Do not promise features on social that aren't in the roadmap
- Do not hire a social media manager in v1 — the founder's voice is the product at this stage
