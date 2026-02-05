# NSAX-ML
NSA-X (Neuro-Symbolic Autonomous Security Analyst) is a research-grade security analytics prototype that combines machine learning–based anomaly detection with symbolic reasoning and explainability.  

NSA-X Machine Learning Layer
Overview

The Machine Learning (ML) layer in NSA-X is responsible for learning normal behavior, detecting deviations, and producing quantitative anomaly signals that are later interpreted by symbolic reasoning and explainability layers.

Unlike traditional black-box detection systems, NSA-X ML models are:

Unsupervised or weakly supervised

Explainable by design

Human-in-the-loop compatible

Governance-aware

The ML layer does not make final security decisions.
It provides signals, not verdicts.

Role of ML in the NSA-X Pipeline
Raw Events
   ↓
Normalization & Processing
   ↓
ML Anomaly Detection  ← THIS MODULE
   ↓
Symbolic Reasoning (MITRE ATT&CK)
   ↓
Explainability Engine
   ↓
Human Analyst Decision

Implemented ML Components
1️⃣ Event-Level Autoencoder (Core Detection)

Purpose
Learn a baseline of normal network behavior and detect anomalous events.

Key Characteristics

Unsupervised learning

Trained only on benign traffic

Uses reconstruction error as anomaly score

Produces feature-level explanations

Model Type

Deep autoencoder with bottleneck architecture

Dropout regularization

Mean Squared Error loss

Output

event_anomaly_score ∈ [0, 1]

Top contributing anomalous features per event

2️⃣ Temporal Sequence Model (GRU Autoencoder)

Purpose
Detect behavioral patterns over time that are not visible in single events.

Why This Matters
Many attacks are:

slow

multi-step

low-noise

Sequence modeling enables detection of:

reconnaissance patterns

lateral movement

stealthy persistence

Model Type

GRU-based sequence autoencoder

Sliding window over event sequences

Reconstruction-based sequence anomaly score

Output

sequence_anomaly_score ∈ [0, 1]

3️⃣ Explainability Outputs

Each ML prediction is accompanied by:

reconstruction error per feature

most anomalous attributes

temporal deviation summaries

These outputs are consumed by the symbolic layer, not hidden.

Datasets Used

The ML layer is designed to operate on enterprise-grade telemetry datasets, including:

TON_IoT (network + system telemetry)

CIC-IDS-2023 (supervised baseline comparison)

Sysmon / Open Threat Research datasets (endpoint behavior)

Only open or research-permitted datasets are used.

Evaluation Strategy
Why ROC-AUC Is Optional

NSA-X uses unsupervised anomaly detection.

ROC-AUC:

requires both benign and attack labels

is undefined when only one class is present

Therefore:

ROC-AUC is reported only when labeled attacks are available

otherwise, evaluation relies on:

anomaly score distributions

qualitative separation

top-K anomaly inspection

This is academically valid for unsupervised IDS research.

Neuro-Symbolic Integration

The ML layer does not operate independently.

Integration occurs when:

ML models generate anomaly scores

symbolic rules (MITRE ATT&CK) consume these scores

human-readable threat hypotheses are produced

Example:

High anomaly score
+ unusual duration
+ application protocol
→ MITRE T1071 (Application Layer Protocol)


This combination enables:

interpretability

policy enforcement

governance controls

Backend Integration

ML models integrate at Layer 3: Neural Detection in the backend pipeline.

Key integration points

ML inference replaces heuristic scoring

Outputs stored in neural_detections table

Model version tracked for auditability

All ML outputs are:

normalized (0–1)

explainable

auditable

Folder Structure (ML)

ml/

├── notebooks/

│   ├── autoencoder_event.ipynb

│   ├── gru_sequence_model.ipynb

│   └── mitre_rule_mapping.ipynb

├── data/

│   └── processed_datasets.csv

├── outputs/

│   ├── event_scores.csv

│   └── sequence_scores.csv

├── requirements.txt

└── ML.md


Design Principles Followed

Explainability First

Human-in-the-Loop

No autonomous enforcement

Model transparency

Reproducibility

Enterprise relevance

Current Status
Component                	Status
Event Autoencoder         	✅ Implemented
GRU Sequence Model	      ✅ Implemented
Explainability	            ✅ Implemented
MITRE Rule Mapping      	✅ Implemented
Backend Integration	      ⚠️ Interface ready
Production Training	      ❌ Out of scope
Research Positioning

NSA-X ML demonstrates how neural models and symbolic reasoning can coexist, offering:

better trust than pure ML

better scalability than pure rules

stronger governance than autonomous systems


