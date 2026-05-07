<div align="center">

<a href="https://ieeexplore.ieee.org/abstract/document/11457698"><img src="https://img.shields.io/badge/IEEE%20Xplore-Read%20Paper-00629B?style=for-the-badge&logo=ieee&logoColor=white" alt="IEEE Xplore"/></a>
<img src="https://img.shields.io/badge/DOI-10.1109%2FTII.2026.3673230-blue?style=for-the-badge"/>
<img src="https://img.shields.io/badge/Status-Published-brightgreen?style=for-the-badge"/>
<img src="https://img.shields.io/badge/Python-3.9%2B-yellow?style=for-the-badge&logo=python&logoColor=white"/>
<img src="https://img.shields.io/badge/TensorFlow-2.x-orange?style=for-the-badge&logo=tensorflow&logoColor=white"/>
<img src="https://img.shields.io/badge/NVIDIA-Jetson%20Orin%20Nano-76B900?style=for-the-badge&logo=nvidia&logoColor=white"/>
<img src="https://img.shields.io/badge/License-Academic-red?style=for-the-badge"/>

# 🔐 Deep Learning-Based Anomaly Detection and Authenticated Encryption Framework for PMU Data in Industrial Cyber-Physical Systems

### 📄 Published in **IEEE Transactions on Industrial Informatics (TII)** — 2026

**Joel John · Rayappa David Amar Raj · Archana Pallakonda · Rama Muni Reddy Yanamala · Edris Pouresmaeil · Jamshid Aghaei**

*Amrita School of AI, Amrita Vishwa Vidyapeetham · NIT Warangal · IIITDM Kancheepuram · Aalto University · Central Queensland University*

---

> **"A unified framework combining deep learning anomaly detection, multiclass event classification, and ChaCha20-HMAC authenticated encryption for real-time industrial PMU data security — validated on NVIDIA Jetson Orin Nano at the edge."**

</div>

---

## 🚀 Highlights at a Glance

<div align="center">

| Metric | Result |
|--------|--------|
| 🎯 Anomaly Detection Accuracy | **97.79%** (BiLSTM-Transformer AE) |
| 📉 False Positives Reduced | **62%** vs. Simple Autoencoder |
| 📉 False Negatives Reduced | **90%** vs. Simple Autoencoder |
| 🏷️ Event Classification Accuracy | **98.27%** across 6 event classes |
| 🔒 MITM & Replay Attack Mitigation | **100%** success |
| 🔀 Shannon Entropy (Encrypted) | **~8.00 bits** (theoretical maximum) |
| 🛡️ Adversarial Robustness (FGSM) | **87.2%** at ε=0.20 after adv. training |
| ⚡ Edge Inference Latency | **91.7 ms / sample** on Jetson Orin Nano |
| 📈 Adversarial Vulnerability Reduction | **63.5%** via adversarial training |

</div>

---

## 🏗️ System Architecture

The framework implements a **cascaded 3-stage pipeline** for real-time industrial cyber-physical security:

```
┌─────────────────────────────────────────────────────────────────────────┐
│  Real-Time PMU Telemetry Stream (60 Hz · 7 features per sample)         │
└───────────────────────────┬─────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────────────┐
│  Stage 1: BiLSTM-Transformer AUTOENCODER  (Unsupervised Anomaly Det.)   │
│  • Reconstruction Error > ε (95th percentile) → Anomaly Flagged         │
│  • Accuracy: 97.79%  |  62% ↓ FP  |  90% ↓ FN                          │
└──────────────┬──────────────────────────────────┬───────────────────────┘
               │ Anomaly Detected                  │ Normal → Continue Monitoring
               ▼                                   ▼
┌─────────────────────────────────────┐    ┌──────────────────────┐
│  Stage 2: BiLSTM-Transformer        │    │  Continuous Window   │
│  CLASSIFIER  (6-Class Event Type)   │    │  Monitoring          │
│  • Normal / Equipment Fault /        │    └──────────────────────┘
│    Line Outage / Generation Outage / │
│    Load Change / Cyber Bad Data      │
│  • Accuracy: 98.27%                  │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────────────────────────────────────────┐
│  Stage 3: ChaCha20 + HMAC-SHA256  AUTHENTICATED ENCRYPTION              │
│  • 256-bit key · 96-bit entropy nonce · HMAC integrity tag               │
│  • Entropy: ~8.00 bits · Correlation ρ ≈ 0 · 100% attack mitigation     │
│  • 1.5–1.9× faster than AES-256 for real-time industrial workloads       │
└─────────────────────────────────────────────────────────────────────────┘
```

<div align="center">

### End-to-End Pipeline with Timing Breakdown (Jetson Orin Nano)

<img src="assets/figures/pipeline_with_timing.png" width="85%" alt="End-to-End Pipeline with Timing"/>

### ChaCha20-HMAC Security Architecture

<img src="assets/figures/chacha20_hmac_architecture.png" width="90%" alt="ChaCha20-HMAC Architecture for Industrial Telemetry"/>

</div>

---

## 📊 Dataset

We use the **IEEE DataPort SGSMA 2024 Labeled PMU Dataset** — a Hardware-in-the-Loop (HIL) synchrophasor testbed that blends real-time power system simulation with cyber-physical interactions.

### Event Label Mapping

| Event Type | Label | Category |
|------------|-------|----------|
| Normal Operation | 0 | Normal |
| Equipment Fault | 1 | Physical |
| Line Outage | 2 | Physical |
| Generation Outage | 3 | Physical |
| Load Change / Drop | 4 | Physical |
| Cyber-Induced Bad Data (FDIA) | 5 | **Cyber** |

### PMU Feature Space

| Feature | Unit | Typical Range |
|---------|------|---------------|
| Timestamp | ms | — |
| Voltage Magnitude | V | 0.95 – 1.05 p.u. |
| Voltage Phase Angle | deg | −180° to +180° |
| Current Magnitude | A | Variable |
| Current Phase Angle | deg | −180° to +180° |
| Frequency | Hz | 59.95 – 60.05 |
| ROCOF | Hz/s | −1.0 to +1.0 |

> **Training**: ~160,000 samples (90 min) | **Testing**: ~44,000 samples (25 min per test dataset)

---

## 🧠 Deep Learning Models

### Stage 1 — Anomaly Detection (Unsupervised Autoencoders)

Five architectures were evaluated to detect anomalies via reconstruction error:

<div align="center">

| Model | Accuracy | False Positives | False Negatives |
|-------|----------|-----------------|-----------------|
| Simple Autoencoder | 94.19% ± 1.2% | 2,500 | 215 |
| LSTM Autoencoder | 95.83% ± 0.9% | 1,800 | 87 |
| CNN-LSTM Autoencoder | 96.01% ± 0.8% | 1,100 | 43 |
| Transformer Autoencoder | 96.60% ± 0.6% | 1,400 | 65 |
| **BiLSTM-Transformer AE** 🏆 | **97.79% ± 0.4%** | **950** | **22** |

*All results: mean ± std over 5-fold cross-validation on NVIDIA T4 GPU (16GB GDDR6)*

</div>

#### Confusion Matrices — Anomaly Detection Models

<div align="center">

| Simple AE | LSTM AE | CNN-LSTM AE | Transformer AE | **BiLSTM-Trans AE** |
|:---------:|:-------:|:-----------:|:--------------:|:-------------------:|
| <img src="assets/figures/cm_simple_autoencoder.png" width="150"/> | <img src="assets/figures/cm_lstm_autoencoder.png" width="150"/> | <img src="assets/figures/cm_cnn_lstm_autoencoder.png" width="150"/> | <img src="assets/figures/cm_transformer_autoencoder.png" width="150"/> | <img src="assets/figures/cm_bilstm_transformer_autoencoder.png" width="150"/> |

</div>

### Stage 2 — Multi-Class Event Classification

#### Architecture: BiLSTM-Transformer Hybrid

```
Input Sequence X = {x₁, x₂, ..., xT},  xₜ ∈ ℝᵈ  (10-step window @ 60 Hz → 167ms)
        │
        ▼
 ┌──────────────────────────────┐
 │ BiLSTM Layer 1 (32 units)    │  →→ h⃗ₜ ⊕ h⃖ₜ  (bidirectional temporal)
 └──────────────────────────────┘
        │
        ▼
 ┌──────────────────────────────┐
 │ BiLSTM Layer 2 (32 units)    │  →→ hₜ = [h⃗ₜ ; h⃖ₜ]
 └──────────────────────────────┘
        │
        ▼
 ┌──────────────────────────────┐
 │ Transformer Encoder          │  →→ Attn(Q,K,V) = softmax(QKᵀ/√dₖ)V
 │ Multi-Head Attention (4 heads)│     (long-range dependency capture)
 └──────────────────────────────┘
        │
        ▼
 ┌──────────────────────────────┐
 │ Dropout (p=0.5)              │  →→ Regularization
 └──────────────────────────────┘
        │
        ▼
 ┌──────────────────────────────┐
 │ Dense Layer ReLU (64 units)  │  →→ z = WhT + b
 └──────────────────────────────┘
        │
        ▼
 ┌──────────────────────────────┐
 │ Softmax Output (C=6 classes) │  →→ ŷᵢ = exp(zᵢ) / Σexp(zⱼ)
 └──────────────────────────────┘
```

#### Multi-Class Classifier Comparison

<div align="center">

| Model | Accuracy | Key Limitation |
|-------|----------|----------------|
| MLP Baseline | 75.83% | Poor temporal fault detection |
| 1D CNN | 97.33% | Line outage confusion (16%) |
| BiLSTM | 95.33% | Normal class trade-off |
| CNN-LSTM | 66.50% | High inter-class confusion |
| **BiLSTM-Transformer** 🏆 | **98.27%** | **Balanced across all 6 classes** |

</div>

#### Confusion Matrices — Multi-Class Event Classification

<div align="center">

| MLP Baseline | 1D CNN | BiLSTM |
|:------------:|:------:|:------:|
| <img src="assets/figures/cm_mlp_multiclass.png" width="200"/> | <img src="assets/figures/cm_cnn_multiclass.png" width="200"/> | <img src="assets/figures/cm_bilstm_multiclass.png" width="200"/> |

| CNN-LSTM | **BiLSTM-Transformer** 🏆 |
|:--------:|:------------------------:|
| <img src="assets/figures/cm_cnn_lstm_multiclass.png" width="200"/> | <img src="assets/figures/cm_bilstm_transformer_multiclass.png" width="200"/> |

</div>

#### Per-Class Accuracy — BiLSTM-Transformer Classifier

| Event Class | Accuracy |
|-------------|----------|
| Normal Operation | **100%** |
| Equipment Fault | **98%** |
| Line Outage | **97%** |
| Generation Outage | **97%** |
| Load Change / Drop | **93%** |
| Cyber-Induced Bad Data | **94%** |

---

## 🔒 Security Analysis — ChaCha20 + HMAC-SHA256

### Why ChaCha20?

> ChaCha20 uses only **modular addition + bitwise rotation + XOR** — no memory-intensive S-Box lookups unlike AES, making it ideal for resource-constrained edge devices.

<div align="center">

### Encryption Speed Comparison (vs. DES, 3DES, AES)

<img src="assets/security/encryption_time_comparison.png" width="70%" alt="Encryption Processing Time Comparison"/>

*ChaCha20: ~800 µs @ 1024KB — **1.5–1.9× faster than AES**, 62.5× faster than 3DES*

</div>

### 🔑 Cryptographic Nonce Design

```
96-bit Nonce N = N_L ‖ N_M ‖ N_H

N_H (32 bits) = Unix timestamp         → Temporal freshness
N_M (32 bits) = DeviceID               → Inter-node collision prevention
N_L (32 bits) = Monotonic packet counter → Per-session uniqueness

Key rotation: every 24h OR when N_L overflows 2³²
```

### Entropy Analysis

| Dataset | Shannon Entropy H(X) | Max H (8-bit) |
|---------|---------------------|---------------|
| Normal PMU data (encrypted) | **8.0000 bits** | 8 bits |
| Anomalous PMU data (encrypted) | **7.9996 bits** | 8 bits |

> Near-perfect entropy demonstrates ChaCha20 produces **statistically indistinguishable ciphertext**.

### Correlation Resistance (ρ ≈ 0)

<div align="center">

<img src="assets/security/correlation_resistance.png" width="65%" alt="Byte-wise Correlation: Plaintext vs. ChaCha20 Ciphertext"/>

*Pearson correlation ρ ≈ 0 — ciphertext reveals **zero information** about the plaintext*

</div>

### False Data Injection Mitigation

<div align="center">

| Original Data Pattern | Encrypted Data Pattern |
|:---------------------:|:----------------------:|
| <img src="assets/security/original_data_pattern.png" width="280"/> | <img src="assets/security/encrypted_data_pattern.png" width="280"/> |

<img src="assets/security/false_decryption_demo.png" width="55%" alt="False Decryption — 20 data points"/>

*False decryption produces chaotic, unusable data — confirming HMAC-SHA256 integrity enforcement*

</div>

### Key Space Visualization

<div align="center">

<img src="assets/security/key_space_visualization.png" width="60%" alt="Key Space Visualization"/>

**Key Space: 2²⁵⁶ ≈ 1.16 × 10⁷⁷**

Brute-force time at 10¹⁸ keys/sec = **~10⁵⁰ years** (universe age: ~10¹⁰ years)

</div>

### Unlinkability Guarantee

<div align="center">

| Encrypted Data | Anomaly-Detected Data |
|:--------------:|:--------------------:|
| <img src="assets/security/unlinkability_data.png" width="280"/> | <img src="assets/security/unlinkability_anomaly.png" width="280"/> |

*Average dissimilarity scores: same-class = 0.0842, different-class = 0.0895 → **indistinguishable distributions***

</div>

---

## ⚔️ Man-in-the-Middle & Replay Attack Analysis

All 100% of simulated MITM and Replay attacks were successfully detected and mitigated.

<div align="center">

| Attack Rate Over Time | Time Between Attacks | Attack Severity |
|:---------------------:|:-------------------:|:---------------:|
| <img src="assets/security/attack_rate_over_time.png" width="260"/> | <img src="assets/security/time_between_attacks.png" width="260"/> | <img src="assets/security/attack_severity.png" width="260"/> |

| Normal vs. Attack Traffic | Moving Average of Attack Trends |
|:------------------------:|:--------------------------------:|
| <img src="assets/security/normal_vs_attack_traffic.png" width="300"/> | <img src="assets/security/moving_average_attacks.png" width="300"/> |

</div>

---

## 🛡️ Adversarial Robustness

The BiLSTM-Transformer classifier was evaluated under **FGSM** (Fast Gradient Sign Method) and **PGD** (Projected Gradient Descent) adversarial attacks, with and without adversarial training.

<div align="center">

<img src="assets/figures/adversarial_fgsm_pgd.png" width="85%" alt="FGSM and PGD Adversarial Robustness Evaluation"/>

</div>

### Summary: Adversarially Trained vs. Standard Model

| Attack | Standard Model | Adversarially Trained | Improvement |
|--------|---------------|----------------------|-------------|
| FGSM ε=0.05 | 89.5% | **96.3%** | +6.8% |
| FGSM ε=0.10 | 73.9% | **91.9%** | +18.0% |
| FGSM ε=0.15 | 62.4% | **89.1%** | +26.7% |
| FGSM ε=0.20 | 54.1% | **87.2%** | +33.1% |
| PGD (10 iter) | 83.0% | **91.8%** | +8.8% |
| PGD (20 iter) | 75.3% | **89.5%** | +14.2% |
| PGD (40 iter) | 69.2% | **86.7%** | +17.5% |
| Avg. @ ε=0.1 | 73.4% | **91.2%** | +17.8% |

> **63.5% reduction in FGSM vulnerability** · **179% enhancement in PGD resilience** via adversarial training

---

## 🔁 Generalization to Non-PMU Industrial Scenarios

The framework was validated on a public **ICS (Industrial Control System) dataset** containing SCADA network traffic with DDoS, IP-Scan, MITM, Port-Scan, and Replay attacks.

<div align="center">

| Binary Classification | Multi-Class Classification |
|:---------------------:|:--------------------------:|
| <img src="assets/figures/generalization_binary.png" width="300"/> | <img src="assets/figures/generalization_multiclass.png" width="300"/> |

</div>

The framework successfully adapted to non-PMU manufacturing telemetry, demonstrating **architectural portability across diverse industrial sensor modalities**.

---

## ⚡ Edge Deployment — NVIDIA Jetson Orin Nano

<div align="center">

<img src="assets/hardware/jetson_orin_nano.jpg" width="50%" alt="NVIDIA Jetson Orin Nano — Edge Deployment"/>

</div>

### Hardware Specifications

| Component | Specification |
|-----------|---------------|
| GPU | 128-core Maxwell |
| CPU | Quad-core ARM Cortex-A57 @ 1.43 GHz |
| Memory | 4GB LPDDR4 @ 25.6 GB/s |
| Form Factor | 69.6mm × 45mm |

### Pipeline Timing Breakdown (44,000 test samples)

| Pipeline Stage | Time (s) | % of Total | Per-Sample (ms) |
|----------------|----------|-----------|-----------------|
| Preprocessing | 0.5 | 0.01% | 0.011 |
| Anomaly Detection | 511.2 | 12.68% | 11.618 |
| Multiclass Classification | 3518.1 | 87.24% | 79.957 |
| ChaCha20 + HMAC Encryption | 6.5 | 0.16% | **0.148** |
| **Total Pipeline** | **4036.3** | **100%** | **91.734** |

> ✅ **91.7 ms per-sample inference** — well below the <100 ms real-time requirement for synchrophasor systems
> 
> 🔒 **Encryption overhead: only 0.16%** of total processing time — security is virtually free

---

## 🔐 Indistinguishability Proofs (IND-CCA2 Security)

The ChaCha20+HMAC scheme is formally proven secure under five adversarial models:

| Attack Model | Adversarial Advantage |
|-------------|----------------------|
| COA (Ciphertext-Only) | ≤ ε_PRF(λ) |
| KPA (Known-Plaintext) | ≤ q · ε_PRF(λ) |
| CPA (Chosen-Plaintext) | ≤ ε_PRF(λ) |
| CCA (Chosen-Ciphertext) | ≤ ε_PRF(λ) + ε_MAC(λ) |
| **CCA2 (Adaptive)** 🔒 | **≤ ε_PRF(λ) + ε_MAC(λ)** |

---

## 🗂️ Repository Structure

```
📦 TII-2026-PMU-Security-Framework/
├── 📓 nlpts.ipynb                    # Main Jupyter Notebook (full pipeline)
├── 📄 README.md                      # You are here!
├── 📁 assets/
│   ├── 📁 figures/                   # Architecture & performance figures
│   │   ├── pipeline_with_timing.png
│   │   ├── chacha20_hmac_architecture.png
│   │   ├── adversarial_fgsm_pgd.png
│   │   ├── cm_bilstm_transformer_autoencoder.png
│   │   ├── cm_bilstm_transformer_multiclass.png
│   │   └── ...
│   ├── 📁 security/                  # Cryptographic analysis figures
│   │   ├── encryption_time_comparison.png
│   │   ├── correlation_resistance.png
│   │   ├── key_space_visualization.png
│   │   ├── attack_rate_over_time.png
│   │   └── ...
│   └── 📁 hardware/                  # Edge device deployment photos
│       └── jetson_orin_nano.jpg
└── 📄 Deep_Learning-Based_Anomaly_Detection_..._.pdf   # Published paper
```

---

## 🛠️ Getting Started

### Prerequisites

```bash
Python 3.9+
TensorFlow 2.x
NumPy, Pandas, Scikit-learn
Matplotlib, Seaborn
PyCryptodome (for ChaCha20/HMAC)
```

### Installation

```bash
git clone https://github.com/<your-username>/TII-2026-PMU-Security-Framework.git
cd TII-2026-PMU-Security-Framework
pip install -r requirements.txt
```

### Run the Pipeline

```bash
# Open the main notebook
jupyter notebook nlpts.ipynb
```

The notebook walks through:
1. **Data Loading & Preprocessing** — PMU telemetry normalization and windowing
2. **Autoencoder Training** — BiLSTM-Transformer anomaly detection
3. **Classifier Training** — 6-class event classification
4. **ChaCha20 Encryption** — Authenticated encryption with HMAC-SHA256
5. **Security Tests** — Entropy, correlation, unlinkability, MITM/replay simulation
6. **Adversarial Evaluation** — FGSM and PGD robustness testing

---

## 📖 Citation

If you use this work in your research, please cite:

📄 **[Read on IEEE Xplore →](https://ieeexplore.ieee.org/abstract/document/11457698)**

```bibtex
@article{joel2026deep,
  title     = {Deep Learning-Based Anomaly Detection and Authenticated Encryption
               Framework for PMU Data in Industrial Cyber-Physical Systems},
  author    = {Joel John and Rayappa David Amar Raj and Archana Pallakonda and
               Rama Muni Reddy Yanamala and Edris Pouresmaeil and Jamshid Aghaei},
  journal   = {IEEE Transactions on Industrial Informatics},
  year      = {2026},
  publisher = {IEEE},
  doi       = {10.1109/TII.2026.3673230},
  url       = {https://ieeexplore.ieee.org/abstract/document/11457698}
}
```

---

## 🤝 Authors & Affiliations

| Author | Affiliation |
|--------|-------------|
| **Joel John** | Amrita School of AI, Amrita Vishwa Vidyapeetham, India |
| **Rayappa David Amar Raj** | Amrita School of AI, Amrita Vishwa Vidyapeetham, India |
| **Archana Pallakonda** | Dept. of CSE, NIT Warangal, India |
| **Rama Muni Reddy Yanamala** | IIITDM Kancheepuram, India |
| **Edris Pouresmaeil** | Dept. of Electrical Engineering & Automation, Aalto University, Finland |
| **Jamshid Aghaei** ✉️ | Central Queensland University, Rockhampton, Australia |

*Corresponding author: j.aghaei@cqu.edu.au*

---

## 📜 License

This repository is provided for academic and research purposes. The published paper is © IEEE 2026. All code and figures are © the respective authors.

---

<div align="center">

**⭐ If this work helped your research, please star this repository! ⭐**

[![IEEE TII](https://img.shields.io/badge/Published%20in-IEEE%20TII%202026-003087?style=for-the-badge&logo=ieee)](https://ieeexplore.ieee.org/abstract/document/11457698)

[![IEEE Xplore](https://img.shields.io/badge/IEEE%20Xplore-Full%20Paper%20Access-00629B?style=for-the-badge&logo=ieee&logoColor=white)](https://ieeexplore.ieee.org/abstract/document/11457698)

*Combining Deep Learning · Cryptography · Edge AI for Industrial Cyber-Physical Security*

</div>
