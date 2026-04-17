# Prescriber Partnership Model

## Strategic Rationale

Rather than competing head-to-head with well-funded compliance platforms (Credo AI, Holistic AI, Fairly AI), Trustixy adopts a **prescriber model** that works both externally (B2B2B through trusted advisors) and internally (top-down through in-house Compliance and Security teams).

**External prescribers** are professional intermediaries — accounting firms, law firms, IT integrators — who already have relationships with SMEs needing EU AI Act compliance.

**Internal prescribers** are Compliance officers and Security teams (CISO, InfoSec) within larger organizations, who act as the review and sign-off authority for their company's own AI systems.

This model serves three purposes:
1. **Distribution** — reduces customer acquisition cost by leveraging existing prescriber relationships (external) or top-down mandate (internal)
2. **Trust** — inherits either the advisor's professional credibility or the internal team's organizational authority
3. **Legal validity** — prescriber co-signature transforms an AI-generated document into a professionally endorsed or internally validated compliance deliverable

### Why the SDK makes the prescriber model stronger

Previously, co-signed documents were based on self-declared system descriptions. Now, every compliance document can be backed by a real operational evidence trail from the Trustixy SDK — actual sessions, action types, affected resources, deployment environments.

This means:
- **Prescribers review more credible input** — not just what the organization says, but what their AI systems actually do
- **Co-signed documents carry more evidential weight** — backed by operational data, not a form
- **Prescribers face less ambiguity** — the SDK evidence helps them assess whether the system description is accurate

---

## The Co-signature Value Loop

Trustixy generates the compliance document, pre-filled from SDK operational data. The prescriber reviews, validates, and co-signs it. This creates a model where:

- **End users** get a document with stronger legal standing — backed by real operational evidence
- **Prescribers** get a billable, high-value deliverable without manual document drafting, and with richer evidence to base their review on
- **Trustixy** is protected from liability — the prescriber assumes professional responsibility for their review

---

## Prescriber Profiles

### 1. Accounting & Audit Firms
**Why they prescribe:**
- Already advise SMEs on regulatory obligations (GDPR, tax, social)
- EU AI Act is a natural extension of their compliance scope
- Need a tool to operationalize advice and add a billable deliverable

**Value for them:**
- New revenue stream: Trustixy setup + co-signature review fees
- SDK evidence makes their review faster and more defensible
- Differentiation vs. competing firms

**Partnership model:** Referral commission + white-label option  
**Priority:** Highest — first target for pilot

---

### 2. Law Firms & Legal Tech Consultants
**Why they prescribe:**
- Clients ask them about EU AI Act obligations
- Co-signature aligns with their existing professional responsibility framework

**Value for them:**
- Productize legal expertise into a repeatable service
- SDK-backed documents reduce review time and ambiguity
- Co-branded experience ("AI Compliance by [Firm], powered by Trustixy")

**Partnership model:** Reseller license + co-branding  
**Priority:** High

---

### 3. IT Service Companies & Integrators (ESN/SSII)
**Why they prescribe:**
- They deploy AI tools and systems for clients
- They often control the deployment environment — installing the Trustixy SDK is a natural project deliverable
- Compliance is a natural add-on to any AI project they deliver

**Value for them:**
- Bundle SDK instrumentation + compliance report + co-signature into project scope = higher contract value
- SDK data makes their compliance deliverable credible and operationally grounded
- Competitive differentiator

**Partnership model:** Bundle integration + API access + volume license  
**Priority:** High

---

### 4. Internal Compliance Teams
**Why they prescribe:**
- Already own the EU AI Act compliance mandate within their organization
- Need a structured tool to document, classify, and sign off on all internal AI systems
- Act as the single internal authority for review and validation — no external advisor needed

**Value for them:**
- Centralized registry of all AI systems, auto-populated from SDK data
- Review queue showing all pending documents across business units
- Co-signature confirms they have reviewed every system — creates an internal audit trail
- Dashboard gives CISO/DPO a live view of the organization's AI compliance posture

**Partnership model:** Enterprise license (internal use) — no co-signature revenue, but platform adoption  
**Priority:** High — natural top-down expansion within Enterprise accounts

---

### 5. Internal Security Teams (CISO, InfoSec)
**Why they prescribe:**
- Already review AI system deployments for security risk
- EU AI Act obligations overlap with security and data governance concerns
- Best positioned to validate that the described system behavior matches what the SDK actually captures

**Value for them:**
- SDK evidence aligns with existing security audit workflows
- Co-signature scope matches their review mandate — they are not providing legal advice, just attesting to behavioral accuracy
- Integrated into the same audit trail used for security incident investigation

**Partnership model:** Enterprise license (internal use)  
**Priority:** High — natural fit with security-led deployments; pairs with the Security team persona in product

---

### 6. Cloud & AI Vendors (AWS, Azure, Google Cloud)
**Why they prescribe:**
- Customers face compliance pressure; a compliance layer with SDK instrumentation is a strong marketplace differentiator

**Partnership model:** Marketplace listing + co-sell agreement  
**Priority:** Medium-term — requires product maturity

---

## Revenue Models

Two economic models for v1, all prescriber-led. Trustixy is the infrastructure; the prescriber owns the client relationship.

---

### Model 1 — Per-act co-signature (accounting firms, lawyers, consultants)

The prescriber sets their own co-signature price (€150–500). Trustixy charges a fixed commission per signed act.

**How it works:**
- The prescriber configures their fee in their portal (`cosignature_fee`)
- The client sees the fee at the time of their request
- Payment via Stripe Connect: the prescriber receives directly, Trustixy takes €25 per act
- If the client has the SDK installed, the document includes an evidence trail — higher perceived value, fee justified

**Prescriber economics:**
- 20 clients × 2 co-signatures × €300 = €12,000/year
- Trustixy: €25 × 40 acts = €1,000/year

**Target profiles:** accounting firms, lawyers, independent consultants

---

### Model 2 — Integrator license (IT service companies, ESN/SSII)

Integrators deliver AI projects. Compliance documentation and SDK instrumentation become a standard project deliverable.

**How it works:**
- Subscription: €399/month flat, unlimited usage
- The integrator installs the SDK at the client's site as part of the project
- Compliance reports generated via API and delivered under the integrator's project scope
- Per-act commissions stack on top when integrator clients request co-signature

**Integrator economics:**
- Trustixy subscription: €4,788/year
- Compliance billing to clients: €500–2,000 × projects/year

**Target:** 10 integrators in year 1 = €47,880 ARR

---

## Partnership Tiers

| Tier | Profile | Model | Trustixy revenue | Co-signature |
|---|---|---|---|---|
| **Per-act** | Accounting firms, lawyers, consultants | €25 commission per act | Variable | Core of the model |
| **Integrator** | IT service companies (ESN/SSII) | Monthly subscription + API | €399/month flat | Included in project deliverable |
| **Enterprise internal** | Internal Compliance and Security teams | Enterprise license | Per contract | Internal validation (no co-signature fee) |

---

## What Prescribers Get

- **Pricing autonomy** — prescriber sets their own fees (Model 1) or bundles into project pricing (Model 3)
- **Stripe Connect** — direct payment collection (Model 1)
- **Co-signature queue** — all documents awaiting review and validation
- **SDK evidence** — every document linked to an SDK agent shows the evidence trail, making review faster and more defensible
- **Partner portal** with per-client compliance health dashboard
- **Project API** — generate reports from their own project management tools (Model 3)
- **Marketing assets** — pitch deck, client one-pager, partner badge
- **Certification program** — validates their capacity to co-sign

---

## Prescriber Certification

Scope acceptance is part of the partner signup form — no separate page or flow.

During the partner application, the applicant reads the Co-signature Scope Document and checks an acceptance box. On approval, co-signature is immediately unlocked for all risk levels.

The scope document defines what the co-signature confirms (classification review, professional judgment) and what it does not (legal guarantee, obligation implementation). It is versioned and stored with every co-signed document.

Full certification module (90-min) is deferred to v2.

---

## Co-signature Scope Document

### What the co-signature confirms
1. The AI system description and use case appear accurate and complete
2. The classification is appropriate given the prescriber's professional judgment
3. The listed obligations are applicable and complete
4. Where SDK evidence is present, the operational behavior is consistent with the described use case
5. The review was conducted with professional care

### What the co-signature does NOT cover
- A guarantee that obligations have been implemented
- A legal opinion on the organization's full compliance posture
- A replacement for the organization's own legal counsel
- Liability for inaccurate information provided by the end user
- A guarantee of the completeness or accuracy of SDK-captured data

---

## Pilot Plan

### Target: 3 partners in 90 days

**Month 1:** Map 20 accounting firms + 10 IT integrators in France. Identify decision-makers. Prepare partner pitch deck.

**Month 2:** Sign 3 pilot partners (2 accounting firms, 1 IT integrator). Onboard their first client (SDK + co-signature co-delivery).

**Month 3:** Measure referral conversion rate and co-signature adoption. Refine commission structure.

---

## Success Metrics

| Metric | Year 1 target |
|---|---|
| Active per-act partners | 15 |
| Integrators under license | 10 |
| Organizations acquired via prescribers | 200 |
| % of total revenue via prescriber channel | >70% |
| Co-signature adoption rate | >40% of documents |
| % of co-signed documents with SDK evidence | >60% |
| Prescriber channel ARR | >€80,000 |

---

## Risks & Mitigations

| Risk | Mitigation |
|---|---|
| Inactive partners | Ready-to-use marketing assets + visible direct commission |
| Reluctance to co-sign | Explicit scope document, two certification levels, SDK evidence reduces ambiguity |
| SDK not installed at prescriber clients | The integrator installs the SDK as part of the project — natural fit. For other tiers, co-signature without SDK remains possible (with a stronger disclaimer). |
| Long sales cycle for federations | Start with digital-first associations (TECH IN France, Syntec Numérique) |
| White-label dilutes the Trustixy brand | Limit white-label to Reseller and Integrator tiers |
