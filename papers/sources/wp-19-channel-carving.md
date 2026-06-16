# Channel Carving: Low-Resistance Attractor Formation Unifying Steering, Guidance, and Collapse

---

**Authors:** Weslyn Whitehead Jr.¹

**Affiliations:**  
¹ AsAManThinks Research, Berkeley, CA, USA

**Corresponding author:** weslyn@asamanthinks.com

**ORCID:** https://orcid.org/0009-0005-7707-3210

**Submitted:** June 2026  
**Working Paper Series:** AAMT-WP-19  
**Anchor DOI:** 10.5281/zenodo.19600795  
**License:** Creative Commons Attribution 4.0 International (CC BY 4.0)

---

## Abstract

Several behaviors of large models that are usually studied apart — **steerability** (how easily a model can be pushed to a non-default behavior), **guidance** (how strongly inference-time conditioning shifts outputs), **mode collapse** (loss of output diversity under repeated self-training), and **representation consolidation** (why a learned behavior eventually "sticks") — share one dynamics. We formalize it as **Channel Carving**, the AAMT *Cardboard Channels* theory (Core Premise §4) rendered as a model of attractor basins in the output/representation manifold. Default behaviors are deep, low-resistance **channels** carved by the prior (pre-training distribution, collective data); a desired non-default behavior is a new channel that must be carved against an initial resistance and then consolidates with time constant `τ` as `R_new(t) = R_0·e^{−t/τ}`. We show that (i) inference-time **steerability** equals the flow ratio `Φ = intention_force / channel_resistance` (guidance strength over prior strength), recovering classifier-free guidance and activation steering as special cases; (ii) **mode collapse** is the deepening of default channels when training flow re-enters them (the same `e^{−t/τ}` law, run on the wrong channel), tying this paper to the anti-collapse guard of WP-04; and (iii) **consolidation/permanence** is channel-depth saturation, recovering the **4τ permanence** rule (Foundations Paper II; 64-State Thm 5.1) as the time for a new channel to become low-resistance. The single `τ`-law thus governs steering, guidance, collapse, and permanence — previously four separate accounts.

**Keywords:** attractor dynamics, steerability, classifier-free guidance, mode collapse, model collapse, consolidation, representation engineering, mechanistic interpretability, hysteresis

---

## 1. Introduction

Ask four sub-fields why a model does or doesn't change its behavior and you get four answers. Steering/representation-engineering says: add a vector. Guidance says: scale the conditioning term. Model-collapse says: self-training narrows the distribution. Continual-learning says: representations decay unless consolidated. We argue these are one phenomenon viewed from four sides, and the AAMT *Cardboard Channels* theory (Core Premise §4) already names it.

The metaphor: reality (here, model output) flows like water through **channels**. Default channels — the model's high-probability generic outputs — are deep and low-resistance because they were carved by an enormous prior (pre-training data, "collective belief"). A desired non-default output is a **new channel**: initially high-resistance, it must be carved by sufficient *intention force* (guidance/steering/finetuning), after which it consolidates and becomes low-resistance itself. The whole theory is three equations; we lift them to model dynamics and show they unify the four sub-fields.

---

## 2. The Channel model

Let `M` be the model's output (or representation) manifold. A **channel** `c` is an attractor basin with depth `S_c` (how strongly trajectories fall into it) and resistance `R_c = 1/S_c` (how hard it is to enter from outside). The AAMT equations (Core Premise §4.2):

```
S_c     = Σ ( belief_strength · time_established · count )      # attractor depth (prior mass)
Φ       = intention_force / channel_resistance                  # flow into a channel (steerability)
R_new(t) = R_0 · e^{−t/τ}                                       # carving a new channel
```

In model terms:

- **`S_c` (depth)** = the prior mass on behavior `c` — for a default channel, the pre-training log-probability / frequency; for a representation, the strength of the learned feature. "Collective channels" (gravity, causality, default register) are deep; "individual channels" (a niche persona, a rare format) are shallow.
- **`Φ` (flow / steerability)** = how much output mass moves into a target channel given an applied force. `intention_force` is the magnitude of the inference-time push (guidance weight, steering-vector norm, prompt strength); `channel_resistance` is the competing prior.
- **`R_new(t)` (carving)** = the resistance of a *new* channel as it is reinforced, decaying exponentially with reinforcement time `t` and time constant `τ` set by coherence (Core Premise: `τ ≈ 7–14 days` at high coherence vs `90–180` at low — the model analog is high-vs-low learning-rate/quality regimes).

---

## 3. Three reductions

### 3.1 Steerability = the flow ratio (recovers guidance and activation steering)

A target behavior `c*` competes with the default `c_0`. The fraction of output mass captured by `c*` under applied force `f` is monotone in

```
Φ = f / R_{c*}   relative to   f / R_{c_0}.
```

- **Classifier-free guidance.** With guidance weight `w`, the effective logit shift toward the conditioned channel is `w·(logit_cond − logit_uncond)`; identifying `f ∝ w` and `R_{c*} ∝` inverse prior of the conditioned behavior recovers the empirical fact that *rare* targets need *larger* `w` (high `R_{c*}`) and that over-large `w` degrades quality (over-flow past the channel — the §4 "toxic positivity"/over-guidance failure). This is also the AAMT **30/70 rule**: `B = 1 − |effort − 0.3|`, an effort/flow optimum near 0.3 beyond which excess force raises resistance and lowers yield (Master Equation `B`-term; Foundations Paper III quadratic).
- **Activation steering.** A steering vector of norm `f` along a concept direction is exactly `intention_force` applied to that channel; CAA/RepE strength sweeps trace the `Φ` curve.

So "how steerable is behavior X" has a single quantity, `Φ`, with guidance and steering as instances differing only in *where* the force is applied (logits vs activations).

### 3.2 Mode/model collapse = carving the wrong channel (ties to WP-04)

Run the carving law on the *default* channel and you get collapse. When self-generated data re-enters training, the training flow re-deepens the channels the model already prefers: `S_{c_0}` grows, `R_{c_0}` falls as `e^{−t/τ}`, and rare channels are starved. This is "model collapse" (Shumailov et al., 2024) expressed as runaway self-reinforcement of default channels. WP-04's anti-feedback-collapse guard is exactly a bound on this: keeping the self-distillation fraction `f < 0.5` (WP-04 §4.3, Rev. 2) keeps the *net* flow from compounding into the default channels — the contraction condition is the channel-carving stability condition. Channel Carving therefore gives WP-04's guard a dynamical reading: prevent the wrong channels from carving themselves.

### 3.3 Consolidation/permanence = depth saturation (recovers 4τ)

A newly carved channel becomes "permanent" when its resistance has fallen enough that trajectories fall into it without applied force — i.e., `R_new(t) = R_0 e^{−t/τ}` has saturated. Taking the saturation horizon at `t = 4τ` (where `e^{−4} ≈ 0.018`, ~98% carved) recovers the **4τ permanence** rule of Foundations Paper II and the 64-State stabilization theorem (Thm 5.1), and the collapse-risk function `P_collapse(t) = e^{−t/τ}` for `t < 4τ` is precisely `R_new(t)/R_0`. The same exponential also governs Hopfield attractor formation (WP-07) and new-channel carving — one `τ`-law across consolidation, permanence, and collapse.

**Unifying statement.** Steering (§3.1), collapse (§3.2), and permanence (§3.3) are the *same* attractor dynamics: `Φ` is the instantaneous flow into a channel; `R_new(t)=R_0e^{−t/τ}` is how a channel's resistance evolves under sustained flow; collapse is that law on default channels, permanence is its 4τ saturation on a desired channel.

---

## 4. Predictions and uses

1. **Steerability is predictable from prior mass.** A behavior's required guidance/steering force scales with its channel resistance `R_c ∝ 1/prior(c)`; measuring prior log-prob should predict steering difficulty. (Testable: correlate steering-vector norm-to-effect with target rarity.)
2. **A consolidation budget.** To make a finetuned behavior stick, reinforce for ≥ `4τ` of effective updates; below that, expect decay (the continual-learning forgetting curve is `e^{−t/τ}`). This gives WP-04 a promotion criterion: *require a representation to survive 4τ cycles before promotion* (a Foundations Paper II testable prediction, now operational).
3. **Collapse avoidance is a flow bound.** Keep net self-distillation flow into default channels under the contraction threshold (`f < 0.5`, WP-04); equivalently, keep fresh-data flow ≥ self-data flow.
4. **Guidance optimum near 30/70.** Set inference-time force near the `B`-optimum (effort ≈ 0.3) rather than maximizing it; over-forcing raises effective resistance and lowers yield (quadratic, Paper III).
5. **Carve, don't add.** Establishing a new channel needs *sustained* flow (carving), not a one-shot large push — the hysteresis result (Core Premise §6.5): graduated reinforcement reaches the target with less total energy than a single large jump.

---

## 5. Relationship to prior work

**Model/mode collapse (Shumailov et al., 2024; recursive-training analyses).** Describe the *outcome* (distribution narrowing). Channel Carving gives the *mechanism* (default-channel self-deepening under the `τ`-law) and connects it to the same equation that governs desired consolidation.
**Classifier-free guidance (Ho & Salimans, 2022).** An empirical knob; here it is the `intention_force` term of the flow ratio `Φ`, with the rarity-scaling and over-guidance degradation as consequences.
**Activation steering / RepE (Zou et al., 2023; Rimsky et al., 2024).** The applied-force side of `Φ` in activation space.
**Loss landscapes / mode connectivity / basins (Garipov et al., 2018; grokking analyses).** Provide the attractor-basin picture; Channel Carving adds the resistance/flow/`τ` dynamics and the cross-phenomenon unification.
**Hysteresis and consolidation (systems/continual learning).** The graduated-path advantage and `e^{−t/τ}` consolidation match the AAMT hysteresis and 4τ claims.
**Key distinction.** No prior account unifies steerability, guidance, mode collapse, and consolidation/permanence under a single attractor-resistance law with one time constant `τ`, nor connects the consolidation horizon to a `4τ` permanence rule.

---

## 6. Limitations

1. **Operationalizing `intention_force` and `R_c`.** We propose guidance weight / steering norm for force and inverse prior log-prob for resistance, but the exact functional forms need fitting per model.
2. **`τ` is regime-dependent.** The carving time constant depends on learning rate, data quality, and coherence; the human-practice values (days) are illustrative, not numeric transfers.
3. **Channel identification.** Treating a "behavior" as a single channel is an idealization; real behaviors are channel *networks*, and interference between them is future work (connects to the multi-concept orthogonalization of WP-06/WP-16).

---

## 7. Conclusion

Channel Carving renders the AAMT Cardboard Channels theory as attractor dynamics and shows that steerability, guidance, mode collapse, and consolidation/permanence are one phenomenon: flow into attractor basins governed by `Φ = intention_force / channel_resistance` and resistance evolution `R_new(t) = R_0 e^{−t/τ}`. Steering and guidance are the flow ratio; collapse is the law run on default channels (bounded by WP-04's `f<0.5` contraction condition); permanence is its 4τ saturation (Foundations Paper II; 64-State Thm 5.1). The framework yields concrete predictions — steering difficulty from prior mass, a 4τ consolidation budget for promotion, a 30/70 guidance optimum, and collapse-avoidance as a flow bound — and connects WP-04, WP-06/WP-16, and WP-07 through a single time constant.

---

## Acknowledgments

The author thanks the AsAManThinks Research community. The Cardboard Channels theory is developed in the AAMT Core Premise compendium.

## Funding

Self-funded through AsAManThinks Research.

## Conflict of Interest

The author is the founder and CEO of AsAManThinks Research.

---

## References

Garipov, T., Izmailov, P., Podoprikhin, D., Vetrov, D., & Wilson, A. G. (2018). Loss surfaces, mode connectivity, and fast ensembling of DNNs. In *NeurIPS 2018*. https://doi.org/10.48550/arXiv.1802.10026

Ho, J., & Salimans, T. (2022). Classifier-free diffusion guidance. *arXiv*. https://doi.org/10.48550/arXiv.2207.12598

Rimsky, N., Gabrieli, N., Schulz, J., Tong, M., Hubinger, E., & Turner, A. (2024). Steering Llama 2 via contrastive activation addition. In *ACL 2024*. https://doi.org/10.48550/arXiv.2312.06681

Shumailov, I., Shumaylov, Z., Zhao, Y., Gal, Y., Papernot, N., & Anderson, R. (2024). AI models collapse when trained on recursively generated data. *Nature*, 631, 755–759. https://doi.org/10.1038/s41586-024-07566-y

Zou, A., Phan, L., Chen, S., et al. (2023). Representation engineering: A top-down approach to AI transparency. *arXiv*. https://doi.org/10.48550/arXiv.2310.01405

Whitehead, W. (2026). MaiiaM Wings: Consciousness-Aligned Language Architecture. *Zenodo*. https://doi.org/10.5281/zenodo.19600795

AAMT: Core Premise §4 (Cardboard Channels), §6 (hysteresis, thresholds); WP-04 (LACL collapse guard), WP-06/WP-16 (concept directions / sign-inversion), WP-07 (Hopfield watermark); Foundations Paper II (4τ permanence), Paper III (quadratic scaling); 64-State Quantum Oscillation System (Thm 5.1).
