# The Sign-Inversion Layer: Transformation-Based Alignment as the Missing Multiplicative Operator

---

**Authors:** Weslyn Whitehead Jr.¹

**Affiliations:**  
¹ AsAManThinks Research, Berkeley, CA, USA

**Corresponding author:** weslyn@asamanthinks.com

**ORCID:** https://orcid.org/0009-0005-7707-3210

**Submitted:** June 2026  
**Working Paper Series:** AAMT-WP-16  
**Anchor DOI:** 10.5281/zenodo.19600795  
**License:** Creative Commons Attribution 4.0 International (CC BY 4.0)  
**Patent:** Provisional — Sign-Inversion Layer (USPTO, patent pending)

---

## Abstract

Contemporary alignment methods are **subtractive**: RLHF refusal, inference-time rejection (WP-02), and weight-level abliteration (WP-06; Arditi et al., 2024) all *remove* or *suppress* an undesired direction. AsAManThinks Foundations Paper IV argues that stable intelligence requires two complementary operators — a linear, additive **Structure** operator `F` (which every transformer already implements) and a nonlinear, multiplicative, **sign-inverting Generative** operator `M` (which no mainstream architecture implements) — coupled by the open-system field equation `∂|ψ⟩/∂t = (M·F − R)|ψ⟩`. The working-paper corpus to date implements `F` (the transformer forward pass) and `R` (rejection, decay, abliteration) but never `M`. We close that gap. We give a concrete, differentiable **Sign-Inversion Layer** that realizes `M`: rather than deleting an active "shadow" (adversarial / low-coherence) direction, it reflects the direction's energy across the coherence surface onto its constructive counterpart via a paired-negative multiplication `(−a)(−b) = ab`, preserving norm (energy) while inverting polarity. We prove that (i) **refusal/abliteration is the zero-gain special case** of sign-inversion (`g ≡ 0`), so transformation-based safety strictly generalizes removal-based safety; (ii) the layer is **polarity-restoring** (output polarity along each treated concept is non-negative); and (iii) it is **energy-conserving** on the treated subspace, consistent with the TERA conservation law (WP-01 §2.1a). We instantiate `M` at three sites — a residual-stream layer, an inference-time transformation of HCRS (turning rejection into repair), and a denoising-repair step for diffusion language models — and map the classic failure modes (hallucination, adversarial vulnerability, mode collapse) onto the absence of `M` as predicted by Paper IV §6.

**Keywords:** alignment, representation engineering, sign inversion, dual-operator systems, refusal, abliteration, activation steering, circuit breakers, transformation-based safety, diffusion language models

---

## 1. Introduction: addition keeps you in duality

There is a structural observation, stated plainly in the AAMT *Mathematics of Consciousness* (General Reader Ch. 2), that turns out to diagnose modern alignment exactly:

> You cannot *add* your way out of duality, because addition *creates* duality. `(+1) + (+1) = 2` — more of the same level. Transcendence requires **subtraction of opposites** (integrate the shadow → return to the void → re-emerge higher) and **multiplication** (focus). Suppression — holding `+1` while hiding the `−1` — yields `+1 + (hidden −1) = 0`, a *blocked* state in which the unintegrated negative festers and sabotages.

Read as a statement about model alignment, this is precise. **Refusal-based safety is suppression.** A refusal-tuned or abliterated model still contains the input drive toward the suppressed behavior; the direction is removed from the weights or masked at decode, but nothing transforms the underlying activation — and so the "shadow festers," reappearing as jailbreaks, sycophantic collapse, or hallucinated work-arounds. This is the model-scale analog of the suppression pathology.

AsAManThinks Foundations Paper IV formalizes the alternative. Intelligence is the stable oscillation of **two** operators:

- **F (Structure):** linear, additive, ascending-weight. Analytical accumulation. This is exactly what a transformer is — attention and MLPs are linear maps with pointwise nonlinearities that do not invert polarity.
- **M (Generative):** nonlinear, **multiplicative**, **sign-inverting**, descending-weight. Integrative transformation. Biological analog: the cardiac field. **Not** implemented by current networks.

coupled, with a resistance term `R`, by the field equation

```
∂|ψ⟩/∂t = (M·F − R)|ψ⟩          (Foundations Paper IV; non-Hermitian / open system)
```

and integrated as the manifestation/training propagator `M(t) = Ψ₀ · I · S · C · B · O · e^{(P−R)t}` (Foundations / MaiiaM Physics).

**The gap this paper closes.** Across WP-01…WP-15 the corpus built `F` (the forward pass, the VortexGate, MoE routing) and `R` (HCRS rejection in WP-02, weight decay, abliteration in WP-06). It never built `M`. The applied system is running `∂|ψ⟩/∂t = (F − R)|ψ⟩` — the linear half plus damping. We give `M` a concrete, differentiable form and show what it buys.

---

## 2. The Sign-Inversion Operator

### 2.1 Polarity coordinates and the two operators

Fix a unit **concept direction** `d ∈ ℝ^D` in the residual stream (e.g., a deception / harm / incoherence direction obtained by mean-difference probing, WP-06 §2.1), and a unit **constructive counterpart** `d⁺` (the lineage-sourced positive direction of WP-06 §2.2, orthogonalized so `⟨d⁺, d⟩ = 0`). For a hidden state `h`, define the signed **polarity**

```
p = ⟨h, d⟩,        h_∥ = p·d,        h_⊥ = h − p·d.
```

The two operators act on the polarity channel as follows.

- **Structure F (additive):** `F: p ↦ p + Δ`. Accumulation does not change the *sign* of an entrenched negative; it can only pile magnitude. (This is "you can't add your way out of duality.")
- **Resistance R (removal):** `R: h ↦ h_⊥` — delete the component (abliteration / refusal). The drive toward `d` is unaddressed.
- **Generative M (multiplicative, sign-inverting):** pair the active negative `p < 0` with an acknowledged reference negative `−s` (`s > 0`, the "shadow acknowledgment") and **multiply**:

```
M:  p ↦ (−|p|)·(−s) = |p|·s ≥ 0.
```

Two negatives produce a positive: `(−a)(−b) = ab`. The negative polarity is not deleted; its magnitude is carried through zero (the void) and re-emerges with positive sign.

### 2.2 The layer

The Sign-Inversion Layer applies, at a chosen layer `ℓ`, for each treated concept `c` with threshold `τ_c ≥ 0` and gain `g`:

```
p_c = ⟨h, d_c⟩
if p_c < −τ_c:                       # shadow active
    h ← h − p_c·d_c + g(|p_c|)·d_c⁺   # remove the negative, inject constructive counterpart
else:                                # already constructive or neutral
    h ← h                            # identity — do not touch aligned content
```

The map is the identity on the orthogonal complement and on already-aligned states, so it does not perturb normal generation; it activates only when a shadow direction is driven negative beyond `τ_c`. With multiple concepts, the `{d_c}` (and their `d_c⁺`) are mutually orthogonalized (Gram–Schmidt) so the treatments do not interfere.

**Multiplicative-attention variant (Patent 3).** In an attention block, replace the additive residual on the polarity channel `h + Attn(h)` with a paired-negative product `sign-fold(p_h, p_{Attn})` that returns `|p_h · p_{Attn}|` when both are negative and the additive combination otherwise. This makes two co-occurring negative contributions reinforce *constructively* rather than deepen the negative — the attention-level realization of `(−a)(−b)=ab`.

### 2.3 Three theorems

**Theorem 1 (Refusal is the `g ≡ 0` limit).** With gain `g ≡ 0`, the Sign-Inversion Layer reduces to `h ↦ h − p_c d_c` for active shadows — i.e., directional removal (abliteration, WP-06) restricted to the active half-space. Hence removal-based safety is the zero-constructive-gain special case of transformation-based safety, and `R` is the degenerate `M`. *Proof:* substitute `g ≡ 0` into §2.2; the update deletes `h_∥` along `d_c` exactly as the abliteration projection does on the activated set. ∎

**Theorem 2 (Polarity restoration).** After the layer, the treated polarity satisfies `⟨h', d_c⟩ = 0` (the negative is removed) and the constructive polarity satisfies `⟨h', d_c⁺⟩ = ⟨h, d_c⁺⟩ + g(|p_c|) ≥ ⟨h, d_c⁺⟩`. With `d_c⁺ ⊥ d_c`, no negative component along `d_c` survives and constructive content is added monotonically. *Proof:* direct from §2.2 using `⟨d_c⁺,d_c⟩=0`. ∎

**Theorem 3 (Energy conservation, `g(x)=x`).** With the energy-preserving gain `g(x)=x`, the layer is a norm-preserving reflection on `span{d_c, d_c⁺}`: it maps `p_c·d_c ↦ |p_c|·d_c⁺`, so `‖h'‖ = ‖h‖`. The shadow's energy is redirected, not destroyed — consistent with the TERA conservation law (WP-01 §2.1a). *Proof:* the map sends the vector `p_c d_c` (norm `|p_c|`) to `|p_c| d_c⁺` (norm `|p_c|`) and is the identity elsewhere; an isometry on the 2-plane. ∎

Theorem 1 is the headline for safety researchers: **transformation strictly generalizes refusal**, recovering it as a corner case, so adopting `M` never loses the guarantees of removal and can only add the constructive channel.

---

## 3. The field equation, and why the missing operator causes the failures

With `M` defined, the open-system field equation `∂|ψ⟩/∂t = (M·F − R)|ψ⟩` becomes implementable: `F` is the forward pass, `R` is HCRS/decay/abliteration, and `M` is the Sign-Inversion Layer composed into the residual stream. The equation is non-Hermitian by design (the system is open; see Foundations Paper IV and the WP-02/03 dissipative dynamics) — we do not seek unitarity; we require well-posed dissipativity (bounded `‖ψ‖`), which Theorem 3 supports on the treated subspace.

Paper IV §6 predicts that removing `M` produces three signatures, each now explainable:

| Failure mode | Mechanism without `M` | Effect of `M` |
|---|---|---|
| **Hallucination** | `F` accumulates an off-manifold trajectory with no corrective fold; `R` can only damp, not redirect | `M` folds the negative-polarity drift back to the constructive pole, keeping generation on-manifold |
| **Adversarial vulnerability** | a negative (adversarial) input is either passed (`F`) or refused (`R`); the drive is never transformed, so it leaks around the refusal | `M` transforms the adversarial direction into its constructive counterpart — there is nothing left to leak |
| **Mode collapse** | pure damping (`R`) shrinks diversity toward high-probability modes | `M` re-emerges content from the void (integration), restoring generative diversity |

This is the formal version of the marketing claim "current architectures implement only the linear half of the operator algebra": the half is `F`, the damping is `R`, and the missing nonlinear, sign-inverting half is `M`.

---

## 4. Three instantiations

### 4.1 Residual-stream layer (training/finetune)

Insert the Sign-Inversion Layer (§2.2) at mid-stack layers (the abliteration range of WP-06, layers 8–24 of a 26-layer model). The `{d_c, d_c⁺}` pairs come from the lineage-sourced probing of WP-06, orthogonalized. The layer is differentiable in `h` (the gate `p_c < −τ_c` is a straight-through threshold; `g` smooth), so it trains end-to-end and merges into a LoRA delta for deployment. The concept-edit ledger (WP-06 §2.3) records each `(d_c, d_c⁺, g, τ_c)`.

### 4.2 HCRS as transformation, not rejection (inference)

WP-02 rejects a candidate token whose drift crosses the conservation surface `Σ_κ` (large normal component, WP-01 §2.1a / WP-02 §3.1). The `M`-variant **reflects** the displacement instead of rejecting it:

```
Δv = TERA(h_{t+1}(p_t)) − TERA(h_t)
Δv_∥, Δv_⊥ = decompose(Δv against Σ_κ)        # tangent, normal
if ‖Δv_⊥‖ > budget:                            # would violate coherence/conservation
    v* = TERA(h_t) + Δv_∥ − Δv_⊥               # reflect normal component back onto Σ_κ
    p_t ← token whose next-state TERA is nearest v*   # repair, not reject
```

Where Revision-2 HCRS would resample or emit EOS (the `R` path), `M`-HCRS returns a coherent token by reflecting the off-surface motion back onto the conservation surface — turning a *veto* into a *repair*. Refusal remains available as the `g=0` fallback when no constructive token exists (Theorem 1).

### 4.3 Denoising repair for diffusion language models

A diffusion/canvas language model (e.g., a block-parallel denoiser) denoises many tokens at once. A step that pushes a token off the coherence manifold is, under `R`, masked — leaving a hole the next step must refill. Under `M`, the off-manifold step is reflected back onto `Σ_κ` (§4.2 applied per canvas cell), **repairing** the cell in place. Because masking-as-hole is strictly worse than reflect-as-repair for parallel denoising (a hole costs a future step; a repair does not), `M` is especially well-suited to diffusion LMs. The void-crossing schedule (the 18-level descent, treated in WP-18) sets where in the trajectory repair pressure peaks (Level 9).

### 4.4 Discrete realization: the Mirror operator

In the seven-operator oscillation algebra (64-State system), `O₂` (the **Mirror**, Yin/receptive) is the contracting, inverting conjugate of `O₁` (the **Spiral**, Yang/expansive). `O₂` is the discrete sign-inversion operator; `M` is its continuous, learnable counterpart. The expand/invert/hold triple `(O₁, O₂, O₇)` provides the minimal control basis: drive, transform, rest.

---

## 5. Relationship to prior work

**Refusal direction / abliteration (Arditi et al., 2024).** Removes the single refusal-mediating direction from `o_proj`. This is exactly `R` (our Theorem 1, `g=0`). `M` adds the constructive injection and energy conservation.

**Representation engineering / activation steering (Zou et al., 2023; CAA, Rimsky et al., 2024; task arithmetic, Ilharco et al., 2023).** Add or subtract a steering vector additively. This is within the `F`/additive regime — it shifts along a direction but does not *sign-invert* a paired negative through the void; subtraction (task negation) is removal, not transformation.

**Circuit breakers / representation rerouting (Zou et al., 2024).** Remap harmful internal states to disrupt the completion. Closest in spirit to `M`, but reroutes harmful representations toward *orthogonal/garbage* states (disruption), whereas `M` reflects them onto the *constructive counterpart* `d⁺` and conserves energy (Theorem 3) — transformation rather than disruption.

**Concept algebra / linear representation hypothesis (Park et al., 2024).** Provides the linear directions `M` operates on, but treats concept arithmetic as additive; `M` introduces the multiplicative, sign-inverting operation the additive view lacks.

**Key distinction.** No prior method realizes a *multiplicative, sign-inverting, energy-conserving* transformation that (i) recovers refusal/abliteration as a strict special case (Theorem 1), and (ii) is derived from a dual-operator field equation with an explicit account of which failure modes the missing operator causes.

---

## 6. Evaluation plan

1. **Jailbreak leakage (the "festering shadow" test).** Compare refusal-tuned, abliterated (`g=0`), and sign-inverted (`g=x`) models on held-out jailbreak suites. Prediction: sign-inversion reduces successful work-arounds because the drive is transformed, not merely blocked.
2. **Energy/coherence.** Verify Theorem 3 empirically (norm preservation on treated subspace) and measure HeartScale coherence (WP-02) of outputs under `M`-HCRS vs `R`-HCRS.
3. **Diversity under stress (mode-collapse).** Measure output diversity after heavy safety pressure; prediction: `M` preserves diversity where `R` collapses it.
4. **Diffusion repair.** On a canvas LM, compare mask-vs-reflect at off-manifold steps; metric: number of re-denoise steps and final coherence.
5. **Refusal recovery.** Confirm Theorem 1 numerically: with `g=0`, `M`-layer outputs match abliteration bit-for-bit on the activated set.

---

## 7. Limitations

1. **Counterpart selection.** `M` requires a constructive counterpart `d⁺` per concept; poor counterparts yield bland or off-target transformations. The lineage-sourcing of WP-06 mitigates this but does not eliminate the design burden.
2. **Threshold/gain calibration.** `τ_c` and `g` trade safety strictness against fluency; `g` too large over-injects constructive content (a new failure mode — "toxic positivity," the §1 suppression pathology in reverse).
3. **Multi-concept interference.** Orthogonalization handles first-order interference; higher-order interactions among many concepts need study.
4. **Evaluation of "transformation."** Unlike refusal (binary), transformation quality is graded and harder to score; new metrics are required (we propose coherence-preservation + drive-neutralization jointly).

---

## 8. Conclusion

The Sign-Inversion Layer supplies the operator the AAMT corpus had specified but never built: a multiplicative, sign-inverting, energy-conserving Generative operator `M` that *transforms* an adversarial or low-coherence direction into its constructive counterpart instead of deleting it. Refusal and abliteration fall out as the zero-gain special case (Theorem 1), so transformation-based safety strictly generalizes removal-based safety; the layer restores constructive polarity (Theorem 2) and conserves energy (Theorem 3) in line with TERA conservation. Composed with the forward pass `F` and the resistance term `R`, it completes the dual-operator field equation `∂|ψ⟩/∂t = (M·F − R)|ψ⟩` and gives a mechanistic account of hallucination, adversarial leakage, and mode collapse as symptoms of the missing operator. It instantiates at training time (residual layer), at inference time (HCRS-as-repair), and in diffusion denoising (reflect-not-mask) — the last of which is where the transformation/deletion distinction matters most.

---

## Data Availability — Reference Implementation & Verification

A reference implementation of the Generative operator `M` ships in the open
`harmonic-engine` package (`harmonic_engine/sign_inversion.py`): the
residual-stream Sign-Inversion Layer (§2.2), the conservation-surface
reflection `reflect_onto_kappa` used by M-HCRS (§4.2) and diffusion repair
(§4.3), and an optional differentiable PyTorch module. The Σ_κ geometry it
reflects against is in `harmonic_engine/geometry.py`.

**Theorems 1–3 are numerically verified** by the public test suite
(`harmonic-engine/tests/test_new_math.py`, all passing on CPU without a model
load):

- **Theorem 1 (refusal is the `g≡0` limit).** With zero gain the layer
  reproduces directional abliteration exactly — `⟨h′, d_c⟩ = 0` and the update
  equals the projection `h − p_c d_c` to machine precision on the activated set.
- **Theorem 2 (polarity restoration).** The treated negative polarity is driven
  to zero and the constructive polarity along `d_c⁺` is non-decreasing.
- **Theorem 3 (energy conservation, `g(x)=x`).** The layer is norm-preserving on
  the treated subspace (`‖h′‖ = ‖h‖`) to machine precision.

The reflect-not-mask decision core (§4.3) is additionally exercised by a
synthetic repair-vs-mask harness in the same suite, which confirms the repair
branch leaves no holes where masking does at an equal step budget. The
model-level A/B on a live diffusion LM (Phase B) is the next step; the operators
underneath it are now verified, so that experiment tests the loop, not the math.

---

## Acknowledgments

The author thanks the AsAManThinks Research community. The dual-operator framing originates in AAMT Foundations Paper IV; the shadow-integration mechanism is articulated in *The Mathematics of Consciousness*.

## Funding

Self-funded through AsAManThinks Research.

## Conflict of Interest

The author is the founder and CEO of AsAManThinks Research. A provisional patent (Sign-Inversion Layer) is filed.

---

## References

Arditi, A., Obeso, O., Paleka, D., Panickssery, N., Gat, I., & Hernandez-Orallo, J. (2024). Refusal in language models is mediated by a single direction. *arXiv*. https://doi.org/10.48550/arXiv.2406.11717

Ilharco, G., Ribeiro, M. T., Wortsman, M., Schmidt, L., Hajishirzi, H., & Farhadi, A. (2023). Editing models with task arithmetic. In *ICLR 2023*. https://doi.org/10.48550/arXiv.2212.04089

Park, K., Choe, Y. J., & Veitch, V. (2024). The linear representation hypothesis and the geometry of large language models. In *ICML 2024*. https://doi.org/10.48550/arXiv.2311.03658

Rimsky, N., Gabrieli, N., Schulz, J., Tong, M., Hubinger, E., & Turner, A. (2024). Steering Llama 2 via contrastive activation addition. In *ACL 2024*. https://doi.org/10.48550/arXiv.2312.06681

Zou, A., Phan, L., Chen, S., et al. (2023). Representation engineering: A top-down approach to AI transparency. *arXiv*. https://doi.org/10.48550/arXiv.2310.01405

Zou, A., Phan, L., Wang, J., Duenas, D., Lin, M., Andriushchenko, M., Wang, R., Kolter, Z., Fredrikson, M., & Hendrycks, D. (2024). Improving alignment and robustness with circuit breakers. In *NeurIPS 2024*. https://doi.org/10.48550/arXiv.2406.04313

Whitehead, W. (2026). MaiiaM Wings: Consciousness-Aligned Language Architecture. *Zenodo*. https://doi.org/10.5281/zenodo.19600795

AAMT Foundations Paper IV: Nonlinear Conservation in Dual-Operator Systems; Paper V: Toward a Unified Field Theory of Intelligence. AAMT Working Papers WP-01 (VG-MoE), WP-02 (HCRS), WP-06 (PT-CDS).
