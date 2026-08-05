---

# PROBLEM II — Privacy-Preserving AI Data Collaboration

## 🎯 Solution Name: **PRISM** — Privacy-Preserving Federated Data Marketplace

### Problem Restatement
AI needs data. But personal data — medical records, financial history, user behavior — is locked in silos because sharing it violates privacy, regulation (GDPR, HIPAA), and user trust. We need a way to train powerful AI models on sensitive data **without anyone seeing the data**.

---

### 💡 Core Idea
**PRISM** is a platform that enables organizations and individuals to **contribute data to AI training without exposing it**, using **Federated Learning + Differential Privacy + Confidential Computing**, and gives individuals a **personal data dashboard** where they control exactly what's shared, with whom, and for what purpose — and get compensated for it.

---

### 🔧 Engineering Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          PRISM ARCHITECTURE                             │
│                                                                         │
│   Individual Users          Organizations          AI Researchers       │
│   ┌────────────┐            ┌──────────────┐       ┌──────────────┐    │
│   │ My Data    │            │ Hospital DB  │       │ Request      │    │
│   │ Dashboard  │            │ Bank Records │       │ federated    │    │
│   │ (consent   │            │ (behind      │       │ training     │    │
│   │  manager)  │            │  firewall)   │       │ job          │    │
│   └─────┬──────┘            └──────┬───────┘       └──────┬───────┘    │
│         │                          │                       │            │
│         └──────────────────────────┤                       │            │
│                                    ▼                       ▼            │
│                          ┌─────────────────────────────────────────┐   │
│                          │          PRISM CORE PLATFORM            │   │
│                          │                                         │   │
│                          │  ┌─────────────────────────────────┐   │   │
│                          │  │  Consent & Policy Engine (XACML)│   │   │
│                          │  └──────────────┬──────────────────┘   │   │
│                          │                 │                       │   │
│                          │  ┌──────────────▼──────────────────┐   │   │
│                          │  │  Federated Learning Orchestrator │   │   │
│                          │  │  (Flower Framework)              │   │   │
│                          │  └──────────────┬──────────────────┘   │   │
│                          │                 │                       │   │
│                          │       ┌─────────┴──────────┐           │   │
│                          │       │                    │           │   │
│                          │  ┌────▼────┐         ┌────▼────┐      │   │
│                          │  │Differen-│         │Confiden-│      │   │
│                          │  │tial     │         │tial     │      │   │
│                          │  │Privacy  │         │Computing│      │   │
│                          │  │(DP-SGD) │         │(SGX/TEE)│      │   │
│                          │  └────┬────┘         └────┬────┘      │   │
│                          │       └─────────┬──────────┘           │   │
│                          │                 ▼                       │   │
│                          │  ┌──────────────────────────────────┐   │   │
│                          │  │  Audit Ledger (Hyperledger Fabric│   │   │
│                          │  │  — immutable consent audit trail)│   │   │
│                          │  └──────────────────────────────────┘   │   │
│                          └─────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────┘
```

#### Tech Stack

| Layer | Technology | Justification |
|---|---|---|
| **Federated Learning** | Flower (flwr) framework | Production-grade FL; supports PyTorch/TF/scikit |
| **Differential Privacy** | Google's DP Library (dp-accounting) + DP-SGD | Mathematical privacy guarantees (ε, δ parameters) |
| **Confidential Computing** | Intel SGX / Azure Confidential Computing | Raw data never leaves encrypted enclave |
| **Consent Engine** | XACML policy engine + custom rule DSL | Granular, machine-readable consent policies |
| **Audit Ledger** | Hyperledger Fabric | Immutable, auditable consent + data usage trail |
| **Backend** | FastAPI + Celery (async FL job queue) | Async federated training jobs |
| **Data Marketplace** | PostgreSQL + Redis | Data catalog + contributor reputation scores |
| **Frontend** | Next.js + TypeScript | SSR for data dashboard + researcher portal |
| **Auth** | OAuth2 + DID (Decentralized Identifiers) | Self-sovereign identity for individuals |
| **Compensation** | Stripe API (fiat) / ERC-20 token (future) | Reward data contributors fairly |

#### Key Engineering Innovations

**1. Federated Learning Flow**
```
Researcher requests: "Train diabetes prediction model on hospital EMRs"
↓
PRISM checks: Which hospitals opted-in to 'medical AI research'?
↓
Sends model weights (not data) to each hospital's FL client
↓
Each hospital trains locally on their own data
↓
Only gradient updates (with DP noise added) returned to PRISM
↓
PRISM aggregates with FedAvg algorithm
↓
Final model: trained on all hospitals' data — no data ever left
```

**2. Differential Privacy Guarantees**
- DP-SGD adds calibrated Gaussian noise to gradients
- Privacy budget (ε) displayed per dataset: "Training this model costs ε=0.5 of your monthly budget"
- Users set their own ε threshold — conservative users set lower ε

**3. Individual Data Dashboard**
- Every person sees: "Who has your data, what they trained on it, when, and what model was produced"
- Revoke consent in one click — future training jobs excluded
- Compensation tracker: "You earned ₹340 this month from 3 data contributions"

**4. Smart Consent DSL**
```json
{
  "data_owner": "user:ankita_sharma",
  "allows": {
    "purpose": ["medical_research", "public_health"],
    "NOT": ["advertising", "insurance_pricing"],
    "time_limit": "2026-12-31",
    "geo_restriction": "India_only",
    "model_types": ["classification", "regression"],
    "NOT_model": ["generative", "facial_recognition"]
  },
  "compensation": { "per_use": "₹10", "currency": "INR" }
}
```

---

### 🌍 Real-World Inspiration

| Inspiration | What We Borrowed |
|---|---|
| **Google's Gboard FL** | Pioneered federated learning on-device (keyboard next-word prediction) |
| **Apple's Private Federated Learning** | On-device training, only aggregated insights shared |
| **OCEAN Protocol** | Decentralized data marketplace with tokenized ownership |
| **IBM Watson Health's FL** | Multi-hospital cancer detection without sharing patient records |
| **EU Data Spaces (Gaia-X)** | Sovereign data sharing infrastructure for Europe |
| **Solid Project (Tim Berners-Lee)** | Personal Data Pods — users control their own data |

> **Key Differentiator**: Existing FL platforms (Flower, PySyft) are dev tools, not end-user products. PRISM adds the **human layer** — a dashboard where non-technical individuals actually exercise consent rights, and a **marketplace** where data value flows back to contributors.

---

### ✅ Values We Built On

| Value | How It Manifests |
|---|---|
| **Data Sovereignty** | Individuals own their data; organizations are custodians |
| **Transparency** | Every model trained has a full provenance audit trail |
| **Fairness** | Data contributors — often marginalized groups with rare conditions — are compensated |
| **Regulatory Compliance** | GDPR Article 7 (consent), HIPAA Safe Harbor — built-in, not bolted on |
| **Inclusivity** | Works with low-bandwidth settings (FL compression); supports local languages in UI |

---

### 📐 Mockup / Wireframe Guidelines

**Screen 1 — Individual Data Dashboard ("My Data Vault")**
- Card layout: "Your Health Data", "Your Financial Data", "Your Browsing Patterns"
- Each card: toggle ON/OFF per category, privacy score (A-F grade), earnings this month
- Timeline: "Last accessed by: Apollo Hospitals Research Team — 3 days ago — Purpose: Cancer Detection Study"

**Screen 2 — Consent Manager**
- Tree-view of consent rules (WHO can use → WHAT for → WHEN until)
- Plain English translation: "Apollo Hospital can use your blood reports to train a diabetes model until Dec 2026. They cannot sell it."
- One-click revoke + export (GDPR right to erasure request auto-generated)

**Screen 3 — Researcher Portal**
- Dataset catalog with privacy budget, contributor count, data type
- "Request Training Job" form: model architecture, purpose declaration, compliance checklist
- Live job status: "Aggregating round 12/50 — current model accuracy: 84.2%"

**Screen 4 — Audit Ledger Explorer**
- Immutable log: who accessed, when, model hash, ε consumed
- Blockchain-style visual: linked blocks with transaction details
- Exportable compliance report for regulators

---

### 📈 Scalability & Future Impact

- **Phase 1 (MVP)**: FL with Flower on synthetic medical dataset + consent dashboard (Next.js)
- **Phase 2**: Multi-tenant with real hospital integration (FHIR API for EMR access)
- **Phase 3**: Confidential Computing integration (Azure Confidential VMs)
- **Phase 4**: National Health AI Data Space — government-backed data infrastructure for public health AI (India's ABDM alignment)

**Impact numbers**: India has 1.4B people. If 10% consent to share anonymized health data for cancer/diabetes research, it becomes the **world's largest privacy-preserving medical AI dataset** — enabling models that outperform anything trained in the West.

---

### ✅ Feasibility

| Concern | Mitigation |
|---|---|
| **FL Complexity** | Flower framework abstracts away FL complexity — runnable in hours |
| **DP Math** | Google's dp-accounting library handles ε,δ computation — no research needed |
| **Blockchain Overkill?** | MVP: use PostgreSQL audit log; blockchain in Phase 2 (it's a roadmap item) |
| **Data for Demo** | Synthea (synthetic patient data generator) for realistic healthcare demo |
| **Single Developer** | MVP = Next.js dashboard + Flower FL demo + FastAPI backend; very achievable |

---
