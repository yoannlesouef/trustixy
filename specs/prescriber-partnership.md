# Prescriber Partnership Model

## Strategic Rationale

Rather than competing head-to-head with well-funded compliance platforms (Credo AI, Holistic AI, Fairly AI), Trustixy adopts a **B2B2B prescriber model**: selling through trusted intermediaries who already have relationships with SMEs needing EU AI Act compliance.

This model serves three purposes:
1. **Distribution** — reduces customer acquisition cost by leveraging existing prescriber relationships
2. **Trust** — inherits the prescriber's professional credibility
3. **Legal validity** — prescriber co-signature transforms an AI-generated document into a professionally endorsed compliance deliverable

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

### 4. Industry Federations & Trade Associations
**Why they prescribe:**
- Actively seek tools to help SME members navigate new regulations
- One partnership = access to thousands of SMEs

**Value for them:**
- Concrete, practical tool for members
- Positions the federation as proactive on AI regulation

**Partnership model:** Group license + co-branded portal  
**Priority:** Medium — slower sales cycle but high volume

---

### 5. Cloud & AI Vendors (AWS, Azure, Google Cloud)
**Why they prescribe:**
- Customers face compliance pressure; a compliance layer with SDK instrumentation is a strong marketplace differentiator

**Partnership model:** Marketplace listing + co-sell agreement  
**Priority:** Medium-term — requires product maturity

---

## Revenue Models

Three economic models, all prescriber-led. Trustixy is the infrastructure; the prescriber owns the client relationship.

---

### Model 1 — Co-signature per acte (cabinets comptables, avocats, consultants)

Le prescripteur fixe lui-même le prix de chaque co-signature (€150–500). Trustixy prélève une commission fixe par acte signé.

**Mécanique :**
- Le prescripteur configure son tarif dans son portal (`cosignature_fee`)
- Le client voit le tarif lors de sa demande
- Paiement via Stripe Connect : le prescripteur reçoit directement, Trustixy prélève €25/acte
- Si le client a le SDK installé, le document inclut un "Evidence Trail" — valeur perçue plus haute, tarif justifié

**Économie prescripteur :**
- 20 clients × 2 co-signatures × €300 = €12 000/an
- Trustixy : €25 × 40 actes = €1 000/an

**Profils ciblés :** cabinets comptables, avocats, consultants indépendants

---

### Model 2 — Licence fédération (fédérations professionnelles, syndicats)

Contrat annuel unique avec la fédération → accès à tous ses membres.

**Mécanique :**
- Contrat annuel : €3 000–10 000/an selon le nombre de membres
- La fédération distribue à ses membres (accès Pro automatique)
- Co-branding optionnel
- Dashboard de conformité agrégé pour tous les membres

**Objectif :** 5 fédérations à l'an 1 = €25 000–40 000 ARR

---

### Model 3 — Licence intégrateur (ESN/SSII, intégrateurs AI)

Les intégrateurs livrent des projets AI. La conformité + SDK instrumentation devient une annexe standard du livrable.

**Mécanique :**
- Abonnement : €299–599/mois, usage illimité
- L'intégrateur installe le SDK chez le client dans le cadre du projet
- Le rapport de conformité sort sous la marque de l'intégrateur (white-label)
- API légère : rapport générable depuis l'outil de gestion de projet de l'intégrateur

**Économie intégrateur :**
- Abonnement Trustixy : €3 600–7 200/an
- Facturation conformité client : €500–2 000 × projets/an

**Objectif :** 10 intégrateurs à l'an 1 = €36 000–72 000 ARR

---

## Partnership Tiers

| Tier | Profil | Modèle | Revenu Trustixy | Co-signature |
|---|---|---|---|---|
| **Per-act** | Cabinets, consultants | Commission €25/acte | Variable | Cœur du modèle |
| **Fédération** | Fédérations, syndicats | Licence annuelle | €3 000–10 000/contrat | Optionnelle |
| **Intégrateur** | ESN/SSII | Abonnement mensuel + API | €299–599/mois | Incluse dans livrable |
| **Reseller** | Grands cabinets | Revente sous leur marque | 30% MRR | Recommandée |

---

## What Prescribers Get

- **Autonomie tarifaire** — le prescripteur fixe ses prix (Model 1) ou intègre dans ses projets (Model 3)
- **Stripe Connect** — encaissement direct (Model 1)
- **Co-signature queue** — documents en attente de validation
- **SDK Evidence** — chaque document lié à un agent SDK montre l'evidence trail, facilitant la revue
- **Co-branding / white-label** — selon le tier
- **Portal partenaire** avec dashboard de santé par client
- **API projet** — génération de rapports depuis leur outillage (Model 3)
- **Marketing assets** (pitch deck, one-pager, badge partenaire)
- **Programme de certification** — pour valider leur capacité à co-signer

---

## Prescriber Certification Program

### Two levels

**Level 1 — Scope Acceptance (15 minutes)**
- Read the Co-signature Scope Document + checkbox acceptance
- Unlocks co-signature for Minimal and Limited risk systems
- Badge: "Scope-accepted review"

**Level 2 — Full Certification (~90 minutes)**
- 4 modules: EU AI Act risk levels, how to review a Trustixy classification + SDK evidence, co-signature responsibilities, sector quick-guides
- 15-question assessment, 75% pass threshold, immediate retry
- Unlocks all risk levels
- Badge: "Certified AI Compliance Reviewer"
- Valid 12 months; renewal: 20-min module + 5-question quiz

### Module topics (Level 2)
1. EU AI Act overview — structure, timeline, key definitions
2. Risk classification — criteria and examples by sector
3. Obligations by risk level
4. How to review a Trustixy classification — what to verify, how to interpret SDK evidence
5. Sector deep-dives — Healthcare, Finance, HR

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

**Month 1 — Identify & qualify**
- Map 20 accounting firms and 10 IT integrators in France
- Identify decision-makers
- Prepare partner pitch deck (highlight SDK evidence trail as co-signature differentiator)

**Month 2 — Pilot agreements**
- Sign 3 pilot partners (2 accounting firms, 1 IT integrator)
- Onboard their first client (co-delivery including SDK instrumentation + co-signature)
- Gather feedback on review workflow and SDK evidence presentation

**Month 3 — Validate & iterate**
- Measure conversion rate from partner referral
- Measure co-signature adoption rate
- Refine commission structure

---

## Success Metrics

| Métrique | Cible an 1 |
|---|---|
| Partenaires per-act actifs | 15 |
| Fédérations sous contrat | 5 |
| Intégrateurs sous licence | 10 |
| Organisations acquises via prescripteurs | 200 |
| % du revenu total via canal prescripteur | >70% |
| Taux d'adoption co-signature | >40% des documents |
| % de documents co-signés avec SDK evidence | >60% |
| ARR canal prescripteur | >€120 000 |

---

## Risks & Mitigations

| Risque | Mitigation |
|---|---|
| Partenaires peu actifs | Assets marketing clé-en-main + commission directe visible |
| Réticence à co-signer | Scope document explicite, deux niveaux de certification, SDK evidence réduit l'ambiguïté |
| SDK non installé chez les clients prescripteurs | L'intégrateur installe le SDK dans le cadre du projet — natural fit. Pour les autres tiers, co-signature sans SDK reste possible (avec disclaimer renforcé). |
| Cycle de vente long pour les fédérations | Démarrer avec associations numériques (TECH IN France, Syntec Numérique) |
| White-label dilue la marque Trustixy | Limiter white-label au tier Reseller et Intégrateur |
