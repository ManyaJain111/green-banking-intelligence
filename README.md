# Green Customer Intelligence Platform (GCIP)

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

An intelligent recommendation, carbon impact estimation, and explainable AI platform designed for retail banking. GCIP analyzes multi-source customer financial and property data to identify personalized green transition opportunities (EV loans, home solar/heat pump borrowing, green mortgages, and green personal loans), while enforcing strict financial eligibility rules, customer fatigue limits, and PCAF-compliant emission accounting.

---

## ⚡ Quickstart

### Prerequisites
* Python 3.11+
* Linux / macOS / Unix environment with `bash`

### 1. Clone the repository
```bash
git clone https://github.com/IIIT-ECell/megathon26-terminal-touchers.git
cd megathon26-terminal-touchers
```

### 2. Launch the Platform
```bash
./run.sh
```
This automatically sets up a virtual environment, installs the lightweight dashboard dependencies, and serves the precomputed 30,000-customer results:
* **Bank Manager Dashboard**: [http://localhost:8050](http://localhost:8050)
* **Customer Mobile View**: [http://localhost:8050/app/](http://localhost:8050/app/)

### Additional Execution Modes
```bash
./run.sh --quick      # Live end-to-end run on 2,000 synthetic customers (~3–5 mins)
./run.sh --rebuild    # Full end-to-end synthetic bank generation & scoring on 30,000 customers (~35 mins)
./run.sh --port 8060  # Serve on a custom port
```

---

## 📂 Repository Structure

The codebase is organized into modular directories:

```
megathon26-terminal-touchers/
├── src/gcip/              # Core platform library (ML models, rules, engine, APIs)
│   ├── fr1/               # Ingestion, validation, and Customer 360 builder
│   ├── categorise/        # Transaction categorisation (rules + TF-IDF ensemble)
│   ├── features/          # Point-in-time behavioral & financial feature store
│   ├── models/            # Calibrated LightGBM propensity models & TreeSHAP reasons
│   ├── impact/            # DESNZ 2026 carbon & financial payback calculator
│   ├── recommend/         # Eligibility filters, guardrails, and recommendation ranking
│   ├── explain/           # Compliant explanation templates & guarded LLM rewrites
│   ├── memory/            # Interaction fatigue limits, cooldowns, and feedback replay
│   ├── insights/          # Bank-wide portfolio trends, decarbonization KPIs, and value
│   └── serve/             # Document builders and API handlers
│
├── configs/               # Declarative YAML configurations
│   ├── products.yaml      # Green product definitions, eligibility rules, and reasons
│   ├── carbon.yaml        # DESNZ emission factors, vehicle fuel efficiencies, and tariffs
│   ├── business.yaml      # Unit economics, margins, and bank value calculations
│   ├── pipeline.yaml      # Snapshot dates, thresholds, and execution settings
│   └── source_systems.yaml# Schema contracts for simulated core banking extracts
│
├── dashboard/             # Single-Page Web Applications
│   ├── index.html         # Bank Operations & Strategy Cockpit
│   ├── app/               # Customer-facing banking app interface
│   ├── app.js             # Client-side routing, state, and interactive controls
│   └── styles.css         # Modern styling and responsive components
│
├── data/                  # Static Reference Data & Lexicons
│   ├── reference/         # Official UK DESNZ GHG factors, fuel prices, EPC averages
│   ├── lexicon/           # Merchant names, energy suppliers, MCC mappings
│   └── benchmarks/        # Out-of-domain evaluation datasets
│
├── demo/                  # Shipped 30,000-Customer Demonstration Data
│   ├── data/              # Customer 360, features, and categorised transactions
│   └── pipeline/          # Trained models, calibration curves, impact scores, and metrics
│
├── scripts/               # CLI Entrypoints & Pipeline Stages
│   ├── run_pipeline.py    # Main pipeline runner (propensity -> impact -> recommend -> insights)
│   ├── serve_dashboard.py # HTTP server for dashboards and REST API
│   ├── predict.py         # Batch scoring CLI for new transactions or customers
│   ├── run_scenario_tests.py # End-to-end resilience and data-edge scenario tests
│   └── build_full_data.sh # End-to-end synthetic bank data generator
│
├── tests/                 # Automated Test Suite
│   ├── test_ingest.py     # Ingestion integrity, schema mismatch, and tamper checks
│   └── test_impact_memory.py # Impact engine accuracy and memory feedback tests
│
└── docs/                  # In-Depth Documentation & Project Deliverables
    ├── PIPELINE.md        # Detailed operational pipeline specification
    ├── EXPLAINER.md       # Architecture decisions, methodology, and design choices
    ├── EVALUATION.md      # Full ML metrics, ROC-AUC, PR-AUC, and fairness audits
    ├── DATA_CARD.md       # Complete data dictionary and simulation methodology
    ├── deliverables/      # Milestone reports & final submission PDF (M1, M2, Final Report)
    └── release/           # Release checksums and bundle manifests
```

---

## 🔄 End-to-End Architecture

```
[Core Banking Sources] (Current Accounts, Cards, Mortgages, Loans, Bureau, CRM)
       │
       ▼
[FR1: Ingestion & Audit] ──> Data validation (36 checks) & Harmonized Customer 360
       │
       ▼
[FR2: Transaction Categoriser] ──> 11-category classification (Seen: 99.0%, Unseen: 90.4%)
       │
       ▼
[Feature Store] ──> 63 point-in-time behavioural & environmental features
       │
       ├─────────────────────────────────────────┐
       ▼                                         ▼
[FR3: Propensity Modeling]              [FR4: Impact Engine]
 • Calibrated LightGBM per product       • Annual & Lifetime CO₂e reduction
 • Out-of-fold isotonic calibration      • Annual £ savings & upfront payback
 • TreeSHAP explainability               • PCAF data-quality scoring (1–5)
       │                                         │
       └───────────────────┬─────────────────────┘
                           ▼
             [FR5: Recommendation Engine]
              • Strict eligibility block rules
              • Commercial viability thresholds
              • Customer fatigue limits & cooldowns
              • Vulnerability safeguarding
                           │
       ┌───────────────────┴─────────────────────┐
       ▼                                         ▼
[FR6: Bank Manager View]                [FR7: Customer Mobile Experience]
 • Portfolio decarbonization metrics     • Jargon-free personal impact
 • Regional green opportunity            • Guarded LLM explanations
 • Campaign targeting & bank value       • One-click feedback (re-plans instantly)
```

---

## 📊 Key Highlights & Results

* **Dual-Snapshot Point-in-Time Correctness**: Strictly separates training data (cut off at March 31, 2026) from future take-up labels (April–September 2026), preventing data leakage.
* **Propensity Performance**: Achieves a mean Test PR-AUC of **0.182** (vs 0.105 rules baseline) and lifts campaign take-up precision to **16.45%** in held-out backtests.
* **PCAF-Compliant Carbon Accounting**: Computes physical fuel and kWh usage directly from card and utility patterns against official UK DESNZ conversion factors.
* **Audited Explanations**: 100% template safety compliance with optional guarded LLM rewrites ensuring zero hallucination of financial figures.
* **Interactive Memory**: Adapts to customer feedback in real-time (`not interested`, `already have`, `dismissed`) with dynamic cooldowns.

---

## 📑 Deliverables & Reports

For full technical details and competition deliverables:
* **Final Report**: [`docs/deliverables/final_report.pdf`](docs/deliverables/final_report.pdf)
* **Pipeline Deep-Dive**: [`docs/PIPELINE.md`](docs/PIPELINE.md)
* **Methodology Explainer**: [`docs/EXPLAINER.md`](docs/EXPLAINER.md)
* **Model Evaluation & Fairness Report**: [`docs/EVALUATION.md`](docs/EVALUATION.md)
* **Data Card**: [`docs/DATA_CARD.md`](docs/DATA_CARD.md)
