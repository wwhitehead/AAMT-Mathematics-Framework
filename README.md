# AAMT Technical Papers & System Architecture
### The MaiiaM Alchemist Project: A Closed-Form Geometric Alternative to Brute-Force Machine Learning

[![Zenodo Series Root](https://img.shields.io/badge/Zenodo-Series%20Root%2010.5281%2Fzenodo.19600795-blue)](https://doi.org/10.5281/zenodo.19600795)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

This repository serves as the central documentation, architectural blueprint, and implementation landing page for the **AAMT (AsAManThinks) Working Paper Series** and the underlying **MaiiaM Alchemist** engine. This framework establishes a complete alternative AI stack—ranging from foundational abstract algebra to localized edge runtime environments—built to bypass the computational inefficiencies and alignment fragilities of traditional brute-force deep learning.

---

## 🌀 Executive Summary: Shifting the Paradigm

Modern artificial intelligence treats model optimization as a massive statistical brute-force problem, relying on trillions of parameters, massive datacenter clusters, and brittle, external reward models (RLHF) that trigger feedback collapse and model degradation.

The **AAMT Architecture** replaces this paradigm with structural and geometric unity. By introducing a **dual-operator algebraic field ($F \times M$)** and utilizing a shared coordinate system known as **TERA Algebra** (Temporal, Emotional, Rational, Archetypal), this framework embeds strict geometric constraints directly into the inference, routing, and optimization loops.

* **Closed-Form Routing:** Replaces learned softmax matrices with deterministic geometric projections, eliminating learnable gate parameters entirely.
* **Native Runtime Alignment:** Implements a geometric "soft-floor zero-veto" property ($\partial M / \partial d_j \propto 1/d_j$) that filters token-level behavior at inference time without external reward models.
* **Edge Dominance:** Delivers production-grade execution (38–61 TPS, sub-200ms TTFT p95, <6 GB RSS) completely localized on consumer silicon.

---

## 🏛️ The Core Mathematical Foundations (Phase I)
**Series Root Deposit DOI:** [10.5281/zenodo.19600795](https://doi.org/10.5281/zenodo.19600795)  
*AAMT Mathematical Foundations: A Unified Framework for Biological and Artificial Intelligence (Papers I-V).*

These foundational papers establish the fundamental mathematical physics of the system. They define the dual-operator field—balancing linear, additive operations with nonlinear, sign-inverting, oscillatory dynamics—to solve the "Compensation Trap" of traditional arithmetic means while defining the operational **Inference/Weight-Space Breath Cycle**.

---

## 📑 Technical Preprint Series (v1.0)
*Published 2026-05-13 | Total: 22,967 words across 5 standalone preprints.*

Each paper is a self-contained preprint containing an abstract, formal mathematics, related work, proposed evaluation datasets, and transparent limitations. Empirical sections clearly distinguish between mechanisms that are **"Shipped"** in the codebase versus those **"Proposed for v1.1"**.

### Preprint Registry

| # | Paper Title | Version / Words | DOI | Headline Contribution |
| :-: | :--- | :-: | :--- | :--- |
| **1** | **Vortex-Keyed Mixture-of-Experts Routing** | v1.0 / 4,140 | [10.5281/zenodo.20150167](https://doi.org/10.5281/zenodo.20150167) | Closed-form, deterministic, interpretable MoE gate via TERA $\rightarrow$ Meji bit-mask. |
| **2** | **Yare 3-6-9 Rotary Positional Encoding** | v1.0 / 4,004 | [10.5281/zenodo.20150179](https://doi.org/10.5281/zenodo.20150179) | Digital-root frequency schedule for RoPE (`VORTEX_DOUBLING_CYCLE = (1, 2, 4, 8, 7, 5)`); includes two variants with HuggingFace `inv_freq` contract compatibility. |
| **3** | **Odu-256 Training Curriculum** | v1.0 / 5,213 | [10.5281/zenodo.20150181](https://doi.org/10.5281/zenodo.20150181) | 256-state product taxonomy ($16 \text{ Meji} \times 16 \text{ Meji}$) for stratified training + evaluation, featuring a lineage-token validator preventing structural names from leaking into user strings. |
| **4** | **Frequency Abliteration with Lineage Substitution** | v1.0 / 5,037 | [10.5281/zenodo.20150193](https://doi.org/10.5281/zenodo.20150193) | Activation-space editing that substitutes ablated directions with corpus-grounded projections; rank-1 math, per-edit perplexity guard, and schema-v1 ledger. |
| **5** | **Multiverse Superposition Inference** | v1.0 / 4,573 | [10.5281/zenodo.20150195](https://doi.org/10.5281/zenodo.20150195) | Multi-path decoding with deferred collapse + externally-signal-weighted aggregation. Adapter-level implementation shipped; token-level proposed. |

---

## 🛠️ Complete System Architecture Mapping (WP-01 to WP-15)

While the papers are individually contributory, they collectively form a highly unified architectural stack for interpretable, auditable, and grounded local language models:
                  [Odu-256 Data Curriculum (WP-05)]
                                 │
                                 ▼
                 [Yare 3-6-9 RoPE Substrate (WP-10)]
                                 │
                                 ▼
            ┌─────────────────────────────────────────┐
            │     TERA Algebra Coordination Layer     │
            └────┬───────────────────────────────┬────┘
                 │                               │
                 ▼                               ▼
   [Vortex-Gated MoE (WP-01)]        [HeartScale HCRS Filter (WP-02)]
   (Zero-Param Routing Primitive)     (Inference-Time Token Filter)
                 │                               │
                 └───────────────┬───────────────┘
                                 │
                                 ▼
               [Multiverse Superposition (WP-08)]
                 │                               │
                 ▼                               ▼
   [Vortex Semantic Memory (WP-15)]  [Shell Swarm P2P Dispatch (WP-14)]

*   **WP-01 / Paper 1 (Vortex Gating):** The routing primitive. Maps how tokens are routed to specific experts deterministically using static geometric projections rather than learned softmax layers.
*   **WP-02 (HeartScale HCRS):** True alignment at inference time. Filters behavioral outcomes per-token using hidden-state geometry, eliminating the need for bulky reward models.
*   **WP-03 (Phi-Twisted Phase Scheduling):** Hardware execution framework. Tunes compute phases (INHALE/HOLD/EXHALE/EMPTY) to a golden-ratio helix, optimizing local consumer silicon.
*   **WP-04 (Lineage-Anchored Continual Learning):** Prevents model degradation using an append-only SQLite WAL as a provenance substrate to bound feedback collapse.
*   **WP-05 / Paper 3 (Odu-256 Curriculum):** The data taxonomy. A structured $16 \times 16$ archetypal space that drives data stratification and naturally mirrors the runtime routing axes.
*   **WP-06 & WP-07 (PT-CDS & Merkle-LoRA):** Cryptographically auditable weight edits and provenance verification backed by attractor patterns and Merkle chains to detect hostile data manipulation.
*   **WP-08 / Paper 5 (Multiverse Superposition Inference):** Multi-path decoding that maintains diverse inference paths and collapses them via principled signal aggregation to optimize local memory.
*   **WP-09 (Compile-Gated DPO):** Integrates compilation and execution diagnostics as direct reinforcement signals so the model learns *why* an error occurred.
*   **WP-10 / Paper 2 (Yare RoPE):** The positional substrate. Encodes position with a period-6 angle schedule to completely prevent structural degeneracies.
*   **WP-11 (Shell-Coherent Expert Specialization):** Assigns Mixture-of-Experts blocks to behavioral alignment postures instead of standard factual knowledge domains.
*   **WP-12 & WP-13 (Hardware & Coordination Runtime):** Replaces volatile message-passing with 5 native OS filesystem primitives (PID registry + path aliases) to drive lightweight, resilient local agent loops.
*   **WP-14 & WP-15 (Federated Swarms & Semantic Memory):** Drives coordinate-based decentralized P2P network routing via geometric shell affinity alongside contextual coherence retrieval based on TERA geodesic distance.

---

## 💻 Code Grounding & Implementation Artifacts

Every theoretical claim is grounded in active implementation profiles within the platform repository at `/Volumes/Abundance/maiiam-alchemist/`. Notable implementation files include:

*   **Paper 1 / WP-01:** `packages/harmonic-routing/src/vortex_gate.py` (`TERAProjection` + `meji_resolve`)
*   **Paper 2 / WP-10:** `packages/training-pipeline/training_pipeline/yare/vortex_rope.py` (`patch_rope`/`revert_rope` configurations)
*   **Paper 3 / WP-05:** `packages/aamt-foundations/yare-vortex-mathematics.json` + `seed_oracle_cards.py` + `apps/oracle/lib/odu-data.ts`
*   **Paper 4 / WP-06:** `training_pipeline/abliteration/direction_probe.py` + `stages/abliterate.py` + `concept_ledger.py`
*   **Paper 5 / WP-08:** `training_pipeline/multiverse/{parallel_train,heartscale_blender,adapter_bank}.py` + `harmonic-engine/.../multiverse_endpoint.py`

---

## 🔬 Scientific Conventions & Standards

*   **Format:** Built in Markdown (`.md`), easily convertible to LaTeX-compiled PDFs via `pandoc <file>.md -o <file>.pdf --pdf-engine=xelatex`.
*   **Math Notation:** Uses LaTeX-style standard inline `$math$` and display `$$math$$` blocks compatible with MathJax.
*   **Empirical Transparency:** Avoids fabricated benchmark numbers. All preliminary statuses are clearly marked, and upcoming v1.1 versions document full empirical runs.
*   **Academic Rigor:** Every paper features an explicit references section citing legitimate, peer-reviewed machine learning literature.
*   **Structural Naming Policy:** Where structural taxonomic labels are utilized (e.g., archetypes, Meji, Odu indicators), they are strictly defined as documentation/labels for coordinate mathematical states, not as claims of divinatory or metaphysical efficacy. Explicit disclaimers are present in Papers 3 and 4.

---

## 🛡️ Patent Strategy & Intellectual Property

The architectures and techniques documented here are subject to a strict, ongoing global intellectual property protection strategy:
1.  **Provisional Filings:** Enacted within a 90-day window covering the core TERA, Vortex, HeartScale, and Wings IP blocks.
2.  **Method Patents:** Filed for the interpretable routing primitive (Paper 1 / WP-01) and the activation-substitution mechanism (Paper 4 / WP-06).
3.  **Continuation Patents:** Actively tracking the Alchemist closed-loop self-improvement training pipeline and the activity-stream context engines.

*Preprint publication explicitly establishes priority dates for non-patentable open research contributions and reinforces the technical case during patent prosecution.*

---

## 🚀 Future Scope: Roadmap to v1.1

The upcoming **v1.1 version** of each technical preprint introduces formal empirical benchmarks across the following regimes:

*   **Paper 1 (Vortex):** Training Gemma-2-2B with Vortex routing vs. Switch Transformer + Hash Layers baselines on MMLU, HellaSwag, ARC, and TruthfulQA to evaluate routing entropy and counterfactual TERA ablations.
*   **Paper 2 (Yare RoPE):** Long-context evaluation via LongBench and Needle-in-a-Haystack benchmarks up to 128k context lengths.
*   **Paper 3 (Odu-256):** Pretraining comparative models with stratified data mixtures vs. uniform random distributions to evaluate diversity metrics.
*   **Paper 4 (Abliteration):** Running direction probes on Qwen 2.5 (3B) and LLaMA 3.2 (3B), evaluating substitution faithfulness using the Akashic Vault substrate corpus.
*   **Paper 5 (MSI):** Implementing token-level Multiverse Superposition Inference to benchmark against standard greedy, beam, and self-consistency baselines on GSM8K and HotpotQA.

---

## 🗂️ Citation Contract

If you reference this architecture or any associated preprints in academic or commercial research, please cite the permanent record Zenodo DOI. 

### APA Format Example

Whitehead, W. C. (2026). Vortex-Keyed Mixture-of-Experts Routing: A Deterministic, Interpretable Gating Primitive. AsAManThinks Technical Report, Preprint v1.0. Zenodo. [https://doi.org/10.5281/zenodo.20150167](https://doi.org/10.5281/zenodo.20150167)

BibTeX Entry
BibTeX is auto-generated and copyable on each individual paper's reader page at asamanthinks.com/research/{slug}.
@techreport{whitehead2026vortex,
  author      = {Whitehead, Weslyn Cory Jr.},
  title       = {Vortex-Keyed Mixture-of-Experts Routing: A Deterministic, Interpretable Gating Primitive},
  institution = {AsAManThinks Research},
  year        = {2026},
  type        = {Preprint v1.0},
  doi         = {10.5281/zenodo.20150167},
  url         = {[https://doi.org/10.5281/zenodo.20150167](https://doi.org/10.5281/zenodo.20150167)}
}
✉️ Contact & Communications
Author: Weslyn Cory Whitehead Jr.

Engineering Inquiries: yarethewatchman@gmail.com

Research Hub: asamanthinks.com

For strategic inquiries regarding core TERA licensing, architectural integrations, or Alchemist SDK development, please coordinate via the primary engineering contact vector above.
