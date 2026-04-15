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

## Revenue Models

Three economic models, all prescriber-led. Trustixy is the infrastructure; the prescriber owns the client relationship and commercial negotiation.

---

### Model 1 — Co-signature per acte (cabinets comptables, avocats, consultants)

Le prescripteur fixe lui-même le prix de chaque co-signature (€150–500 selon la complexité du système, le niveau de risque, et la taille du client). Trustixy prélève une commission fixe par acte signé.

**Mécanique :**
- Le prescripteur configure son tarif de co-signature dans son portal (champ `cosignature_fee`)
- Le client voit le tarif lors de sa demande de co-signature
- Le paiement transite via Stripe Connect : le prescripteur reçoit directement, Trustixy prélève €25 de commission par acte
- Le prescripteur gère sa propre facturation client

**Économie prescripteur :**
- 20 clients × 2 co-signatures/an × €300 = €12 000/an de CA co-signature
- Trustixy prélève €25 × 40 actes = €1 000/an
- Le prescripteur garde ~€11 000/an sans aucun coût d'acquisition

**Profils ciblés :** cabinets comptables, cabinets d'avocats, consultants indépendants

---

### Model 2 — Licence fédération (fédérations professionnelles, syndicats, ordres)

Un contrat annuel unique avec la fédération donne accès à tous ses membres. La fédération distribue, supporte, et promeut Trustixy auprès de ses membres — Trustixy n'a qu'un seul interlocuteur.

**Mécanique :**
- Contrat annuel : €3 000–10 000/an selon le nombre de membres (tranches : <100, 100–500, 500+)
- La fédération devient une organisation parente (`parent_organization_id`) — ses membres sont des organisations enfants avec accès automatique au plan Pro
- Co-branding optionnel : "Trustixy, recommandé par [Fédération]"
- La fédération accède à un tableau de bord de conformité agrégé pour l'ensemble de ses membres
- La fédération peut co-signer les documents de ses membres si elle dispose d'une certification prescripteur

**Économie :**
- Un contrat fédération 300 membres = €6 000/an (€20/membre) vs €49/mois × 300 membres = €176 400/an en acquisition directe
- Moins rentable par membre, mais zéro coût d'acquisition et zéro support individuel
- Objectif : 5 fédérations à l'an 1 = €25 000–40 000 ARR

**Profils ciblés :** MEDEF, CPME, syndicats sectoriels (numérique, santé, finance), ordres professionnels

---

### Model 3 — Licence intégrateur (ESN/SSII, intégrateurs AI)

Les intégrateurs livrent des projets AI à leurs clients. La conformité EU AI Act devient une annexe standard du livrable projet — facturée au client dans le budget projet, pas comme abonnement SaaS.

**Mécanique :**
- Abonnement intégrateur : €299–599/mois, usage illimité pour tous leurs projets clients
- L'intégrateur crée des projets clients (`integrator_projects`) ; chaque projet génère un rapport de conformité Trustixy exportable en PDF
- API légère : le rapport peut être généré depuis l'outil de gestion de projet de l'intégrateur
- Le rapport sort sous la marque de l'intégrateur (white-label inclus dans la licence)
- L'intégrateur facture la conformité à son client dans son devis (€500–2 000 par projet) — Trustixy ne voit pas cette marge

**Économie intégrateur :**
- Abonnement Trustixy : €3 600–7 200/an
- Facturation conformité client : €500–2 000 × nombre de projets/an
- ROI positif dès 3–5 projets/an

**Économie Trustixy :**
- Revenu récurrent et indépendant du churn client final
- L'intégrateur fait 100% du commercial et de la relation client
- Objectif : 10 intégrateurs à l'an 1 = €36 000–72 000 ARR

**Profils ciblés :** ESN/SSII délivrant des projets AI, agences de transformation digitale, cabinets de conseil IT

---

## Partnership Tiers

| Tier | Profil | Modèle économique | Revenu Trustixy | Co-signature |
|---|---|---|---|---|
| **Per-act** | Cabinets, consultants | Commission €25/acte via Stripe Connect | Variable selon volume | Cœur du modèle |
| **Fédération** | Fédérations, syndicats | Licence annuelle forfaitaire | €3 000–10 000/contrat | Optionnelle |
| **Intégrateur** | ESN/SSII, intégrateurs | Abonnement mensuel + API | €299–599/mois | Incluse dans livrable |
| **Reseller** | Grands cabinets | Revente sous leur marque | 30% MRR récurrent | Recommandée |

---

## What Prescribers Get

- **Autonomie tarifaire** — le prescripteur fixe le prix de ses co-signatures (Model 1) ou l'intègre dans ses projets (Model 3)
- **Stripe Connect** — encaissement direct sans passer par Trustixy (Model 1)
- **Co-signature queue** dans le portal — documents en attente de validation
- **Co-branding / white-label** — selon le tier
- **Portal partenaire** avec dashboard de santé de conformité par client
- **Dashboard fédération** — vue agrégée de conformité de tous les membres (Model 2)
- **API projet** — génération de rapports depuis leur outillage (Model 3)
- **Marketing assets** (pitch deck, one-pager, badge partenaire)
- **Certification programme** — pour valider leur capacité à co-signer

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

| Métrique | Cible an 1 |
|---|---|
| Partenaires per-act actifs | 15 |
| Fédérations sous contrat | 5 |
| Intégrateurs sous licence | 10 |
| Organisations acquises via prescripteurs | 200 |
| % du revenu total via canal prescripteur | >70% |
| Taux d'adoption co-signature | >40% des documents |
| Volume actes co-signés / mois (an 1 fin) | >100 |
| ARR canal prescripteur | >€120 000 |

---

## Risks & Mitigations

| Risque | Mitigation |
|---|---|
| Partenaires peu actifs commercialement | Assets marketing clé-en-main + commission directe sur actes (revenu immédiat visible) |
| Réticence à co-signer (peur de la responsabilité) | Scope document explicite, deux niveaux de certification, notes professionnelles pour cadrer la revue |
| Cycle de vente long pour les fédérations | Démarrer avec les associations numériques (TECH IN France, Syntec Numérique) — sensibilisées EU AI Act |
| Prix co-signature trop bas fixé par le prescripteur | Fournir une grille tarifaire recommandée dans les assets marketing |
| Intégrateurs ne renouvellent pas leur licence | Rattacher la valeur au rapport de livrable projet — non-renouvellement = perte d'un avantage concurrentiel |
| White-label dilue la marque Trustixy | Limiter le white-label au tier Reseller et Intégrateur |
| Co-signature crée un goulot d'étranglement | Co-signature optionnelle — documents avec disclaimer toujours téléchargeables |
