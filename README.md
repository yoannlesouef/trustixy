# Trustixy 🚀

**AI Trust & Compliance, simplified**

Trustixy is a SaaS platform designed to help companies comply with the **EU AI Act** in minutes.
It enables teams to register AI systems, assess risk levels, and generate compliance documentation automatically.

---

## 🎯 Target

### Who is it for?
- **Companies building or using AI systems** in the EU
- **Teams without legal expertise** who need to become compliant quickly

### What problem does it solve?
The EU AI Act requires companies to:
1. **Register** their AI systems
2. **Classify** their risk level (minimal / limited / high / unacceptable)
3. **Produce compliance documentation**
4. Maintain an **audit trail**

Trustixy automates all of this using LLMs, removing the need for legal or compliance specialists.

---

## ✨ Features

### 🧾 AI Systems Registry

* Create, edit, and manage AI systems
* Centralized inventory of all AI usage

### ⚖️ Risk Classification

* AI Act–aligned classification
* Automated analysis using LLMs
* Outputs:

  * Risk level
  * Explanation
  * Obligations

### 📄 Compliance Document Generator

* Generate structured compliance documents
* Ready for internal or regulatory use

### 🕵️ Audit Logs

* Track all actions:

  * Creation
  * Updates
  * Classification
  * Document generation

### 📤 Export

* Export compliance documentation (PDF / text)

---

## 🧱 Tech Stack

* **Frontend:** Next.js (App Router)
* **Backend:** Supabase (Database + Auth)
* **Styling:** Tailwind CSS
* **AI:** LLM API (classification & document generation)
* **Deployment:** Vercel (recommended)

---

## 🚀 Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/your-username/trustixy.git
cd trustixy
```

### 2. Install dependencies

```bash
npm install
```

### 3. Setup environment variables

Create a `.env.local` file:

```env
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_supabase_key
LLM_API_KEY=your_llm_api_key
```

---

### 4. Setup database (Supabase)

Run the SQL schema from:

```
/specs/data-model.md
```

---

### 5. Run the app

```bash
npm run dev
```

App will be available at:

```
http://localhost:3000
```

---

## 🧭 Project Structure

```
trustixy/
  /specs        → Product & technical specs
  /tasks        → Development roadmap
  /app          → Next.js app
  /components   → UI components
  /lib          → API & utilities
```

---

## 🎯 MVP Scope

The initial version (V1) includes:

* Authentication (Supabase)
* AI systems CRUD
* Risk classification
* Compliance document generation
* Basic dashboard UI

---

## 🧠 Product Vision

> "Get compliant with the EU AI Act in minutes"

Trustixy aims to become the **standard platform for AI governance and compliance**.

---

## 🤝 Go-to-Market Strategy

Trustixy adopts a **B2B2B prescriber model** — distributing through trusted intermediaries who already serve SMEs, rather than competing head-to-head with well-funded players.

### Key Prescriber Channels

| Prescriber | Why they push Trustixy |
|---|---|
| Accounting & audit firms | Natural extension of their compliance advisory |
| Law firms & legal consultants | Delivery tool for AI compliance mandates |
| IT integrators (ESN/SSII) | Compliance add-on to AI project deliverables |
| Industry federations | Practical tool to offer their SME members |
| Cloud & AI vendors | Marketplace compliance layer for AI adoption |

### Partnership Tiers
- **Referral** — 20% first-year MRR commission
- **Reseller** — 30% recurring MRR, white-label option
- **Embedded** — API integration, custom pricing
- **Group** — Flat fee, co-branded portal for federations

See full details in [`/specs/prescriber-partnership.md`](specs/prescriber-partnership.md).

---

## 💰 Monetization (V1)

* Free plan: up to 3 AI systems
* Pro plan: unlimited systems
* Enterprise: advanced export & audit features

---

## 🔒 Security & Compliance

* User-based data isolation
* Secure API handling
* Audit trail for traceability

---

## 🛣️ Roadmap

* Improved classification accuracy
* PDF export enhancements
* Multi-user workspaces
* API integrations
* Advanced audit features

---

## 🤝 Contributing

This project is currently in MVP stage.
Contributions and feedback are welcome.

---

## 📬 Contact

For early access or partnerships:

👉 [yoann.lesouef@gmail.com](mailto:yoann.lesouef@gmail.com)

---

## ⚡ Quick Summary

* Build AI compliance in minutes
* No legal expertise required
* Simple, fast, and scalable

---

**Trustixy — Your AI, compliant by design.**

