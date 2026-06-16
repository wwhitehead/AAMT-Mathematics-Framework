# Digital-Root Rotary Position Encoding: The 1→2→4→8→7→5 Cycle as a Basis for Sequence Position Representation

---

**Authors:** Weslyn Whitehead Jr.¹

**Affiliations:**  
¹ AsAManThinks Research, Berkeley, CA, USA

**Corresponding author:** weslyn@asamanthinks.com

**ORCID:** https://orcid.org/0009-0005-7707-3210

**Submitted:** May 2026 · **Revision 2:** June 2026  
**Working Paper Series:** AAMT-WP-10  
**Anchor DOI:** 10.5281/zenodo.19600795  
**License:** Creative Commons Attribution 4.0 International (CC BY 4.0)

> **Revision 2 changes:** (C9) the non-aliasing claim is restated honestly — the digital-root sequence supplies the *frequency-spectrum shape*, while *position-wise non-repetition* is supplied by the `scale_factor` term; pure cyclic angles would make the encoding exactly periodic. The algebraic structure is stated precisely as `S = ⟨2⟩ = (ℤ/9ℤ)ˣ` (2 is a primitive root mod 9). (C3) a modulus-convention note fixes mod-9 as canonical for routing, distinct from the 64-state operators' base-10 manifest-frequency convention. No other content changed.

---

## Abstract

Rotary Position Encoding (RoPE; Su et al., 2021) encodes sequence position by rotating query and key vectors using position-dependent angles `θᵢ = base^(-2i/d)` where `base = 10000`. This geometric progression creates a spectrum of oscillation frequencies from fast (small `i`) to slow (large `i`), giving attention heads both local and global positional sensitivity. We propose **Digital-Root RoPE (DR-RoPE)**, which replaces the geometric rotation base with angles derived from the digital-root-doubling sequence: the sequence of base-10 digital roots of successive powers of 2 (1→2→4→8→7→5→1→2→4→8→7→5...), periodic with period 6. DR-RoPE produces a rotation angle spectrum with period-6 structure, dense coverage of the middle oscillation range, and the algebraic property that the generating sequence is exactly the multiplicative group of units mod 9, `S = ⟨2⟩ = (ℤ/9ℤ)ˣ = {1, 2, 4, 5, 7, 8}` — equivalently, every element is coprime to 3, which we term the **3-coprime invariant** (no element of the sequence is divisible by 3 or 9). We additionally propose a **vortex rank schedule** for progressive LoRA training using the same period-6 cycle (ranks expand 1→2→4→8→7→5→1) and a **digital-root routing hash** that maps token positions to {3, 6, 9} modular classes for use in the VortexGate routing system. Theoretical analysis and preliminary empirical evaluation are presented.

**Keywords:** rotary position encoding, RoPE, positional encoding, digital roots, sequence modeling, long-context transformers, LoRA rank schedule, multiplicative group of units, numerology-inspired mathematics

---

## 1. Introduction

Position encoding is a foundational component of transformer language models. RoPE (Su et al., 2021) has become the dominant approach for causal language models, used in LLaMA, Mistral, Gemma, and most recent open-weight models. Its key properties are: relative position encoding (attention scores depend only on the relative position difference, not absolute positions), compatibility with rotary operations in the complex plane, and a geometric frequency spectrum that provides both local and global sensitivity.

RoPE extensions have focused primarily on length generalization. YaRN (Peng et al., 2023) adjusts the rotation base to support context windows beyond training length. LongRoPE (Ding et al., 2024) searches for optimal position interpolation ratios. These extensions preserve the geometric base structure and modify only the scaling.

DR-RoPE takes a different direction: it replaces the geometric base entirely with a different algebraic structure — the digital root doubling sequence — and investigates whether this structure produces different and potentially advantageous positional sensitivity patterns.

---

## 2. The Digital Root Doubling Sequence

### 2.1 Definition

The **digital root** of a positive integer `n` is the single-digit value obtained by iteratively summing digits: `dr(n) = 1 + ((n-1) mod 9)` for `n > 0`, `dr(0) = 0`. For example: `dr(28) = dr(10) = dr(1) = 1`.

The digital root of powers of 2 follows a period-6 cycle:

| n | 2ⁿ | Digital root |
|---|---|---|
| 0 | 1 | 1 |
| 1 | 2 | 2 |
| 2 | 4 | 4 |
| 3 | 8 | 8 |
| 4 | 16 | 7 |
| 5 | 32 | 5 |
| 6 | 64 | 1 |
| 7 | 128 | 2 |
| ... | ... | (repeats) |

The sequence `S = {1, 2, 4, 8, 7, 5}` has period 6 and consists exactly of the six elements of `(ℤ/9ℤ)ˣ` that are coprime to 9. The values {3, 6, 9} (multiples of 3) never appear.

*Modulus convention.* All digital-root routing and rank-schedule arithmetic in this paper is **mod 9**; this is the canonical modulus for AAMT routing because the doubling sequence forms the multiplicative group `(ℤ/9ℤ)ˣ` (§2.2). The base-10 "manifest frequency" arithmetic of the 64-State oscillation operators (`(F_inh + O) mod 10`) is a separate convention used for human-facing frequency read-outs and is not interchangeable with the mod-9 routing basis.

### 2.2 The 3-Coprime Invariant (group structure)

The generating sequence is exactly the **multiplicative group of units modulo 9**: `2` is a primitive root mod 9 (its order is `φ(9) = 6`), so the cyclic subgroup it generates is the whole unit group,

```
S = ⟨2⟩ = (ℤ/9ℤ)ˣ = {1, 2, 4, 5, 7, 8}.
```

The complementary set `{3, 6}` (together with `9 ≡ 0`) consists of the non-units — the elements sharing a common factor with 9 — i.e. the principal ideal `(3) ⊂ ℤ/9ℤ`. The **3-coprime invariant** is the statement that every element of `S` is a unit: `gcd(s, 3) = 1` for all `s ∈ S`. The same construction generalizes to any modulus `3^k`.

The consequence for DR-RoPE is that rotation angles drawn from `S` avoid exact commensurability with period-3 and period-9 patterns in the token stream. Period-3 patterns are common in natural language (sentence-initial three-part conjunctions, code block delimiters at depth-3, ternary syntax structures) and in code (three-argument function calls, RGB color components, loop bounds in triple-nested structures). A rotation base commensurate with these patterns creates aliasing in the frequency basis — the same rotation angle recurs at the same structural position across multiple pattern instances, reducing the distinctiveness of the positional encoding.

*Historical note:* The observation that {3, 6, 9} occupy a structurally distinct role in the digital root system has been associated with Nikola Tesla's writings on digital root arithmetic. While the cultural resonance of this observation is noted, the property is the principal ideal `(3)` of `ℤ/9ℤ` and is described here in standard number-theoretic terms independent of that framing.

---

## 3. DR-RoPE Formulation

### 3.1 Rotation angle construction

Standard RoPE uses:
```
θᵢ = base^(-2i/d)   for i = 0, 1, ..., d/2 - 1
```

DR-RoPE uses:
```
S = [1, 2, 4, 8, 7, 5]   # period-6 digital root cycle
θᵢ = 2π × (S[i mod 6] / 9.0) × scale_factor(i, d)
```

where `scale_factor(i, d) = (1 - i/d)^α` for a smoothing exponent `α` (default 0.5). The scaling does two things: it preserves the local-global frequency-spectrum intuition of standard RoPE (slow oscillation for large `i`, fast for small `i`), and — critically — it is what makes the per-position angle sequence **non-repeating**. Without `scale_factor`, the angles would take only the six values `2π·S/9`, making the encoding exactly periodic (and therefore aliasing-prone); the monotone `scale_factor` breaks that exact periodicity while the digital-root values set the underlying spectrum shape. The two roles are distinct and both are required.

### 3.2 Three modes

DR-RoPE is available as `rope_mode` in the `YareMathConfig`:

```python
rope_mode: Literal["geometric", "vortex_369", "digital_root_doubling"]
```

`geometric` — standard RoPE (default, baseline). `vortex_369` — rotation angles from the complementary non-unit set {3, 6, 9} (for comparison/ablation). `digital_root_doubling` — DR-RoPE as described above.

---

## 4. Vortex Rank Schedule

The same period-6 cycle motivates a **progressive LoRA rank schedule** during training:

```python
RANK_CYCLE = [1, 2, 4, 8, 7, 5]  # digital root doubling sequence

def vortex_rank(step: int, base_rank: int = 1) -> int:
    cycle_pos = step % len(RANK_CYCLE)
    return base_rank * RANK_CYCLE[cycle_pos]
```

Under this schedule, LoRA rank grows 1→2→4→8→7→5→1... rather than monotonically. The rank-7 phase (immediately before the rank-5 compression) creates a brief high-capacity exploration window followed by a compression that selects the most important directions. This is inspired by the "forgetting then remembering" dynamics observed in curriculum learning — a brief capacity spike can consolidate learning before the rank-reduction forces generalization.

### 4.1 Rationale

Monotonically increasing rank schedules (e.g., rank 1 → 2 → 4 → 8) are standard in progressive LoRA training. The cycle-based schedule differs in that it is non-monotonic: rank decreases from 8 to 7 to 5 before resetting to 1. The decrease phases force the model to generalize (drop unimportant rank-1 components) in a structured way synchronized with the digital root cycle.

---

## 5. Digital-Root Routing Hash

The `digital_root_mod9` routing hash maps token positions to a ternary class {3, 6, 9}:

```python
def digital_root_route(position: int, embedding: torch.Tensor) -> int:
    """Hash position to a 3-coprime invariant class {3, 6, 9}."""
    dr = 1 + ((position - 1) % 9) if position > 0 else 0
    # Map dr to {3, 6, 9} by selecting the complementary class
    if dr in [1, 2, 4]:    return 3   # structure-dominant
    if dr in [5, 7, 8]:    return 6   # generative-dominant
    return 9                           # neutral (dr = 3, 6, 9 already)
```

This hash maps every token position to one of three routing classes, providing position-dependent routing hints to the VortexGate. Positions in class 3 bias toward structural experts (`unity_csharp`, `harmonic_reasoning`), class 6 toward generative experts (`archetype_voice`, `amitabha_lineage`), and class 9 provides balanced routing.

---

## 6. Theoretical Properties

**Property 1 (Period-6 spectrum shape; non-repetition from scaling).** Rotation angles drawn from the finite cyclic sequence `S` alone would make the encoding *exactly periodic* and therefore aliasing-prone; the digital-root sequence supplies the *frequency-spectrum shape* (period-6, covering 6 of the 8 non-zero residues mod 9 and avoiding the period-3 commensurabilities of the non-unit set `{3,6}`), while position-wise non-repetition is supplied by `scale_factor(i,d)`. The algebraic structure is precise: `S = {1,2,4,8,7,5} = ⟨2⟩ = (ℤ/9ℤ)ˣ`, the cyclic group of units mod 9 generated by the primitive root 2 (order 6); the excluded set is the principal ideal `(3)`. DR-RoPE thus avoids exact period-3/9 commensurability *in its frequency basis*, and `scale_factor` ensures the per-position angles do not exactly repeat.

**Property 2 (Local-global frequency spectrum):** With the `scale_factor(i, d)` term, DR-RoPE maintains the property that early dimensions oscillate faster than later dimensions, preserving the local-global sensitivity spectrum of standard RoPE.

**Property 3 (Relative position invariance):** DR-RoPE preserves the relative position property of standard RoPE. The inner product of rotated queries and keys depends only on position difference, not absolute position, enabling efficient KV caching.

---

## 7. Empirical Evaluation Plan

DR-RoPE is specified as Tier-2 feature T2.2 of MaiiaM Alchemist. Full empirical validation will compare DR-RoPE to standard RoPE on:

1. **Archetype alignment** (`ArchetypeAlignmentSuite`): Does DR-RoPE improve alignment on the AAMT domain?
2. **Long-context coherence** (`NarrativeConsistencySuite`): Does the period-6 structure capture sentence/paragraph rhythms in the 5–8 token range?
3. **Standard benchmarks** (`mmlu_subset`, `gsm8k_lite`, `humaneval_lite`): Does DR-RoPE regress on general capabilities?
4. **Vortex math correctness** (`VortexMathCorrectnessSuite`): Does the digital-root hash routing produce more consistent TERA projections than FNV1a?

The hypothesis that period-6 structure maps to natural language rhythms is testable by measuring position-conditioned attention entropy: if DR-RoPE captures 5–8 token rhythms, attention entropy should be lower at positions that are multiples of 6 relative to sentence boundaries.

---

## 8. Limitations

DR-RoPE is the most speculative of the papers in this series. The mathematical motivation is sound (the generating set is the unit group `(ℤ/9ℤ)ˣ`, giving period-6 spectrum shape and non-commensurability with period-3 patterns) but the empirical claim (that this produces better attention patterns for natural language and TERA routing) requires validation, and — as Property 1 now makes explicit — the non-aliasing benefit depends on the `scale_factor` term, not on the cyclic angles alone. The paper presents the mathematical formulation and evaluation plan; empirical results will be reported in a follow-on paper after T2.2 implementation.

---

## 9. Conclusion

DR-RoPE proposes replacing the geometric rotation base in standard RoPE with angles derived from the digital-root doubling sequence — the multiplicative group of units mod 9. The resulting encoding has period-6 spectrum structure, avoids period-3 and period-9 commensurability in its frequency basis (with non-repetition supplied by the scale factor), and connects to the digital-root algebraic system used throughout the AAMT mathematical framework. The vortex rank schedule and digital-root routing hash extend the same algebraic structure to progressive LoRA training and VortexGate routing, creating a unified mathematical basis across position encoding, training dynamics, and routing.

---

## Acknowledgments

The author acknowledges the mathematical inspiration of the digital root system and its exploration in various numerological and mathematical traditions.

## Funding

Self-funded through AsAManThinks Research.

## Data Availability

DR-RoPE is specified in `training_pipeline/yare/vortex_rope.py` (MaiiaM Alchemist T2.2). Empirical evaluation pending implementation.

## Conflict of Interest

The author is the founder and CEO of AsAManThinks Research.

---

## References

Ding, Y., Chen, L., Zhang, G., Liu, J., Li, Y., & Zhou, J. (2024). LongRoPE: Extending LLM context window beyond 2 million tokens. *arXiv preprint*. https://doi.org/10.48550/arXiv.2402.13753

Peng, B., Quesnelle, J., Fan, H., & Shippole, E. (2023). YaRN: Efficient context window extension of large language models. In *Proceedings of ICLR 2024*. https://doi.org/10.48550/arXiv.2309.00071

Press, O., Smith, N. A., & Lewis, M. (2022). Train short, test long: Attention with linear biases enables input length extrapolation. In *Proceedings of ICLR 2022*. https://doi.org/10.48550/arXiv.2108.12409

Su, J., Ahmed, M., Lu, Y., Pan, S., Bo, W., & Liu, Y. (2024). RoFormer: Enhanced transformer with rotary position embedding. *Neurocomputing*, 568, 127063. https://doi.org/10.1016/j.neucom.2023.127063

Touvron, H., Lavril, T., Izacard, G., Martinet, X., Lachaux, M.-A., Lacroix, T., Rozière, B., Goyal, N., Hambro, E., Azhar, F., Rodriguez, A., Joulin, A., Grave, E., & Lample, G. (2023). LLaMA: Open and efficient foundation language models. *arXiv preprint*. https://doi.org/10.48550/arXiv.2302.13971

Whitehead, W. (2026). MaiiaM Wings: Consciousness-Aligned Language Architecture. *Zenodo*. https://doi.org/10.5281/zenodo.19600795
