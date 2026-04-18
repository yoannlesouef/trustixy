# Privacy & GDPR Compliance

> **Legal review required before launch.** This spec is the engineering and operational reference for GDPR compliance. All sections marked [LEGAL REVIEW] must be validated by a qualified lawyer before publication.

Trustixy SAS is the **data controller** under GDPR. DPO contact: dpo@trustixy.com.

---

## 1. Personal Data Inventory

### Data we collect directly

| Data | Source | Subjects |
|---|---|---|
| Email address, first name, last name | Signup form | Org users, prescribers |
| Organization name | Signup form | Organizations |
| IP address, browser/OS | Auth logs | All users |
| AI agent descriptions, use cases, data types | User input (classification form) | Org users |
| SDK action metadata (model, tokens, duration, affected file paths) | SDK ingest | Org users / their end users |
| `human_identity` (email or identifier of who triggered a session) | SDK ingest (optional, caller-provided) | Third parties (org's own end users) |
| Payment data (card, billing address) | Stripe (we never touch raw card data) | Org users |
| Co-signer name and firm | Partner onboarding | Prescribers |
| Feedback submissions | In-app feedback form | Org users |

### Data we do NOT collect
- Full prompt/response content (unless `captureContent: true` is explicitly enabled by the org)
- Passwords in plaintext (bcrypt-hashed)
- Payment card numbers (Stripe handles PCI-DSS)

---

## 2. Lawful Basis per Processing Activity

| Processing activity | Legal basis (GDPR Art. 6) | Notes |
|---|---|---|
| Account creation and authentication | Art. 6(1)(b) — contract execution | Necessary to provide the service |
| SDK action log storage | Art. 6(1)(b) — contract execution | Core service feature |
| AI risk classification (sending data to Anthropic API) | Art. 6(1)(b) — contract execution | User initiates classification; Anthropic processes as sub-processor |
| Compliance document generation | Art. 6(1)(b) — contract execution | |
| Billing and payment processing | Art. 6(1)(b) — contract execution | |
| Sending transactional emails (API key, co-sig notifications, alerts) | Art. 6(1)(b) — contract execution | Cannot be unsubscribed from |
| Sending behavioral/product emails (digest, upgrade nudges, re-engagement) | Art. 6(1)(f) — legitimate interest | Balancing test documented below |
| Audit log (immutable platform actions) | Art. 6(1)(c) — legal obligation + Art. 6(1)(f) | Necessary for compliance traceability |
| Platform health monitoring (error rates, latency) | Art. 6(1)(f) — legitimate interest | Aggregated / anonymized where possible |
| `human_identity` field storage | Art. 6(1)(b) — contract execution (org's instruction) | Org is responsible for notifying their own users |

### Legitimate interest balancing test — behavioral emails [LEGAL REVIEW]

**Interest:** Improve user activation and retention; reduce churn; communicate product updates relevant to the user's compliance obligations.
**Necessity:** Email is the primary channel; no less intrusive method achieves the same outcome.
**Balance:** Users are professionals who signed up for a compliance tool; communications are directly related to their use. Opt-out is always available (unsubscribe link in every behavioral email).
**Conclusion:** Legitimate interest applies. Document this test and make it available to supervisory authority on request.

---

## 3. Sub-processors and International Transfers

All sub-processors must have a signed Data Processing Agreement (DPA) with Trustixy.

| Sub-processor | Role | Data transferred | Location | Transfer mechanism |
|---|---|---|---|---|
| **Supabase** | Database, Auth, Storage | All user and platform data | EU (select EU region in Supabase dashboard — **mandatory**) | Adequacy / standard EU hosting |
| **Vercel** | Application hosting, Edge Runtime | HTTP requests, logs | EU Edge nodes where possible; some US nodes | SCCs [LEGAL REVIEW] |
| **Anthropic** | LLM API (classification + doc generation) | Agent descriptions, classification questions | USA | SCCs — verify Anthropic's DPA covers SCCs [LEGAL REVIEW] |
| **Stripe** | Payment processing | Billing data, payment card data | USA / EU | SCCs + PCI-DSS |
| **Resend** | Transactional email delivery | Email address, email content | USA | SCCs [LEGAL REVIEW] |
| **Slack** | Internal platform alerts (Platform Guardian) | Alert content (no user PII if designed correctly) | USA | SCCs — or replace with EU-hosted webhook (e.g. self-hosted) |
| **GitHub** | Issue tracker (admin feedback push) | Feedback title, description, vote count | USA | SCCs — minimize PII in issue body (no user names, only org name) |

### Action items before launch

- [ ] Confirm Supabase project region is set to **eu-west** or **eu-central** — never US
- [ ] Sign Anthropic DPA; confirm SCCs coverage for EU→US transfer
- [ ] Sign Vercel DPA; confirm SCCs for EU→US transfer
- [ ] Sign Resend DPA; confirm SCCs
- [ ] Sign Stripe DPA (usually pre-signed via Stripe's standard agreements)
- [ ] Evaluate replacing Slack webhook with EU-hosted alerting to avoid US transfer of alert data
- [ ] Update CGU Art. 9.6 to list all sub-processors (currently missing Stripe, Resend, Slack, GitHub)

---

## 4. `human_identity` Field — Third-Party Notice

The SDK allows organizations to pass a `human_identity` value (e.g. `user@company.com`) to identify who triggered an AI agent session. This data is stored in `sessions.human_identity`.

**Trustixy's responsibility:** Store and display this data within the organization's account only (enforced by RLS).

**Organization's responsibility:** Organizations using `human_identity` are acting as data controllers for their own users' data. They must:
1. Inform their own users that their identity may be associated with AI agent sessions
2. Have a lawful basis for passing this data to Trustixy
3. Include Trustixy as a sub-processor in their own privacy documentation

This must be stated in the CGU (Art. 7 — User obligations) and in the developer documentation.

---

## 5. GDPR Rights — Procedures and SLAs

All rights requests are received at **dpo@trustixy.com**. Response deadline: **30 days** (extendable to 90 days for complex cases, with notification within 30 days).

### Right of access (Art. 15)

Provide a full export of:
- Account data (email, name, org)
- All agents, classifications, documents, obligations for the org
- Audit log entries linked to the requesting user
- Feedback submissions
- Email communication history (list of triggered emails, not content)

Export format: JSON or PDF. Delivered via a signed download URL (Supabase Storage), valid 24 hours.

**Implementation:** Build a server action `exportUserData(userId)` that aggregates all tables filtered by `user_id` or `organization_id`.

### Right to rectification (Art. 16)

Users can update their own email, name, and organization data directly in Settings. For data they cannot self-edit (e.g. audit log entries), explain that these are immutable by design (legal retention).

### Right to erasure (Art. 17) — anonymization procedure

**Tension:** Audit logs and compliance documents are immutable by design. They may need to be retained for legal reasons (GDPR Art. 17(3)(b): legal obligation; Art. 17(3)(e): legal claims).

**Resolution — two-tier approach:**

**Tier 1 — Identity fields (always erased within 30 days of request):**
- `users.email` → replaced with `[deleted@trustixy-erased.invalid]`
- `users` name fields → replaced with `[Deleted User]`
- `sessions.human_identity` → replaced with `[deleted]`
- `audit_logs.user_id` → replaced with a fixed null UUID `00000000-0000-0000-0000-000000000000`
- `document_signatures` co-signer name/firm in PDF → not retroactively removable from already-issued PDFs [LEGAL REVIEW]

**Tier 2 — Structural records (retained for legal obligation, anonymized):**
- `actions`, `sessions`, `classifications`, `documents` — retained but user-identifying fields anonymized as above
- Organizations may request full deletion of their entire org data (agents, sessions, documents, classifications) — granted unless there is an active legal dispute or regulatory obligation to retain

**Implementation:** Build an admin action `anonymizeUser(userId)` and `deleteOrganization(orgId)` (hard delete of all org data, cascading).

### Right to portability (Art. 20)

Available for data provided by the user and processed under contract execution:
- Agent metadata (name, description, use case, sector)
- Classification results and obligations
- Compliance documents (PDF)
- Audit log (CSV)

Same export as Art. 15 access, provided in machine-readable format (JSON).

**Implementation:** Reuse `exportUserData()`. Triggered from Settings → Privacy → Export my data.

### Right to restriction (Art. 18)

Mark the user account as restricted (`restricted_at` flag on `users`). Restrict all processing except storage. Do not send emails, do not run any cron logic affecting this user.

### Right to object (Art. 21)

For processing based on legitimate interest (behavioral emails, platform analytics):
- Provide an unsubscribe mechanism in every behavioral email
- Provide an "Email preferences" page in Settings where users can opt out of all non-transactional emails
- Store opt-out preference in `users.marketing_opted_out (boolean)`

### Right not to be subject to automated decision-making (Art. 22)

Trustixy's AI risk classification is **decision-support**, not an automated decision with legal effect. The user reviews and confirms all classifications before they are applied. The mandatory disclaimer reinforces this. No automated decision is made without human review.

**However:** Trustixy must be transparent about the role of AI in generating classifications. This is covered by the disclaimer and confidence score display, but must also be stated in the CGU. [LEGAL REVIEW]

---

## 6. Data Retention Schedule

| Data category | Retention period | Deletion method |
|---|---|---|
| User account data (email, name) | Duration of contract + 3 years | Anonymized on erasure request; hard deleted 3 years after contract end |
| Session and action logs (Free plan) | 30 days | Automatic nightly Supabase job (cascades to actions) |
| Session and action logs (Pro plan) | 1 year | Automatic nightly Supabase job |
| Session and action logs (Enterprise) | Per contract | Configured per org |
| Classifications, obligations, documents | Duration of contract + 3 years | Anonymized on erasure; retained for legal obligation |
| Audit logs | Duration of contract + 3 years | Anonymized (user_id replaced); structural record retained |
| Payment records (Stripe) | 10 years (French accounting law) | Not deletable — legal obligation |
| IP addresses / auth logs | 12 months | Automatic rotation |
| Feedback submissions | Until deleted by user or org | Hard delete on request |
| `human_identity` in sessions | Same as session log per plan | Anonymized (`[deleted]`) on erasure request |

### Free plan nightly session purge (implementation note)

Supabase scheduled job, runs at 02:30 UTC:
```sql
DELETE FROM actions WHERE session_id IN (
  SELECT id FROM sessions
  WHERE organization_id IN (SELECT id FROM organizations WHERE plan = 'free')
  AND created_at < NOW() - INTERVAL '30 days'
);
DELETE FROM sessions
WHERE organization_id IN (SELECT id FROM organizations WHERE plan = 'free')
AND created_at < NOW() - INTERVAL '30 days';
```
Cascade: `sensitive_file_alerts` linked to deleted sessions are also deleted.

---

## 7. Data Breach Response Procedure

**GDPR Art. 33:** Notify CNIL within **72 hours** of becoming aware of a breach that poses a risk to individuals' rights.
**GDPR Art. 34:** Notify affected users without undue delay if the breach is likely to result in high risk.

### Breach response steps

1. **Detection** — Platform Guardian alert or manual discovery
2. **Containment** (within 1 hour) — revoke affected tokens, isolate affected data, block suspicious IPs
3. **Assessment** (within 4 hours) — determine scope, affected users, type of data, risk level
4. **CNIL notification** (within 72 hours) — via CNIL online portal (notifications.cnil.fr). Required information: nature of breach, categories/number of affected persons, likely consequences, measures taken
5. **User notification** (if high risk) — email to affected users within 24 hours of CNIL notification; describe the breach, data affected, measures taken, DPO contact
6. **Post-mortem** — document the incident in an internal breach register (required by GDPR Art. 33(5))

**Contacts to notify internally:** Yoann (CEO) immediately; legal counsel within 4 hours.

---

## 8. Cookie and Tracking Policy

Trustixy uses minimal cookies. No third-party advertising or tracking.

| Cookie | Type | Purpose | Duration |
|---|---|---|---|
| `sb-auth-token` | Strictly necessary | Supabase authentication session | Session |
| `sb-refresh-token` | Strictly necessary | JWT refresh | 30 days |
| `trustixy-consent` | Strictly necessary | Store cookie consent choice | 12 months |

No analytics cookies in v1 (no Google Analytics, Hotjar, Mixpanel, etc.). If analytics are added in future, update this table and add a consent banner.

**Cookie banner:** Required on first visit for EU users. Must offer accept/reject for any non-essential cookies. Strictly necessary cookies do not require consent.

**Implementation:** A minimal banner component on first load. Choice stored in `trustixy-consent` cookie. No cookie set before consent except strictly necessary ones.

---

## 9. Email Consent and Preferences

### Transactional emails (no consent required — contract execution)
- API key delivery
- Email verification
- Co-signature request/completion notifications
- Sensitive file alerts
- Payment failure notifications
- Account security (password reset, new login)

### Behavioral/product emails (opt-out available — legitimate interest)
- Activation sequence (24h, 48h nudges)
- Weekly digest
- Upgrade nudges
- Re-engagement emails

**Opt-out mechanism:**
- Unsubscribe link in every behavioral email footer
- Settings → Notifications → "Product & activity emails" toggle
- Stored in `users.marketing_opted_out (boolean)` — default `false`
- Must be checked before any behavioral email is sent

**Implementation:** Add `marketing_opted_out` column to `users` table. Email sending logic checks this before triggering any behavioral sequence.

---

## 10. Trustixy's Own EU AI Act Status

Trustixy uses AI (Anthropic Claude API) to classify other organizations' AI systems. This means **Trustixy is itself a provider of an AI-assisted tool** subject to the EU AI Act.

### Trustixy's self-assessment [LEGAL REVIEW required]

- **Use case:** AI-assisted risk classification of AI systems for regulatory compliance
- **Decision type:** Decision-support (human reviews and confirms all classifications)
- **Impact on natural persons:** Indirect (affects organizations' compliance posture, not individuals directly)
- **Preliminary risk level:** Likely **Limited risk** — AI system with transparency obligations (Art. 52)
- **Transparency obligation (Art. 52):** Users must be informed they are interacting with an AI system — covered by the mandatory disclaimer on all documents

**Required disclosures (already implemented):**
- Mandatory disclaimer on every generated document ✓
- Confidence score displayed with every classification ✓
- "AI-assisted" labeling in UI ✓

**Required disclosures (to add):**
- CGU must state explicitly that Trustixy is an AI system provider and describe the role of AI in generating classifications
- Technical documentation of Trustixy's own classification model behavior (model used, prompt version, regulatory version) must be maintained internally

---

## 11. Updates to CGU Required

The following must be added to `cgu.md` before launch:

- [ ] **Art. 9.6** — Add missing sub-processors: Stripe, Resend, Slack, GitHub
- [ ] **Art. 9.6** — Add international transfer mechanism for each US-based sub-processor (SCCs)
- [ ] **Art. 9.3** — Add explicit lawful basis per processing activity
- [ ] **Art. 9.5** — Add right to restriction and right to object (currently only lists: access, rectification, erasure, portability, opposition)
- [ ] **Art. 9 (new section)** — Add cookie policy reference
- [ ] **Art. 7** — Add obligation for organizations using `human_identity` to inform their own users
- [ ] **Art. 4 (new section)** — Add Trustixy's own AI Act status and transparency disclosure
- [ ] **Art. 9 (new section)** — Anonymization procedure for erasure requests (vs. immutable audit log)
- [ ] **Separate privacy policy page** on trustixy.com (the CGU covers legal terms; a separate, user-friendly privacy page is expected by users and CNIL)
