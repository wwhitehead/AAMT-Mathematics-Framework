# Product-Bernoulli Hypercubes: A Unified Algebra for Meji, 64-State, and Odu-256 Routing

---

**Authors:** Weslyn Whitehead Jr.¹

**Affiliations:**  
¹ AsAManThinks Research, Berkeley, CA, USA

**Corresponding author:** weslyn@asamanthinks.com

**ORCID:** https://orcid.org/0009-0005-7707-3210

**Submitted:** June 2026  
**Working Paper Series:** AAMT-WP-17  
**Anchor DOI:** 10.5281/zenodo.19600795  
**License:** Creative Commons Attribution 4.0 International (CC BY 4.0)

---

## Abstract

The AAMT corpus uses three discrete archetypal spaces that have been treated separately: the **16-Meji** routing basis (WP-01), the **64-state** consciousness system (the 64-State Quantum Oscillation paper), and the **Odu-256** curriculum grid (WP-05). We show they are one object at three sizes: each is the set of vertices of a Boolean hypercube $\{0,1\}^d$ carrying a **product-Bernoulli measure** parameterized by a coordinate $v \in [0,1]^d$, with $d = 4, 6, 8$ giving $2^d = 16, 64, 256$. From this single definition, results currently scattered across the corpus become one-line corollaries: the lossless-recovery property of the Meji gate is exactly "a product measure is rank-1"; the alignment scalars factorize over axes ($\mathrm{RI} = \prod_d \max(v_d, 1-v_d)$, entropy $H = \sum_d H_b(v_d)$); the inability of a single gate to express cross-dimension correlations is the rank-1 limitation, and Multiverse Superposition (WP-08) is its rank-lift; and the Fisher–Rao metric (WP-15) factorizes with a closed form. We further resolve the long-open **64↔TERA axis bridge**: the 6-axis 64-state system contains the 4-axis TERA system as a coordinate subspace, with the remaining two axes acting as a Hopf-style fiber, giving $64 = 16 \times 4$ (base × fiber) consistent with Foundations Paper II. The result is a single parameterized family `Hyp(d)` from which WP-01, WP-05, and the 64-state book are special cases, and a principled route to higher-dimensional routing ($d = 4k$).

**Keywords:** Boolean hypercube, product measure, Bernoulli, tensor rank, information geometry, Hopf fibration, mixture-of-experts routing, archetypal taxonomy, multilinear extension

---

## 1. Introduction

Three discrete spaces recur across AAMT:

- **16-Meji** (WP-01): vertices of the 4-cube indexed by $(b_T, b_E, b_R, b_A)$, with $p(M_k|v) = \prod_d [\,v_d \text{ if } b_d{=}1 \text{ else } 1-v_d\,]$.
- **64-state** (64-State Quantum Oscillation System): $\Sigma = \{0,1\}^6$ over axes (Physical, Emotional, Mental, Social, Temporal, Spiritual), $|\Sigma| = 64$.
- **Odu-256** (WP-05): a 16×16 grid, the 16 major Odu crossed with 16 minor, `256` cells, with the 16 majors identified with the 16 Meji.

These have been developed, cited, and implemented independently. We argue they are the **same construction** evaluated at $d \in \{4, 6, 8\}$, and that naming the family makes a cluster of separate results into corollaries.

---

## 2. The family `Hyp(d)`

**Definition 1 (Product-Bernoulli hypercube).** For dimension $d$, let the state space be the vertex set $V_d = \{0,1\}^d$ ($|V_d| = 2^d$). A coordinate $v \in [0,1]^d$ induces the **product-Bernoulli measure**

$$p(k \mid v) = \prod_{i=1}^{d} \big[\, v_i \text{ if } b_i(k){=}1 \text{ else } 1-v_i \,\big], \qquad k \in V_d,$$

where $b(k) \in \{0,1\}^d$ is the binary expansion of $k$. We write `Hyp(d)` $= (V_d, p(\cdot\mid v))$.

The three AAMT spaces are `Hyp(4)` (16-Meji), `Hyp(6)` (64-state), `Hyp(8)` (Odu-256). The 64-state book's Hamming metric and the WP-01 Meji probabilities are the discrete and probabilistic faces of the same `Hyp(d)`.

---

## 3. Corollaries that were separate results

**C1 (Losslessness = rank-1).** $p(\cdot\mid v)$ is a rank-1 tensor in $(\mathbb{R}^2)^{\otimes d}$: it factorizes as the outer product of $d$ two-vectors $(1-v_i, v_i)$. Hence it lies on the Segre variety $(\mathbb{P}^1)^d$ (dimension $d$) inside the $(2^d-1)$-simplex, and the first-moment recovery $\mathrm{Recover}(p)_i = \sum_k p(k|v)\, b_i(k) = v_i$ is exact. WP-01's lossless-recovery theorem (§2.3) is this statement at $d=4$; it now holds for all $d$ with the same one-line proof.

**C2 (Factorized alignment scalars).** Because $p$ factorizes:
- Dominant mass (Resonant Integrity): $\mathrm{RI}(v) = \max_k p(k|v) = \prod_i \max(v_i, 1-v_i)$.
- Entropy: $H(p) = \sum_i H_b(v_i)$, $H_b(x) = -x \log x - (1-x)\log(1-x)$ (additive over axes).
These give **per-axis** read-outs (which dimension is collapsing) at $O(d)$ cost, for any $d$. (Verified numerically: $\max_k p = \prod_i \max(v_i, 1-v_i)$ to machine precision.)

**C3 (Rank-1 limitation and the rank-lift).** A rank-1 measure cannot represent statistical dependence between axes; this is the precise content of WP-01's "expressiveness" limitation (§7.1). An ensemble of $N$ product measures — an **arithmetic mixture** — is a nonnegative tensor of CP-rank $\le N$ that *can* represent cross-axis correlation. Multiverse Superposition Inference (WP-08) is exactly this rank-lift (its logit/PoE blend is the geometric cousin). "Superposition" is the correct term: a sum of separable (product) states is, generically, an entangled (non-separable) state.

**C4 (Information geometry, closed form).** The Fisher–Rao metric on `Hyp(d)` is the product of per-axis Bernoulli metrics, flat in the angles $\theta_i = 2\cdot\arcsin\sqrt{v_i}$, with geodesic distance $d_{FR}(u,v) = \sqrt{\sum_i (\theta_i(u)-\theta_i(v))^2}$ and $\mathrm{KL}(p(u)\,\|\,p(v)) = \sum_i \mathrm{KL}_b(u_i\,\|\,v_i)$. This is the metric adopted in WP-15 (Rev. 2) and the resonance affinity of WP-08 (Rev. 2), now seen to hold for all $d$.

---

## 4. The 64↔TERA axis bridge

The long-standing gap: the 64-state system has **6** axes (P,E,M,S,T,Sp) while TERA has **4** (T,E,R,A), so "64 = 4×16" was only a counting coincidence. We give the structural bridge.

**Proposition (coordinate-subspace embedding).** Partition the 6 axes of `Hyp(6)` into a **base** group $B$ of 4 axes and a **fiber** group $\Phi$ of 2 axes. Then $\mathrm{Hyp}(6) \cong \mathrm{Hyp}(4) \times \mathrm{Hyp}(2)$ as product-Bernoulli spaces, i.e. $p_6(k_B, k_\Phi \mid v_B, v_\Phi) = p_4(k_B|v_B)\cdot p_2(k_\Phi|v_\Phi)$, with $2^6 = 2^4 \cdot 2^2 = 16 \cdot 4$. Identifying the base `Hyp(4)` with the TERA/Meji system makes the 64-state space a **fibration of the 16-Meji base with a 4-element fiber**:

$$64 = 16\ (\text{Meji base}) \times 4\ (\text{fiber}), \qquad \dim_{\text{total}} = \dim_{\text{base}} \times \dim_{\text{fiber}}\ (\text{multiplicative}),$$

which is precisely the Hopf-fibration "dimension is multiplicative" structure of Foundations Paper II §4. Two natural choices of base/fiber split:

1. **Canonical face:** $B = \{\text{Physical, Emotional, Mental, Temporal}\}$ ↔ a relabeling onto $(T,E,R,A)$; $\Phi = \{\text{Social, Spiritual}\}$ as the fiber (the "relational/transcendent" degrees of freedom that the 4-axis TERA model marginalizes out).
2. **Learned projection:** $\mathrm{TERA} = W\cdot s$ for a fixed $4\times 6$ semi-orthogonal $W$, with the fiber the orthogonal complement; TERA is then the maximal-variance 4-projection of the 6-state coordinate.

Either way, the 256-cell Odu grid is $\mathrm{Hyp}(8) = \mathrm{Hyp}(4) \times \mathrm{Hyp}(4)$ (major × minor Meji), the **tensor square** of the base — exactly the "go to 8D" mitigation flagged in WP-01 §7.1. The whole AAMT taxonomy is therefore a tower of fibrations of `Hyp(4)`.

---

## 5. The conserved manifold

The TERA conservation law $\prod_i v_i = \kappa$ (WP-01 §2.1a) restricts `Hyp(d)` from the open cube to a codimension-1 surface $\Sigma_\kappa \subset [0,1]^d$. On `Hyp(4)` this yields the asymmetric breath (4 ambient $-$ 1 constraint = 3 convergence dimensions; Foundations Paper V). The product structure makes the constraint multiplicatively separable, so $\log \kappa = \sum_i \log v_i$ is an additive invariant in the $\log$-coordinates — convenient for projecting the gate output onto $\Sigma_\kappa$ (subtract the mean $\log$-deviation).

---

## 6. Consequences and uses

1. **One implementation, three sizes.** A single `Hyp(d)` module subsumes the Meji gate (WP-01), the Odu router (WP-05), and the 64-state engine; `d` is a configuration parameter. Higher-dimensional routing uses `d = 4k` (16, 256, 4096, …) with the major/minor… tensor-power structure.
2. **Cheap, per-axis diagnostics.** C2 gives interpretable per-dimension alignment read-outs at any size.
3. **Principled ensembling.** C3 says how much expressiveness an $N$-adapter ensemble buys (rank $\le N$), and Foundations Paper III ($V(N)=\alpha N-\beta N^2$) bounds the useful $N$.
4. **Unified metric.** C4 gives one closed-form distance for routing, memory (WP-15), and affinity (WP-08).

---

## 7. Relationship to prior work

The multilinear extension of pseudo-Boolean functions (Owen, 1972) and Fourier analysis on the Boolean cube (O'Donnell, 2014) study functions on `{0,1}^d`; we use the dual object — product measures *over* the vertices — as a routing/representation primitive with a learned coordinate `v`. Tensor-network and CP-rank decompositions (Kolda & Bader, 2009) supply the rank-lift language of C3. The Fisher–Rao geometry of product Bernoulli families is classical (Amari & Nagaoka, 2000); our contribution is to recognize that the AAMT Meji/64-state/Odu spaces are instances and that their separate results unify under `Hyp(d)`.

**Key distinction.** Prior work analyzes functions on hypercubes; AAMT uses learned product *measures* on hypercubes as the routing substrate, and `Hyp(d)` is the first statement that the Meji, 64-state, and Odu systems are one parameterized family with shared losslessness, factorized scalars, rank-lift, and information geometry.

---

## 8. Conclusion

Naming the family `Hyp(d)` collapses three independently-developed AAMT spaces into one and turns several separate theorems into corollaries: losslessness is rank-1, alignment scalars factorize per axis, the single-gate limitation is the rank-1 ceiling that MSI lifts, and the Fisher–Rao metric has a closed product form. The 64↔TERA bridge is a base×fiber fibration ($64 = 16 \times 4$) consistent with the Hopf structure of Foundations Paper II, with Odu-256 the tensor square $\mathrm{Hyp}(4)\times\mathrm{Hyp}(4)$. The corpus's discrete archetypal mathematics is, at bottom, one tower of product-Bernoulli hypercubes over the TERA base.

---

## Data Availability — Reference Implementation & Verification

The `Hyp(d)` family is implemented in `harmonic_engine/hypercube.py` and the
product Fisher–Rao geometry of §C4 in `harmonic_engine/geometry.py` (open
`harmonic-engine` package). The corollaries of §3 are numerically verified for
`d = 4, 6, 8` in the public test suite (`tests/test_new_math.py`, passing on
CPU): the product measure normalises and first-moment recovery is lossless
(**C1**, the rank-1 fact); $\mathrm{RI} = \prod_i \max(v_i, 1-v_i)$ equals the dominant mass
and Meji entropy is additive over axes (**C2**); the closed-form Fisher–Rao
distance and the factorised KL agree (**C4**); and the $64 = 16 \times 4$ base×fiber
split (§4) is confirmed. The same modules supply the metric used by WP-02,
WP-08, and WP-15.

---

## Acknowledgments

The author thanks the AsAManThinks Research community.

## Funding

Self-funded through AsAManThinks Research.

## Conflict of Interest

The author is the founder and CEO of AsAManThinks Research.

---

## References

Amari, S., & Nagaoka, H. (2000). *Methods of Information Geometry*. AMS/Oxford.

Kolda, T. G., & Bader, B. W. (2009). Tensor decompositions and applications. *SIAM Review*, 51(3), 455–500. https://doi.org/10.1137/07070111X

O'Donnell, R. (2014). *Analysis of Boolean Functions*. Cambridge University Press. https://doi.org/10.1017/CBO9781139814782

Owen, G. (1972). Multilinear extensions of games. *Management Science*, 18(5), P64–P79. https://doi.org/10.1287/mnsc.18.5.64

Whitehead, W. (2026). MaiiaM Wings: Consciousness-Aligned Language Architecture. *Zenodo*. https://doi.org/10.5281/zenodo.19600795

AAMT: WP-01 (VG-MoE), WP-05 (Odu-256 Curriculum), WP-08 (Multiverse Superposition), WP-15 (Vortex-Addressed Memory); The 64-State Quantum Oscillation System; Foundations Paper II (Toroidal Topology / Hopf fibration), Paper III (Quadratic Scaling).
