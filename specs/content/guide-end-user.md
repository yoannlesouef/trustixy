# User Guide — End User (SME / CTO / Legal Team)

## Who this guide is for

This guide is for anyone at an organization that uses or builds AI systems and needs to comply with the EU AI Act. You do not need legal expertise to use Trustixy. This guide walks you through every step from first login to a co-signed compliance document.

---

## 1. Getting Started

### Create your account
1. Go to trustixy.com and click **Start for free**
2. Enter your email and create a password
3. Enter your organization name — this creates your private workspace
4. You will receive a confirmation email. Click the link to activate your account.

### Access your dashboard
After login, you land on the **Dashboard**. It shows:
- Your compliance summary (how many systems are compliant, need review, or are unclassified)
- Your AI systems list with status badges
- Any active compliance alerts at the top

If your organization was invited by a prescriber partner, their name will appear in the top bar. Your data remains private — your prescriber only sees the compliance status of your systems, not the content.

---

## 2. Identifying Your AI Systems (Wizard)

The first challenge most organizations face is simply knowing which tools they use that qualify as AI systems under the EU AI Act. Trustixy's identification wizard solves this.

### What counts as an AI system?
Any software that uses AI to make or support decisions: a chatbot, a recommendation engine, a CV screening tool, a credit scoring model, a fraud detection system, an automated email classifier. If it uses machine learning or AI to produce outputs that influence decisions — it's likely in scope.

### Running the wizard
The wizard is triggered automatically after your first login. You can also access it anytime from the dashboard ("Discover your AI systems →").

**Step 1 — Select your sector**
Choose your primary sector. Trustixy uses this to filter the tool list to what's most relevant for you.

**Step 2 — Check the tools you use**
You'll see a categorized list of common AI-powered tools. Check everything your organization uses — even tools you're not sure about. Categories include productivity tools, customer service, HR, sales, and finance. There's also a free-text field for tools not in the list.

**Step 3 — Review the analysis**
Trustixy analyzes each tool you checked. For each one in scope of the EU AI Act, you'll see:
- Why it's in scope
- Its likely risk level
- A pre-filled description you can edit

Tools that are out of scope are listed separately with an explanation.

**Step 4 — Confirm and create your registry**
Review the pre-filled descriptions, edit as needed, and click **Create my registry**. All confirmed systems are added to your registry with status Unclassified.

### Description quality
When you save a system description, Trustixy checks its quality automatically. A good description enables an accurate classification — it should clearly explain what the system does, how it makes decisions, what data it uses, and whether humans can override its outputs.

If the description is insufficient, you'll see an orange indicator with specific suggestions: "Please clarify whether personal data is processed" or "Specify how autonomous the system's decisions are." Classification is blocked until the quality score is sufficient — this protects you from getting a meaningless result.

---

## 3. Registering a System Manually

If you want to add a system outside of the wizard:

1. Click **Add system** on the dashboard
2. Fill in the fields:
   - **Name** — a clear internal name (e.g. "Customer support chatbot")
   - **Description** — what the system does in plain language (quality check runs on save)
   - **Use case** — what business decision or process it supports
   - **Data used** — what types of data the system processes (personal, financial, behavioral, etc.)
   - **Autonomy level** — does the system make decisions automatically, or does a human always review?
   - **Owner** — the person or team responsible for this system
3. Click **Save**

The system is created with status **Unclassified**. If the description quality is insufficient, improve it before classifying.

### Edit or archive a system
- Click the system name to open the system page
- Use the edit icon to update any field
- Updating the description automatically sets the status to **Needs Review** — re-classify after significant changes
- To remove a system, use **Archive**. Archived systems are never deleted — they remain in your audit trail.

---

## 3. Running a Classification

### Start the questionnaire
1. Open the system page and click **Classify**
2. Answer 10 structured questions about the system:
   - What sector does it operate in?
   - Who is affected by the decisions it makes?
   - Can a human override its output?
   - What sensitive data does it process?
   - Is it deployed internally or publicly?
   - (and more)
3. Click **Submit** when done

### Review the result
After submission, Trustixy displays:
- **Risk level**: Minimal / Limited / High / Unacceptable
- **Explanation**: why this risk level was assigned
- **Confidence score**: how certain the AI is of this classification (0–100%)
- **Obligations**: a list of what the EU AI Act requires for this system

**If the confidence score is below 70%**, a yellow warning appears:
> "This classification has low confidence. We recommend requesting a review from your prescriber partner before generating a compliance document."

This does not block you — you can proceed without a review. But it is a signal to be careful.

### What the risk levels mean

| Level | What it means | Typical examples |
|---|---|---|
| **Minimal** | No specific EU AI Act obligations | Spam filter, basic recommendation |
| **Limited** | Transparency obligations (users must know they're interacting with AI) | Chatbot, AI-generated content |
| **High** | Strict requirements: risk management, transparency, human oversight, data governance | Hiring tools, credit scoring, medical devices, law enforcement |
| **Unacceptable** | Prohibited under the EU AI Act | Social scoring, real-time biometric surveillance in public spaces |

---

## 4. Managing Your Obligations Checklist

After each classification, an **Obligations Checklist** is generated — a list of concrete actions your organization must take for this system to be compliant.

### Working with the checklist
Each item shows:
- **Title** — what you need to do
- **Description** — detailed explanation
- **Regulatory reference** — the specific EU AI Act article
- **Status** — Todo / In Progress / Done
- **Optional due date**

### Updating item status
1. Click on any checklist item
2. Change the status to **In Progress** or **Done**
3. Changes are saved automatically and logged in the audit trail

### Completion progress
The system page shows a progress bar (e.g. "3 of 7 obligations complete"). Your dashboard shows this for all systems at a glance.

When all obligations are marked **Done**, the system's compliance status upgrades to **Compliant**.

---

## 5. Generating a Compliance Document

### Generate a document
1. From the system page, click **Generate document**
2. Trustixy generates a structured compliance document that includes:
   - System description and purpose
   - Risk classification and justification
   - Applicable obligations
   - Risk analysis
   - Mitigation measures
   - Human oversight section
   - A mandatory disclaimer

The document is linked to your current classification. If you re-classify later, the previous document is preserved and marked as superseded.

### The disclaimer
Every document contains this notice:

> *"This document was generated by Trustixy using AI assistance. It is provided to support your compliance process and does not constitute legal advice. Trustixy cannot be held liable for regulatory decisions made solely on the basis of this document. For legally binding compliance, we recommend co-signature by a qualified prescriber partner or review by a qualified legal professional."*

This is non-removable and appears in both the on-screen view and the PDF export.

### Export to PDF
Click **Export PDF** to download the document. The PDF includes:
- Document header (generation date, regulatory version, confidence score)
- Full document content
- Disclaimer block
- Signature block (shows "Unsigned" until co-signed)

---

## 6. Requesting Co-signature

Co-signature is the process of having your prescriber partner (accounting firm, law firm, or IT integrator) review and professionally endorse your compliance document.

**Why it matters:** An unsigned AI-generated document is a useful starting point. A co-signed document carries genuine professional standing — your advisor has reviewed and validated it.

### When to request co-signature
- When the confidence score is below 70%
- Before submitting documentation to a regulator or auditor
- For high-risk systems (risk level: High or Unacceptable)
- Any time you want professional validation of your compliance posture

### How to request
1. From the document page, click **Request co-signature**
2. Select your prescriber partner (pre-filled if your organization is linked to one)
3. Add an optional message (e.g. "Please review before our audit on June 15")
4. Click **Send request**

Your prescriber receives an email notification and sees the request in their partner portal.

### What happens next
- **Pending:** The document shows "Awaiting review by [Partner Firm]"
- **Changes requested:** You will receive a notification with the prescriber's notes. Update the system description or ask Trustixy to re-classify, then re-submit.
- **Approved and signed:** The document shows a verified badge with the prescriber's name, firm, and date. The PDF export includes a signed signature block.

---

## 7. Understanding Compliance Alerts

Trustixy monitors EU AI Act updates. When a relevant change is published, you receive an alert.

### What an alert looks like
A banner appears on your dashboard and system page:
> "Regulatory update: [Summary of change]. This may affect [System Name]. Re-classification recommended."

You also receive an email.

### What to do
1. Read the alert — it explains what changed and why your system may be affected
2. Click **Re-classify now** to run a new assessment under the updated regulation
3. Or click **Acknowledge** if you have reviewed it and determined no action is needed
4. Or click **Dismiss** if the alert does not apply to your system

All actions are logged in the audit trail.

### Alert history
The **Alerts Center** (accessible from the main navigation) shows all past and active alerts for your organization, filterable by status and system.

---

## 8. Exporting Your Compliance Record

### Export a compliance document
From the document page, click **Export PDF**.

### Export your audit trail
From the system page, scroll to **Audit Log** and click **Export**. This produces a PDF or CSV with every action taken on this system — creation, updates, classifications, document generations, co-signatures, alert acknowledgements.

### Export your organization compliance report
From the dashboard, click **Export compliance report**. This produces a PDF covering all your systems: compliance statuses, classification dates, obligation completion rates, and document versions. Useful for internal governance reviews or partner presentations.

---

## 9. Frequently Asked Questions

**Do I need legal expertise to use Trustixy?**
No. The questionnaire is written in plain language. The classification and document are generated automatically. For high-stakes systems, we recommend having a prescriber partner co-sign.

**What if my system's description changes significantly?**
Update the system description — Trustixy will prompt you to re-classify. Previous classifications and documents are preserved in your history.

**Can I have multiple people in my organization use the same account?**
Yes. You can invite team members from the organization settings. Roles: Admin (full access) and Member (can view and classify, cannot delete or change organization settings).

**What happens if a system is classified as Unacceptable risk?**
The document will describe the prohibition and what you must do: stop deploying the system or modify it until it no longer falls in this category. Trustixy does not block you from registering such a system — it helps you understand and document what needs to change.

**Is my data shared with anyone?**
No. All data is scoped to your organization. If you are linked to a prescriber partner, they see your compliance status and can view documents you explicitly share for co-signature. They do not have access to your raw system data.
