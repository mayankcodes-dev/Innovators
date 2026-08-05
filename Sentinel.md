
---

# PROBLEM I — Proactive Cybersecurity for Critical Infrastructure

## 🎯 Solution Name: **SENTINEL** — AI-Driven Predictive Cyber Defense Platform

### Problem Restatement
Critical infrastructure (hospitals, power grids, financial networks, water systems) faces escalating, sophisticated cyberattacks. Traditional SIEM tools and firewalls are **reactive** — they raise alerts only after breaches occur. We need a system that **predicts → detects → contains → recovers** with minimal human latency.

---

### 💡 Core Idea
**SENTINEL** is a distributed, AI-powered cybersecurity orchestration platform that uses **behavioral anomaly detection**, **graph-based threat propagation modeling**, and **automated playbook execution** to shift security posture from reactive to predictive — protecting hospitals, grids, and banks before attackers succeed.

---

### 🔧 Engineering Architecture

#### System Components

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         SENTINEL ARCHITECTURE                           │
│                                                                         │
│  ┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐            │
│  │ Hospital │   │  Bank    │   │  Grid    │   │ Transit  │  ← Sources  │
│  │ Network  │   │ Network  │   │ SCADA    │   │ SCADA    │             │
│  └────┬─────┘   └────┬─────┘   └────┬─────┘   └────┬─────┘            │
│       │              │               │               │                  │
│       └──────────────┴───────────────┴───────────────┘                 │
│                              │                                          │
│                   ┌──────────▼──────────┐                              │
│                   │  Edge Sensor Layer  │  (eBPF probes + NetFlow)     │
│                   └──────────┬──────────┘                              │
│                              │  Kafka Stream (billions of events/day)  │
│                   ┌──────────▼──────────┐                              │
│                   │  SENTINEL Core      │                              │
│                   │  ┌───────────────┐  │                              │
│                   │  │ Graph Neural  │  │  ← Threat Propagation        │
│                   │  │ Network (GNN) │  │                              │
│                   │  ├───────────────┤  │                              │
│                   │  │ LSTM Anomaly  │  │  ← Behavioral Baseline       │
│                   │  │ Detector      │  │                              │
│                   │  ├───────────────┤  │                              │
│                   │  │ Threat Intel  │  │  ← MITRE ATT&CK mapping      │
│                   │  │ Correlator    │  │                              │
│                   │  └───────────────┘  │                              │
│                   └──────────┬──────────┘                              │
│                              │                                          │
│              ┌───────────────┼───────────────┐                         │
│              │               │               │                         │
│   ┌──────────▼──────┐  ┌────▼────┐  ┌───────▼──────┐                 │
│   │ Auto Containment│  │ SOC     │  │  Recovery    │                  │
│   │ Engine          │  │ Dashboard│  │  Orchestrator│                  │
│   │ (Firewall API)  │  │         │  │  (Runbooks)  │                  │
│   └─────────────────┘  └─────────┘  └──────────────┘                  │
└─────────────────────────────────────────────────────────────────────────┘
```

#### Tech Stack

| Layer | Technology | Justification |
|---|---|---|
| **Data Ingestion** | Apache Kafka + eBPF sensors | Real-time, zero-copy kernel-level telemetry |
| **Stream Processing** | Apache Flink | Sub-100ms windowed anomaly detection |
| **ML Core** | PyTorch GNN + LSTM | Graph for lateral movement; LSTM for time-series baselining |
| **Threat Intel** | MITRE ATT&CK API, VirusTotal, Shodan | Enrich alerts with known TTPs |
| **Containment** | pfSense API, AWS Security Groups API | Programmatic isolation of compromised nodes |
| **Backend API** | FastAPI (Python) + gRPC for internal services | High performance + typed contracts |
| **Frontend SOC** | React + D3.js + WebSockets | Live network topology + real-time threat map |
| **Database** | TimescaleDB (metrics) + Neo4j (graph) | Purpose-built for time-series + graph traversals |
| **Orchestration** | Kubernetes + Helm | Auto-scaling under attack load spikes |
| **Incident Playbooks** | Temporal.io (durable workflows) | Resilient automated response steps |

#### Key Engineering Decisions

1. **GNN for Lateral Movement Detection**
   - Model the entire infrastructure as a directed property graph
   - Nodes = devices/services; Edges = communication patterns
   - GNN learns normal communication subgraphs
   - Anomalous edge patterns (e.g., printer → AD server) trigger alerts with confidence scores

2. **LSTM Behavioral Baseline**
   - Per-device time-series of 40+ features (CPU, net I/O, syscall frequency, login patterns)
   - Rolling 30-day baseline; deviation >3σ triggers investigation
   - Trained with federated learning so each organization's baseline stays private

3. **Automated Containment Playbooks (Temporal.io)**
   ```
   Attack Detected → Risk Score Calculated → 
   IF score > 90: auto-isolate node + page SOC + snapshot memory
   IF score 70-90: rate-limit + shadow-mode + alert SOC
   IF score < 70: log + monitor + enrich threat intel
   ```

4. **MITRE ATT&CK Mapping**
   - Every alert tagged with ATT&CK Tactic + Technique
   - Enables threat actor attribution ("This looks like APT29 — Cozy Bear")
   - SOC analysts gain context, not just raw data

---

### 🌍 Real-World Inspiration

| Inspiration | What We Borrowed |
|---|---|
| **Darktrace (UK)** | Unsupervised AI learning normal "immune system" behavior |
| **Microsoft Sentinel** | Cloud-native SIEM with SOAR playbook automation |
| **Vectra AI** | Network detection using ML without signature databases |
| **CrowdStrike Falcon** | Lightweight endpoint sensor + cloud correlation |
| **CISA's SHIELD** | US Govt's Active Defense framework — layered defense |

> **Key Differentiator vs. existing tools**: SENTINEL is purpose-built for **OT/SCADA + IT convergence** (e.g., hospital biomedical devices + hospital IT) which Darktrace and CrowdStrike handle poorly. We also add **recovery orchestration**, which existing SIEMs skip entirely.

---

### ✅ Values We Built On

| Value | How It Manifests |
|---|---|
| **Zero Trust** | Every device is authenticated per request, never trusted by position |
| **Privacy First** | Federated learning: raw logs never leave the client's network |
| **Human-in-Loop** | AI recommends; for score < 95, human approves containment |
| **Explainability** | Every alert shows the exact graph path + features that triggered it |
| **Resilience** | SENTINEL itself is hardened — air-gapped management plane |

---

### 📐 Mockup / Wireframe Guidelines

**Screen 1 — Live Threat Map**
- Full-screen interactive network graph (D3.js force-directed)
- Nodes color-coded: green (healthy) → yellow (suspicious) → red (compromised)
- Animated "attack pulse" radiating from compromised node
- Side panel: real-time event feed with MITRE tags

**Screen 2 — Alert Triage Dashboard**
- Kanban-style: Investigating → Contained → Resolved
- Each card: severity badge, affected asset, ATT&CK technique, AI confidence %, suggested action
- One-click "Isolate Node" button → triggers containment API

**Screen 3 — Threat Intelligence Overlay**
- World heatmap of attack origins
- Timeline chart: attack attempts vs. auto-contained incidents
- Sector comparison: "Your hospital vs. industry average"

**Screen 4 — Recovery Orchestrator**
- Step-by-step playbook progress (Temporal workflow steps shown as a flow)
- ETA to full restoration, services restored %, rollback option

---

### 📈 Scalability & Future Impact

- **Phase 1 (Hackathon MVP)**: Single-tenant SIEM dashboard + LSTM anomaly detector on simulated hospital network data
- **Phase 2 (6 months)**: Multi-tenant SaaS, GNN implementation, Kafka ingestion at 1M events/sec
- **Phase 3 (1 year)**: ISAC (Information Sharing and Analysis Center) integration — share anonymized threat intel across hospitals
- **Phase 4 (3 years)**: National-level deployment as a critical infrastructure protection layer (GovTech)

**Scalability numbers**: Kafka + Flink pipeline handles **5M events/second**. Neo4j Enterprise supports graphs of **100B+ edges**. Kubernetes auto-scales ML inference pods during attack spikes.

---

### ✅ Feasibility

| Concern | Mitigation |
|---|---|
| **Data Access** | Use synthetic + public CICIDS2017/2018 datasets for training/demo |
| **Model Accuracy** | Industry GNNs achieve 97%+ F1 on lateral movement; start with LSTM (simpler) |
| **False Positives** | Tiered alert system — low confidence = log only; reduces SOC fatigue |
| **Integration with Legacy OT** | Passive network taps (mirror port) — no changes to production systems |
| **Single Developer** | MVP focuses on: React dashboard + FastAPI backend + LSTM on CICIDS dataset |

---
---
