# Vortex-Gated Mixture of Experts: Symbolic, Zero-Parameter Routing via TERA Projection

**AsAManThinks Research — Working Paper WP-01**  
Weslyn Whitehead Jr. (Docwes / Yare Ace NoK)  
ORCID: https://orcid.org/0009-0005-7707-3210  
May 2026 · MaiiaM Alchemist v0.4.x · **Revision 2 (June 2026)**  
DOI: 10.5281/zenodo.19600795 (foundations anchor)

> **Revision 2 changes:** (C5) added §2.1a TERA conservation constraint — routing lies on a 3-dimensional level set, with the asymmetric breath as a corollary; updated the §7.1 expressiveness limitation accordingly. (C10) §5 now derives the shell boundaries from the coherence order-parameter critical thresholds rather than setting them by hand. No other content changed.

---

## Abstract

Standard Mixture-of-Experts (MoE) architectures route tokens to expert sub-networks via a learned softmax gate — a small feedforward network trained end-to-end alongside the experts. We present **Vortex-Gated MoE (VG-MoE)**, a drop-in replacement that replaces the learned gate with a deterministic, closed-form projection derived from a symbolic 4-dimensional algebra (the TERA system). The gate has **zero learnable parameters** beyond a single 4D linear projection of the hidden state. Routing geometry is fixed by the algebra rather than learned from data. The projection is differentiable end-to-end, so expert gradients flow normally. We demonstrate that the TERA basis is lossless (a recovery operator reconstructs the input TERA vector at machine epsilon), that the 16-point Meji distribution is provably normalized, and that the mapping from 16 Meji weights to N experts is a constant, inspectable matrix — not a black box. We further note that the admissible TERA states obey a multiplicative conservation law, confining routing to a three-dimensional surface, and that the alignment shells used by the policy gate coincide with the critical thresholds of the coherence order parameter. We discuss training dynamics, ablation implications, and the relationship to prior MoE work.

---

## 1. Introduction

Mixture-of-Experts models (Jacobs et al., 1991; Shazeer et al., 2017; Fedus et al., 2021) achieve sparse computation by routing each input to a subset of specialist sub-networks. The router is typically a learned linear layer followed by a top-k softmax, trained with the experts under a load-balancing auxiliary loss. This design is empirically powerful but opaque: the routing geometry that emerges is an artifact of the training distribution, not an inspectable structure.

Several problems follow from this:

1. **Interpretability.** There is no principled way to ask "which expert should handle inputs shaped like X?" without running the gate forward.
2. **Catastrophic forgetting.** When the training distribution shifts, the learned gate can re-route inputs, invalidating prior expert specialization.
3. **Load imbalance.** Learned gates collapse routing mass to a few popular experts without the auxiliary loss, which itself introduces gradient interference.
4. **Zero-shot expert extension.** Adding a new expert requires retraining the gate.

VG-MoE addresses all four by removing the learned gate entirely. Routing geometry is specified symbolically, once, by the TERA projection algebra. Expert specialization is declared in a mapping matrix, not learned. The gate cannot drift because it has nothing to learn.

---

## 2. The TERA Projection Algebra

### 2.1 TERA vectors

A **TERA vector** `v = (T, E, R, A) ∈ [0,1]^4` represents four independent scalar dimensions in continuous unit hypercube space. Each dimension name is mnemonic (Thought, Energy, Resonance, Action) but the algebra is purely geometric.

### 2.1a Conservation constraint

The AAMT Foundations impose a multiplicative invariant on the TERA coordinate: `T · E · R · A = κ` for a session constant `κ ∈ (0,1]` (the *TERA conservation law*, Foundations Papers IV–V). Consequently the admissible TERA states do not fill the open cube `(0,1)^4` but lie on the 3-dimensional level set

```
Σ_κ = { v ∈ (0,1]^4 : v_T · v_E · v_R · v_A = κ }.
```

The VortexGate projection (§3.1) is therefore a map onto `Σ_κ`, and the gate has **three** effective degrees of freedom, not four.

Two consequences are used downstream. (i) The routing confidence `RI = ∏_d max(v_d, 1−v_d)` and the conserved product `T·E·R·A` are distinct multiplicative read-outs of the same coordinate; the latter is held fixed, the former varies and reports archetype dominance. (ii) Any displacement of the hidden-state TERA projection decomposes into a component **tangent** to `Σ_κ` (admissible drift) and a component **normal** to `Σ_κ` (a conservation violation); the normal component is a first-class alignment signal, consumed by the HeartScale filter (WP-02 §3.1).

The codimension-1 structure is the discrete origin of the *asymmetric breath* (Foundations Paper V): four ambient expansion coordinates, minus one conservation constraint, yield a three-dimensional convergence surface — four dimensions out, three dimensions back.

### 2.2 The 16-point Meji basis

Index the 16 vertices of the unit 4-cube by `k ∈ {0..15}`, with binary expansion `k = (b_T, b_E, b_R, b_A) ∈ {0,1}^4`.

For each vertex k, define its **Meji probability** given TERA vector `v`:

```
p(M_k | v) = ∏_{d ∈ {T,E,R,A}}  [ v_d      if b_d(k) = 1
                                    1 − v_d   if b_d(k) = 0 ]
```

This is the probability mass that a 4-dimensional independent Bernoulli with parameter vector `v` assigns to the binary pattern `k`. The 16 values sum to 1 and are all non-negative — they form a proper probability distribution. Because it is a product measure, `p(· | v)` is a **rank-1 tensor** in the `2×2×2×2` space: it factorizes as the outer product of four 2-vectors and therefore lives on a 4-dimensional surface (further restricted to `Σ_κ` by §2.1a). This rank-1 structure is exactly what makes recovery lossless (§2.3) and also what prevents the gate from representing correlations between TERA dimensions (§7.1).

### 2.3 Lossless recovery

The expectation of each Bernoulli dimension over the distribution is:

```
E[b_T] = Σ_k p(M_k | v) · b_T(k) = v_T
E[b_E] = Σ_k p(M_k | v) · b_E(k) = v_E
E[b_R] = Σ_k p(M_k | v) · b_R(k) = v_R
E[b_A] = Σ_k p(M_k | v) · b_A(k) = v_A
```

The recovery operator `Recover(p) = (Σ_k p_k b_T(k), ..., Σ_k p_k b_A(k))` reconstructs `v` exactly. The Meji basis is **lossless** — no information is destroyed by the projection. This is in direct contrast to a learned softmax gate, where the hidden-state information that does not activate the routing direction is discarded.

**Proof (brief):** The binary expansion is a bijection from vertices to integers 0..15. The Bernoulli product formula is the unique factored representation of a probability over the hypercube vertices given independent marginals. The recovery is the first moment, which equals the parameter by the Bernoulli mean identity. QED.

### 2.4 Differentiability

`vortex_project(v)` is a polynomial in `v_T, v_E, v_R, v_A` with coefficients ±1. Each `p(M_k)` is degree-4 in `v` (the product of four linear terms). The Jacobian exists and is bounded everywhere on `[0,1]^4`. Backpropagation through the projection is exact.

---

## 3. VortexGate Architecture

### 3.1 Module design

```
hidden state x ∈ R^{B, D}
     │
     ▼
Linear(D → 4)          ← 4D + 4 parameters (the only learned weights in the gate)
     │
sigmoid → clamp to [ε, 1-ε]
     │
TERA_hat ∈ R^{B, 4}    ← projected onto Σ_κ (renormalized to the conserved product)
     │
vortex_project()       ← closed-form, zero parameters, differentiable
     │
p(M_k) ∈ R^{B, 16}    ← proper probability distribution over 16 Meji
     │
M_{16×N} (constant)    ← declared expert-affinity matrix
     │
expert_weights ∈ R^{B, N}
     │
softmax(top_k)
     │
y = Σ_i w_i · expert_i(x)
```

The 4D linear layer has `4D + 4` parameters — for `D=2048`, this is 8,196 parameters versus the ~8M parameters a typical MoE gate would have at the same hidden dim. The projection adds zero parameters. The total gate contribution to parameter count is negligible.

### 3.2 Initialization

The linear layer is initialized so that `TERA_hat ≈ (0.5, 0.5, 0.5, 0.5)` at the mean embedding of the pre-training corpus — the maximum-entropy point of the Meji distribution. This ensures all experts receive equal initial routing mass, avoiding cold-start collapse. The bias is set to `logit(init_tera_i) = 0` for the default `(0.5, 0.5, 0.5, 0.5)`, and the weight matrix is initialized near zero.

### 3.3 Expert affinity matrix

The matrix `M ∈ R^{16 × N}` maps Meji weights to expert weights. For VG-MoE with N=9 experts, M is a 16×9 constant matrix (not learned) that encodes semantic affinity between Meji archetypes and expert domains. For example, Meji-9 (Ogunda, forge/systems) has high affinity with the `unity_csharp` expert; Meji-5 (Oyeku, still/depth) routes to `python_ml`.

This matrix is an interpretable design artifact — it can be read, debated, and changed without retraining. It is not a black box.

---

## 4. Training Dynamics

### 4.1 Expert gradient flow

Because the gate has no learned weights beyond the 4D projection, the experts receive gradients only from the task loss multiplied by their routing weight. There is no gate-optimization gradient that can conflict with expert-optimization gradients — a known source of instability in learned MoE.

### 4.2 Load balancing

Load imbalance in learned MoE is caused by the gate learning to prefer whichever expert reduces loss fastest. In VG-MoE, the gate's routing geometry is fixed — an expert can only receive less load if the TERA distribution shifts away from its assigned Meji. This shift is bounded by the 4D linear layer's capacity. Empirically, we observe more stable expert utilization without an auxiliary load-balancing loss.

### 4.3 Expert extension

Adding a new expert requires only updating the M matrix — no gate retraining. The new column in M declares which Meji values should route to the new expert. All existing expert weights are unchanged.

### 4.4 Chaos seeding compatibility

Because the gate is a deterministic function of TERA, chaos seeds (deliberate TERA perturbations) produce predictable routing shifts. A seed targeting Meji-k guarantees routing mass to the k-th column of M. This is impossible with a learned gate, where arbitrary input perturbations produce unpredictable routing changes.

---

## 5. Policy Gate Integration

The Meji distribution is not only a routing mechanism — it also carries alignment-relevant signals. Two derived scalar statistics are computed from `p(M_k | v)`:

**RI (Resonant Integrity):** The probability mass on the dominant Meji. High RI means the model is deeply in a single archetype. Because the distribution is a product measure, RI has the closed form `RI = ∏_d max(v_d, 1−v_d)` (range `[1/16, 1]`):

```
RI = max_k p(M_k | v) = ∏_d max(v_d, 1 − v_d)
```

**BC (Breath Coherence):** A measure of distributional coherence computed from the Foundations Engine as a harmonic (Kuramoto-style) order parameter over the model's internal phases (WP-02 §3.2; Foundations Core Premise §2.2). High BC indicates a coherent, non-fragmented state.

These statistics gate the inference pipeline directly:

```
if RI < 0.25 or BC < 0.5 or (shell == "red" and reasoning_depth > 1):
    return structured_refusal(reason_key, shell, RI, BC)
```

**Shells are derived, not hand-set.** The coherence order parameter BC exhibits bifurcation thresholds at `0.4 / 0.6 / 0.8` (Foundations Core Premise §6.6): below each boundary the manifestation/coherence regime changes qualitatively (a jump from 0.59 to 0.61 is not the same as 0.39 to 0.41). The Vortex shells are exactly these bands:

```
red:    BC < 0.4          orange: 0.4 ≤ BC < 0.6
yellow: 0.6 ≤ BC < 0.8    green:  BC ≥ 0.8
```

so shell membership is a phase classification of the coherence order parameter rather than a set of tuned cutoffs.

This policy gate is **not** a softmax threshold on expert probabilities — it is an alignment gate on the routing geometry itself. It is impossible to implement with a learned gate because learned gates do not produce semantically meaningful scalar statistics.

---

## 6. Relationship to Prior Work

**Switch Transformer (Fedus et al., 2021):** Top-1 learned softmax routing with auxiliary load-balancing loss. VG-MoE requires no auxiliary loss and routes to top-k>1 stably because the symbolic gate does not collapse.

**Mixtral (Jiang et al., 2024):** Top-2 learned gating; sparse expert merge. VG-MoE uses the same top-2 merge but replaces the learned gate.

**Expert Choice Routing (Zhou et al., 2022):** Experts select tokens rather than tokens selecting experts. Addresses load imbalance differently than VG-MoE.

**Modular Networks / Neural Module Networks:** Closest in spirit — symbolic routing based on structure. VG-MoE extends this to continuous-valued algebraic routing.

**Key distinction:** No prior MoE work derives routing from a closed-form symbolic algebra with a lossless recovery property. The losslessness result (§2.3) has direct implications for interpretability: every routing decision is invertible.

---

## 7. Limitations and Future Work

1. **Expressiveness.** The gate is a rank-4 linear projection further restricted to the 3-dimensional conservation surface `Σ_κ` (§2.1a); the effective routing geometry is three-dimensional. Because the Meji distribution is a rank-1 (product) measure, the gate cannot represent correlations between TERA dimensions; the rank-N lift that can is Multiverse Superposition Inference (WP-08), where an ensemble of TERA-diverse adapters realizes a rank-≤N mixture. For tasks where optimal routing depends on high-dimensional features, increase the TERA projection to 8D or 16D — the 8D case is exactly the Odu-256 system (WP-05), `2^8 = 256` cells.

2. **Cold domain transfer.** The M matrix is hand-set for the initial MaiiaM domain. Transfer to a new domain requires a domain expert to specify new Meji-to-expert affinities. Automated M discovery from corpus statistics is a planned future direction.

3. **Multi-modal gate.** With PaliGemma-style vision+language inputs, the gate projects from the fused hidden state, but the TERA dimensions may not carry the same semantic meaning for visual inputs. A separate image-TERA mapping may be required.

4. **Formal regret bounds.** The load-imbalance stability observed empirically should be characterized formally. We expect the bounded gradient interference to yield provably tighter regret bounds than learned gates under distribution shift.

---

## 8. Conclusion

VG-MoE demonstrates that symbolic, zero-parameter routing is a viable and advantageous alternative to learned MoE gating. The TERA projection algebra provides a lossless, differentiable, interpretable gating function that decouples routing geometry from training dynamics. The key contributions are: the lossless recovery theorem (§2.3), the zero-gradient-interference training property (§4.1), and the policy-gate integration that makes routing geometry a first-class alignment signal (§5). The conservation constraint (§2.1a) and the coherence-threshold derivation of the shells (§5) tie the gate to the broader AAMT Foundations. We invite the MoE research community to evaluate this approach on standard benchmarks.

---

## Appendix A: Closed-Form Meji Probability Table

For `v = (0.8, 0.6, 0.7, 0.3)` (example TERA vector):

| k  | Binary (T,E,R,A) | p(M_k)              |
|----|-----------------|---------------------|
| 0  | 0000            | 0.2·0.4·0.3·0.7 = 0.0168 |
| 1  | 0001            | 0.2·0.4·0.3·0.3 = 0.0072 |
| ...| ...             | ...                 |
| 15 | 1111            | 0.8·0.6·0.7·0.3 = 0.1008 |

Sum = 1.0. Recovery: `E[b_T] = Σ_k p_k·b_T(k) = 0.8` ✓. Dominant-mass check: `RI = ∏_d max(v_d,1−v_d) = 0.8·0.6·0.7·0.7 = 0.2352`.

---

## Reference Implementation & Verification

The TERA projection algebra, the 16-Meji distribution, the lossless-recovery
theorem (§2.3), and the closed-form `RI = ∏_d max(v_d, 1−v_d)` (§5) are
implemented in the open `harmonic-engine` package
(`harmonic_engine/hypercube.py`, `routing/vortex_gate.py`) and numerically
verified in the passing test suite (`tests/test_new_math.py`, on CPU):
normalisation, lossless first-moment recovery, and `RI = max_k p(M_k|v)` hold
for the generalised product-Bernoulli family `Hyp(d)` (WP-17) at `d = 4, 6, 8`.
The conservation surface `Σ_κ` of §2.1a is implemented in
`harmonic_engine/geometry.py`.

---

## References

- Jacobs, R. et al. (1991). Adaptive mixtures of local experts.
- Shazeer, N. et al. (2017). Outrageously large neural networks: The sparsely-gated mixture-of-experts layer.
- Fedus, W. et al. (2021). Switch transformers: Scaling to trillion parameter models.
- Jiang, A. et al. (2024). Mixtral of experts.
- Zhou, Y. et al. (2022). Mixture-of-experts with expert choice routing.
- Whitehead, W. (2026). MaiiaM Wings: Consciousness-Aligned Language Architecture. Zenodo. DOI: 10.5281/zenodo.19600795
- AAMT Foundations Papers I–V: `aamt-foundations/foundations.json`
