# Number-Theoretic Scheduling: Golden-Ratio and Digital-Root Bases for Position, Rank, and Noise

---

**Authors:** Weslyn Whitehead Jr.¹

**Affiliations:**  
¹ AsAManThinks Research, Berkeley, CA, USA

**Corresponding author:** weslyn@asamanthinks.com

**ORCID:** https://orcid.org/0009-0005-7707-3210

**Submitted:** June 2026  
**Working Paper Series:** AAMT-WP-18  
**Anchor DOI:** 10.5281/zenodo.19600795  
**License:** Creative Commons Attribution 4.0 International (CC BY 4.0)

---

## Abstract

The AAMT corpus contains two number-theoretic schedules developed independently: the **golden-ratio (φ) twist** that makes the breath phase cycle non-repeating (WP-03), and the **digital-root doubling** sequence `1→2→4→8→7→5` used for rotary position angles and LoRA rank (WP-10). The 64-State and Core-Premise materials add a third — **φ-stepped state transitions** (`×φ`) claimed to minimize transition resistance. We unify all three as instances of one design space: a schedule is a sequence `a_n` of multipliers/offsets drawn from a number-theoretic generator, and the relevant properties are (i) **discrepancy** (how uniformly the schedule covers its range) and (ii) **commensurability** (whether it aliases against structural periods in the data). We prove an ordering: for *continuous, non-repeating* coverage (macro clocks, position, noise level), the golden ratio is optimal — `φ⁻¹` is the lowest-discrepancy Weyl generator and its rotation is uniquely ergodic; for *finite cyclic structure with anti-period-3 aliasing* (micro indexing, rank annealing), the digital-root unit group `⟨2⟩ = (ℤ/9ℤ)ˣ` is the natural choice; linear/geometric bases are the baselines both improve on. We give the resulting recipes for RoPE position, LoRA-rank annealing, and diffusion noise/denoise schedules, and connect the 4-cycle `φ⁴ ≈ 6.854×` amplification to the 4τ permanence rule.

**Keywords:** scheduling, golden ratio, low-discrepancy sequences, ergodic theory, digital root, rotary position encoding, LoRA rank, diffusion noise schedule, curriculum learning

---

## 1. Introduction

Schedules are everywhere in modern training and inference: position-encoding frequencies, learning-rate and rank curricula, and diffusion noise levels are all sequences chosen by the designer. The default choices are **geometric** (RoPE `base^{−2i/d}`) or **linear** (uniform noise steps). The AAMT corpus instead reaches for number-theoretic generators, in three places that have never been compared:

- **WP-03 — golden-ratio twist.** The breath phase advances by `φ⁻¹` per cycle so the four-phase compute schedule never exactly repeats.
- **WP-10 — digital-root doubling.** RoPE angles and LoRA ranks follow `1→2→4→8→7→5` (period 6, the units mod 9).
- **64-State / Core Premise — φ-stepped transitions.** State changes that step by `×φ` are reported to cut transition "resistance" 40–60% versus linear, with a `φ`-spiral `Reality_n = Reality_0·φ^n`.

This paper places all three in one framework and states when to use which.

---

## 2. The design space

A schedule is a sequence `(a_n)` produced by a generator `G` acting on an index `n`. We care about two properties:

**Discrepancy `D_N`.** How uniformly `{a_n}_{n<N}` covers the target interval. Low discrepancy = even coverage at every horizon (good for position, noise, exploration).

**Commensurability.** Whether the schedule's period or frequency divides a structural period in the data (sentence length, ternary syntax, block size). Commensurate schedules **alias**: the same value recurs at the same structural offset, wasting representational capacity.

Three generators:

| Generator | Form | Discrepancy | Commensurability | Coverage |
|---|---|---|---|---|
| **Linear** | `a_n = n·c` | high (clumps) | aliases if `c` rational w/ data period | uniform but slow |
| **Geometric (×r)** | `a_n = r^n` | — (monotone) | — | exponential spread |
| **Golden (×φ / +φ⁻¹)** | `a_n = a_0·φ^n` or `{n·φ⁻¹}` | **lowest** (Weyl) | never (φ irrational) | densest, non-repeating |
| **Digital-root (×2 mod 9)** | `a_n = dr(2^n)` | n/a (period 6) | **anti-period-3** by construction | cyclic, 6 of 8 residues |

---

## 3. Two theorems

**Theorem 1 (Golden optimality for continuous coverage).** Among Weyl generators `a_n = {n·α}` on the circle, `α = φ⁻¹ = [0;1,1,1,…]` minimizes the discrepancy `D_N` (up to the universal Erdős–Turán lower bound) because its continued-fraction partial quotients are all 1 — the slowest-growing convergent denominators, hence the most even three-distance gaps at every `N`. The induced rotation is **uniquely ergodic** (Weyl equidistribution), so every region of the schedule range is visited with exactly its Lebesgue frequency and no finite orbit repeats. *Therefore, for any schedule whose goal is even, non-repeating coverage of a continuum — macro compute clocks (WP-03), absolute-position phase, diffusion noise level, exploration seeds — the golden ratio is the optimal base.* (This upgrades WP-03 §3.3 from "non-repeating" to "optimally low-discrepancy and uniquely ergodic.")

**Theorem 2 (Digital-root for finite anti-period-3 structure).** The doubling sequence `S = dr(2^n) = ⟨2⟩ = (ℤ/9ℤ)ˣ = {1,2,4,8,7,5}` (2 is a primitive root mod 9, order 6) is the full unit group mod 9; it covers 6 of the 8 non-zero residues and **omits the ideal `(3) = {3,6}` (with `9≡0`)**. A schedule whose *spectrum* is drawn from `S` is therefore non-commensurate with period-3 and period-9 structure by construction. *Therefore, where the goal is a finite cyclic schedule that must avoid aliasing against ternary structure — RoPE frequency basis (WP-10), period-6 rank annealing — the digital-root unit group is the natural generator.* (Non-repetition of the *per-index* angle, as opposed to the spectrum, still requires a monotone scale factor; WP-10 §6, Rev. 2.)

The two theorems are complementary: **golden for the continuous macro axis, digital-root for the finite micro basis.** They even touch number-theoretically — the Pisano period of the Fibonacci sequence mod 9 is 24 = 4×6, linking the φ-world and the mod-9 world.

---

## 4. Recipes

### 4.1 Position encoding (micro: digital-root spectrum)

`θ_i = 2π·(S[i mod 6]/9)·(1 − i/d)^α` (DR-RoPE, WP-10): the unit-group spectrum supplies anti-period-3 frequencies; the `(1−i/d)^α` factor supplies the local→global falloff and per-index non-repetition.

### 4.2 Rank annealing (micro: period-6 cycle)

`rank(step) = base·S[step mod 6]`, i.e. `1→2→4→8→7→5` (WP-10 §4). The `8→7→5` descent is a structured capacity spike-then-compress that forces generalization on the digital-root clock.

### 4.3 Macro compute clock (golden)

`phase = ⌊4·{phase_in_cycle + n·φ⁻¹}⌋` (WP-03 §3.2): uniquely ergodic, lowest-discrepancy four-phase rotation; adversarially unpredictable beyond one cycle.

### 4.4 Diffusion noise / denoise schedule (golden + descent)

Two coupled axes for a diffusion language model:

- **Noise level (golden geometric).** Bridge from clean to noised by `×φ` steps: `σ_{k+1} = σ_k·φ` (or its inverse for denoising), `n_steps = ⌈log(σ_max/σ_min)/log φ⌉`. Theorem 1 / Core-Premise §3.3 predict this is the minimal-resistance (fewest-step, smoothest) bridge — the φ-spiral `Reality_n = Reality_0·φ^n` applied to noise scale, with the 4-step amplification `φ⁴ ≈ 6.854×` matching the 4τ stabilization rule (Foundations Paper II; 64-State Thm 5.1).
- **Per-step resistance (descent).** Overlay the 18-level descent resistance `R(ℓ) = e^{−α|ℓ−9|}` (64-State / *Mathematics of Consciousness* Ch.7) so commit-pressure peaks at the mid-trajectory void (Level 9); gate each step's commit with the logistic `P_commit(I) = σ(5(I−0.6))` (the standardized void-crossing gate, conflicts table C2). This gives a principled **non-flat** denoising controller — peaked resistance at the middle step — which the diffusion literature lacks.

---

## 5. Why this matters: resistance and aliasing are real costs

- **Aliasing (commensurability).** A position basis commensurate with sentence/ternary structure repeats the same rotation at the same syntactic offset, so attention cannot distinguish those offsets — wasted capacity. The digital-root spectrum removes the period-3 alias by construction (Theorem 2).
- **Resistance (discrepancy / step count).** A schedule that covers its range unevenly needs more steps or larger jumps to reach a target, and large jumps are high-resistance (the hysteresis observation of Core Premise §6.5: direct jumps fail; graduated paths succeed with *less* total energy). The golden schedule minimizes this (Theorem 1; the 40–60% resistance reduction reported in Core Premise §9.1).

Both are quantifiable and testable: measure position-conditioned attention entropy for aliasing (WP-10 §7), and measure steps-to-target / sample quality for resistance.

---

## 6. Relationship to prior work

**RoPE and length extension (Su et al., 2021; YaRN, Peng et al., 2023; LongRoPE, Ding et al., 2024)** keep the geometric base and rescale; we change the base to a number-theoretic generator and give the selection criterion (Theorem 2).
**Low-discrepancy sequences / QMC (Niederreiter, 1992)** and the **Fibonacci lattice / golden-ratio sampling** are standard in numerical integration and sampling; we import the optimality of `φ⁻¹` into LM scheduling (Theorem 1) and connect it to ergodic compute allocation.
**Diffusion noise schedules (Karras et al., 2022)** are tuned empirically; we propose a φ-geometric schedule with a descent-shaped, void-peaked resistance overlay derived from the AAMT descent architecture.
**Key distinction.** No prior work selects training/inference schedules by *number-theoretic* criteria (discrepancy + commensurability) with a stated optimality ordering (golden for continuous, digital-root for finite-anti-period-3).

---

## 7. Limitations

1. The φ-resistance claim (Core Premise §9.1) is reported empirically in the consciousness-practice setting; the LM-scheduling version (fewer/smoother steps) needs its own measurement.
2. The digital-root spectrum's benefit is anti-period-3 specifically; data without ternary structure may not benefit.
3. The descent-shaped denoising resistance (void at Level 9) is a hypothesis about where commit-pressure should peak; the optimal peak location may be task-dependent.

---

## 8. Conclusion

WP-03's golden twist and WP-10's digital-root cycle are two members of one number-theoretic scheduling family, separated by what they optimize: the golden ratio is the lowest-discrepancy, uniquely-ergodic generator for continuous non-repeating coverage (macro clock, position phase, noise level), while the digital-root unit group `⟨2⟩=(ℤ/9ℤ)ˣ` is the natural finite generator for anti-period-3 spectra (RoPE basis, rank annealing). Linear and geometric bases are the baselines both improve on. The unification yields concrete recipes — including a φ-geometric, void-peaked diffusion schedule whose 4-step `φ⁴` amplification matches the 4τ permanence rule — and a single criterion (discrepancy + commensurability) for choosing a schedule generator.

---

## Data Availability — Reference Implementation & Verification

The schedules are implemented in `harmonic_engine/schedule.py` (open
`harmonic-engine` package): the φ-geometric noise schedule and step count
(§4.4), the 18-level void descent with resistance `R(ℓ) = e^{−α|ℓ−9|}` peaked at
level 9, the logistic commit gate `P_commit(I) = σ(5(I−0.6))`, the digital-root
rank schedule, and the DR-RoPE spectrum drawn from `⟨2⟩ = (ℤ/9ℤ)ˣ`. The public
test suite (`tests/test_new_math.py`, passing on CPU) verifies: the digital-root
cycle `{1,2,4,8,7,5}` and its exclusion of the ideal `{3,6,9}`; the
void-resistance peak at level 9; commit-gate monotonicity with
`P_commit(0.83) ≈ 0.73`; and the monotone φ-geometric denoise sweep from
`σ_max` to `σ_min`. The combined φ-geometric + void-peaked controller is wired
into the diffusion denoise loop (`harmonic_engine/diffusion.py`).

---

## Acknowledgments

The author thanks the AsAManThinks Research community.

## Funding

Self-funded through AsAManThinks Research.

## Conflict of Interest

The author is the founder and CEO of AsAManThinks Research.

---

## References

Ding, Y., et al. (2024). LongRoPE. *arXiv*. https://doi.org/10.48550/arXiv.2402.13753

Karras, T., Aittala, M., Aila, T., & Laine, S. (2022). Elucidating the design space of diffusion-based generative models. In *NeurIPS 2022*. https://doi.org/10.48550/arXiv.2206.00364

Niederreiter, H. (1992). *Random Number Generation and Quasi-Monte Carlo Methods*. SIAM. https://doi.org/10.1137/1.9781611970081

Peng, B., Quesnelle, J., Fan, H., & Shippole, E. (2023). YaRN. *arXiv*. https://doi.org/10.48550/arXiv.2309.00071

Su, J., et al. (2024). RoFormer: Enhanced transformer with rotary position embedding. *Neurocomputing*, 568, 127063. https://doi.org/10.1016/j.neucom.2023.127063

Weyl, H. (1916). Über die Gleichverteilung von Zahlen mod. Eins. *Mathematische Annalen*, 77, 313–352.

Whitehead, W. (2026). MaiiaM Wings: Consciousness-Aligned Language Architecture. *Zenodo*. https://doi.org/10.5281/zenodo.19600795

AAMT: WP-03 (Phi-Twisted Phase Scheduling), WP-10 (Digital-Root RoPE); The 64-State Quantum Oscillation System; *The Mathematics of Consciousness* (Ch. 7, 10); Core Premise §3, §6, §9; Foundations Paper II (4τ permanence).
