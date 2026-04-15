# Prescriber Partnership Model

## Strategic Rationale

Rather than competing head-to-head with well-funded compliance platforms (Credo AI, Holistic AI, Fairly AI), Trustixy adopts a **B2B2B prescriber model**: selling through trusted intermediaries who already have relationships with SMEs needing EU AI Act compliance.

This model serves three purposes:
1. **Distribution** — reduces customer acquisition cost by leveraging existing prescriber relationships
2. **Trust** — inherits the prescriber's professional credibility
3. **Legal validity** — prescriber co-signature transforms an AI-generated document into a professionally endorsed compliance deliverable, addressing the core weakness of pure LLM-generated output

---

## The Co-signature Value Loop

Trustixy generates the compliance document. The prescriber reviews, validates, and co-signs it. This creates a model where:

- **End users** get a document with stronger legal standing than an unsigned AI output
- **Prescribers** get a billable, high-value deliverable (review + co-signature) without manual document drafting
- **Trustixy** is protected from liability — the prescriber assumes professional responsibility for their review

Co-signature is the primary reason prescribers have a recurring role (not just onboarding) and the main justification for their commission on ongoing MRR.

---

## Prescriber Profiles

### 1. Accounting & Audit Firms
**Why they prescribe:**
- Already advise SMEs on regulatory obligations (GDPR, tax, social)
- EU AI Act is a natural extension of their compliance scope
- Need a tool to operationalize advice and add a billable deliverable

**Value for them:**
- New revenue stream: Trustixy setup + co-signature review fees
- Differentiation vs. competing firms
- Reduces time spent on manual compliance documentation

**Partnership model:** Referral commission + white-label option
**Priority:** Highest — first target for pilot

---

### 2. Law Firms & Legal Tech Consultants
**Why they prescribe:**
- Clients ask them about EU AI Act obligations
- Co-signature aligns with their existing professional responsibility framework
- Trustixy becomes their delivery vehicle for AI compliance mandates

**Value for them:**
- Productize legal expertise into a repeatable service
- Reduce delivery time per client
- Co-signature fees are a natural extension of legal review billing
- Co-branded experience ("AI Compliance by [Firm], powered by Trustixy")

**Partnership model:** Reseller license + co-branding
**Priority:** High

---

### 3. IT Service Companies & Integrators (ESN/SSII)
**Why they prescribe:**
- They deploy AI tools and systems for clients
- Compliance is a natural add-on to any AI project they deliver
- They are co-accountable for the AI systems they build

**Value for them:**
- Bundle compliance + co-signature into project scope = higher contract value
- Reduces legal risk on delivered projects
- Competitive differentiator vs. other integrators

**Partnership model:** Bundle integration + API access + volume license
**Priority:** High

---

### 4. Industry Federations & Trade Associations
**Why they prescribe:**
- Actively seek tools to help SME members navigate new regulations
- One partnership = access to thousands of SMEs
- Enhances their value proposition to members

**Value for them:**
- Concrete, practical tool for members
- Positions the federation as proactive on AI regulation
- Group pricing makes it accessible

**Partnership model:** Group license + co-branded portal
**Priority:** Medium — slower sales cycle but high volume

---

### 5. Cloud & AI Vendors (AWS, Azure, Google Cloud)
**Why they prescribe:**
- Drive AI adoption and face pressure to help customers comply
- A compliance layer with co-signature is a strong marketplace differentiator
- Reduces churn from customers blocked by compliance concerns

**Value for them:**
- Enrich marketplace catalog
- Reduce compliance friction for AI service adoption
- Co-sell opportunity with enterprise accounts

**Partnership model:** Marketplace listing + co-sell agreement
**Priority:** Medium-term — requires product maturity

---

## Partnership Tiers

| Tier | Profile | Model | Revenue Share | Co-signature |
|---|---|---|---|---|
| **Referral** | Accounting firms, consultants | Send leads, earn commission | 20% first year MRR | Optional |
| **Reseller** | Law firms, IT integrators | Sell under own brand | 30% recurring MRR | Recommended (billable) |
| **Embedded** | ESN/SSII, cloud vendors | API integration | Custom | Required for co-signed tier |
| **Group** | Federations, associations | Group license | Flat fee | Optional |

---

## What Prescribers Get

- **Revenue share** on referred or resold accounts
- **Co-signature queue** in the partner portal — pending documents waiting for review
- **Co-branding / white-label** options (Reseller+ tiers)
- **Partner portal** with client compliance health dashboard
- **Dedicated onboarding** support for their clients
- **Marketing assets** (pitch decks, one-pagers, email templates)
- **Partner badge** ("EU AI Act Certified Partner")

---

## Prescriber Certification Program

Co-signature requires certification. Trustixy provides an in-app certification program that serves three purposes:
1. Gives prescribers the knowledge to review and sign confidently
2. Gives end users assurance that the co-signer is qualified
3. Creates a defensible record that the prescriber was trained on the specific version of the regulation in effect at signing

### Program structure
* **Initial certification**: ~2 hours — 5 training modules + 20-question assessment (80% pass threshold)
* **Renewal**: ~30 minutes — focused on regulatory changes since last certification; required every 12 months
* **Certification badge**: displayed on all documents co-signed by a certified prescriber

### Module topics
1. EU AI Act overview — structure, timeline, key definitions
2. Risk classification — criteria and examples by sector
3. Obligations by risk level — what each level requires in practice
4. How to review a Trustixy classification — what to verify, red flags to watch for
5. Sector deep-dives — Healthcare, Finance, HR (user selects relevant ones)

### Certification versioning
* Each certification is linked to a `certification_version` reflecting the EU AI Act content at time of training
* When the regulation changes significantly, a new certification version is issued and renewal is required
* Documents display the certification version active at time of co-signature — providing full traceability

---

## Co-signature Scope Document

Every prescriber reads and explicitly accepts the **Co-signature Scope Document** before their first co-signature. This document defines precisely what the prescriber confirms and what is excluded.

### What the co-signature confirms
1. The AI system description and use case appear accurate and complete
2. The AI-generated classification is appropriate given the prescriber's professional judgment
3. The listed obligations are applicable and complete
4. The review was conducted with professional care consistent with the prescriber's advisory practice

### What the co-signature explicitly does NOT cover
* A guarantee that obligations have been or will be implemented by the organization
* A legal opinion on the organization's full regulatory compliance posture
* A replacement for the organization's own legal counsel
* Liability for inaccurate information provided by the end user in their system description

### Professional notes
Prescribers can add a **Reviewer Comments** section to any document they co-sign. This is optional but strongly recommended for complex or high-risk systems. Notes may include:
* Caveats about the system description's completeness
* Sector-specific considerations not captured in the classification
* Recommended follow-up actions beyond the obligations checklist
* Scope limitations of the review

Professional notes appear in the document and PDF export as a distinct "Reviewer Comments" section. They help document the exact scope of the review and protect the prescriber from over-reliance claims.

### Scope document versioning
The scope document is versioned (`cosignature_scope_version`). Prescribers must re-accept when it changes materially. All co-signatures are permanently linked to the scope version in effect at signing.

---

## Co-signature Responsibilities

By co-signing a Trustixy compliance document, the prescriber confirms that they have:
1. Completed Trustixy certification for the relevant regulatory version
2. Read and accepted the Co-signature Scope Document
3. Reviewed the AI system description and use case
4. Verified the AI-generated classification is appropriate given their professional judgment
5. Validated that the listed obligations are applicable and complete

The prescriber assumes professional responsibility for their review, consistent with their existing professional liability framework (audit, legal, or technical advisory). Trustixy provides the tool and the AI-generated first draft; the prescriber provides the expert validation.

**Important:** Prescribers should verify with their own professional indemnity insurer and/or professional association that co-signing AI-generated compliance documents falls within their covered practice scope before accepting their first co-signature request.

---

## Pilot Plan

### Target: 3 partners in 90 days

**Month 1 — Identify & qualify**
- Map 20 accounting firms and 10 IT integrators in France
- Identify decision-makers (managing partners, heads of innovation)
- Prepare partner pitch deck and one-pager (co-signature as the core value prop)

**Month 2 — Pilot agreements**
- Sign 3 pilot partners (2 accounting firms, 1 IT integrator)
- Onboard their first client together (co-delivery including co-signature)
- Gather feedback on review workflow and co-signature UX

**Month 3 — Validate & iterate**
- Measure conversion rate from partner referral
- Measure co-signature adoption rate
- Refine commission structure and onboarding materials
- Prepare partner program v1 for broader rollout

---

## Success Metrics

| Metric | Target (6 months) |
|---|---|
| Active prescriber partners | 10 |
| Clients acquired via prescribers | 50 |
| % of new MRR from partner channel | >60% |
| Co-signature adoption rate | >40% of documents |
| Partner NPS | >40 |

---

## Risks & Mitigations

| Risk | Mitigation |
|---|---|
| Partners don't actively push the product | Provide ready-to-use sales materials; align incentives with recurring commission |
| Partners reluctant to co-sign (liability fear) | Clearly scope co-signature responsibility in partner agreement; frame as professional review, not legal guarantee |
| Partner sales cycle is long | Prioritize firms with existing compliance advisory practice |
| White-label dilutes Trustixy brand | Limit white-label to Reseller tier |
| Partners build competing products | Non-compete clause in partnership agreements |
| Co-signature creates bottleneck for end users | Make co-signature optional; unsigned documents remain usable with prominent disclaimer |
