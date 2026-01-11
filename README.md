# Gesture Recognition: Spiking vs. Polynomial Kernels
### A Comparative Study of PLEIADES and S-TLLR on Event-Based Data

This project evaluates two fundamentally different neural architectures for real-time gesture recognition using the **DVS128 Gesture dataset**. We compare **PLEIADES** (Polynomial-based temporal kernels) and **S-TLLR** (Spiking Neural Network with local learning rules) to analyze the trade-offs between biological plausibility, latency, and classification accuracy.

---

## 📌 Project Overview
The core of this research focuses on the **latency-accuracy trade-off**. In real-time human-computer interaction, a system must classify a gesture as early as possible rather than waiting for the entire motion to complete. This project investigates how architectural choices—specifically spiking vs. frame-based convolutions—impact responsiveness.

### Key Models Compared:
* **PLEIADES (M1):** A frame-based spatiotemporal network using **Jacobi Polynomials** to parameterize temporal kernels. It enforces strict causality, ensuring predictions only rely on past events.
* **S-TLLR (M2):** A **Spiking Neural Network (SNN)** utilizing a biologically inspired local learning rule. It operates on asynchronous event streams, making it highly memory-efficient for edge deployment.

---

## 📊 Performance Benchmarks

### Latency vs. Accuracy Analysis
The following comparison illustrates the "warm-up" period required for each model to reach peak confidence.



| Metric | PLEIADES (M1) | S-TLLR (M2) |
| :--- | :--- | :--- |
| **Peak Accuracy** | **100.00%** | 97.92% |
| **Learning Rule** | Gradient Descent (BPTT) | Local (STDP-inspired) |
| **Input Format** | 10ms Binned Frames | Asynchronous Spikes |
| **Best For** | Ultra-high precision | Edge / Low-power hardware |

---

## 🛠️ Methodology & Implementation

The comparison between PLEIADES and S-TLLR is a study of two opposing training philosophies and temporal processing methods.

### M1: PLEIADES (The Dense/Frame-based Approach)
PLEIADES replaces standard 1D temporal weights with coefficients mixing fixed **orthogonal Jacobi polynomials**. 
* **Per-Step Training:** Unlike many models, PLEIADES is trained with gradients applied at *every* timestep. This forces the model to attempt confident predictions as early as possible.
* **Zero-Padding Strategy:** By using zero-padding for its 10-frame causal kernels, the model produces outputs from the very first frame, though accuracy "warms up" as the 440ms receptive field is populated.
* **Instant Activation:** Uses standard ReLU activations, allowing features to propagate through the network instantly without the integration delay seen in spiking neurons.

### M2: S-TLLR (The Spiking/Bio-inspired Approach)
S-TLLR utilizes Leaky Integrate-and-Fire (LIF) neurons and a three-factor learning rule.
* **End-of-Sequence Training:** The model is traditionally trained to emit a single prediction only after observing the full 1.5s sequence.
* **Membrane Dynamics:** Accuracy is tied to the gradual accumulation of membrane potential. This "integration time" inherently slows down decision-making compared to the instant-fire CNN layers of PLEIADES.
* **Retraining for Fair Comparison:** The original S-TLLR release lacked time-resolved weights. To enable this research, **we retrained the S-TLLR model from scratch**. We then processed each gesture across 20 discrete 75ms timesteps to generate the fine-grained latency data required for a side-by-side benchmark.

---

## 🔍 Comparative Insights

### The "Early Response" Paradox
A key finding in our analysis is that **S-TLLR outperforms PLEIADES at extremely low latencies (<100ms)**. 
* **Why?** Spiking neurons can emit "salient spikes" almost immediately upon seeing a stimulus. Even without full temporal context, these initial spikes often align with the correct class by capturing immediate motion. 
* **The PLEIADES Lag:** During the first 100ms, PLEIADES is still "filling" its temporal kernels; its early predictions are heavily influenced by zero-padding rather than meaningful motion data.

### The Accuracy Crossover
Once the timestamp crosses the **200–300ms mark**, PLEIADES rapidly overtakes S-TLLR. 
* **Reliability:** By the end of the 1.5s sequence, PLEIADES reaches **100% accuracy**. 
* **Conclusion:** While SNNs provide biological plausibility and energy efficiency, parameterized kernels (Polynomials) offer a more robust mathematical path to perfect accuracy on this specific benchmark by effectively modeling complex motion direction through causal convolutions.

---

## 👥 Authors
* **Ginevra Bozza**
* **Saijal Singhal**

*This project was completed as part of a Computer Vision major project, with an emphasis on research-driven comparative analysis.*
