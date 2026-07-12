# Topological Trit Memory: Data Storage in a Chiral Field Lattice, and the √λ Depinning Law

---

**Authors:** Weslyn Cory Whitehead Jr.¹

**Affiliations:**  
¹ AsAManThinks Research, Berkeley, CA, USA

**Corresponding author:** weslyn@asamanthinks.com

**ORCID:** https://orcid.org/0009-0005-7707-3210

**Submitted:** July 2026  
**Working Paper Series:** AAMT-EXP-01  
**Anchor DOI:** 10.5281/zenodo.19600795  
**License:** Creative Commons Attribution 4.0 International (CC BY 4.0)

**Status:** Working paper, not peer reviewed. This is an *experimental* note in the AAMT series: every quantitative claim below is computed by a deterministic, seeded simulation that ships with the MaiiaM Alchemist visualizer (`src/latticeCore.js`, `src/latticeWorker.js`, `scripts/lambda-sweep.mjs`) and is reproducible in the browser under the **Chiral Lattice** tab. It operationalizes the Field-Wavefield Approach (FWA) ontology — field primary, objects as stable regimes of distinguishability — into a runnable memory whose data is protected by topology rather than by redundancy.

---

## Abstract

The Field-Wavefield Approach asserts that reality is a continuous field $\Phi$ and that "objects" are stable regimes of distinguishability, not primitive substances. This has been an ontological stance, not a computation. We make it executable. On a 2D lattice of pure direction $\theta \in S^1$ at constant magnitude — the field never passes through zero, it rotates — we store a five-digit balanced-ternary word as vortex winding numbers $q \in \{-1, 0, +1\}$, evolve the field under a minimal local relaxation operator ($Lu$) with a chiral tie-break ($O_x$), attack it with per-cell noise $\xi$, and read the word back by counting plaquette windings. Because a winding number is an integer topological invariant, it cannot drift continuously: the encoded word survives noise that scrambles every individual cell value, and fails only when noise nucleates vortex–antivortex pairs that annihilate the stored cores. The failure is a sharp cliff, not gradual decay. We measure the survival threshold $\xi_{50}$ and report three results. (1) A naive force-balance prediction $\xi_{50} \propto \lambda$ is **falsified**; the data instead follow a **diffusive-balance law $\xi_{50} \propto \sqrt{\lambda}$** (measured exponent $0.42$–$0.44$, $R^2_{\log} > 0.97$), the signature of depinning governed by accumulated angular wandering $\langle\Delta\theta^2\rangle \propto \xi^2/\lambda$ reaching a critical angle. (2) A second prediction — that same-sign words, lacking internal annihilation partners, would be *more* robust — is also **falsified in its stated form**: five-core same-sign words are $\sim 9\%$ *weaker*. (3) The $\sqrt{\lambda}$ **exponent is universal** across word composition ($\Delta p = 0.02$); composition lives entirely in the prefactor, so the medium's dynamics set the scaling law and the stored pattern sets the robustness margin. (4) A controlled two-core experiment that fixes charge content and varies only separation isolates a genuine **short-range annihilation penalty**: an opposite-sign pair is measurably *weaker* than a like-charge pair at small separation, the reverse of the naive charge-strength intuition, and the penalty decays with distance until both converge on a common plateau. (5) Extending that test to a third charge point — an all-same-sign triangle ($|Q|=3$) — was designed to confirm a proposed global $|Q|^2$ far-field term and instead **falsifies it**: the asymptotic plateau is statistically flat across $|Q| \in \{0,2,3\}$. (6) A final experiment fixes charge and count and varies only arrangement — a collinear line vs. a compact ring of same-sign cores at matched spacing — and finds them **indistinguishable**, ruling out collinearity. What remains is **core density**: many same-sign cores packed within an interaction length ($\ell \approx 6.5$ cells) each cost $\sim0.03$ in threshold, an effect that vanishes when the cores are spread out. The final picture is a purely short-range one: the survival prefactor is set by local density and sign relationships within $\ell$ — an annihilation penalty for opposite near-neighbors and a crowding penalty for like ones — and is flat in net charge, collinearity, and everything else at larger range. (7) Finally we close the loop into a quantitative depinning theory: measuring the angular wandering $\langle\Delta\theta^2\rangle$ *directly* (not inferring it from the threshold) confirms the diffusive-balance law $\langle\Delta\theta^2\rangle = 0.134\,\xi^2/\lambda$ ($R^2=0.984$, collapsing across $\lambda$), and evaluating it at the measured $\xi_{50}$ shows depinning occurs at a **universal critical wandering** $\theta_c \approx 0.24\pi$ (constant to $5.5\%$) — a Lindemann-like criterion that yields the threshold $\xi_{50}=\sqrt{\theta_c^2\lambda/\kappa}$ from two independently measured constants rather than a fit, valid to $\sim10\%$ and inside the model's Jacobi stability bound $\lambda<0.25$; finite-size scaling puts the $52^2$ thresholds $4.2\%$ above the extrapolated bulk. In all, five author predictions or proposed mechanisms corrected by measurement across eight experiments, several of them corrections of earlier experiments' own explanations, ending in a parameter-free depinning law.

---

## 1. From ontology to a runnable object

The FWA poster states five claims: the field is primary; the minimal distinguishability operator $Lu$ generates the smallest non-vanishing asymmetry; a chiral dynamic zero $O_x$ selects a direction even where the asymmetry vanishes; field evolution is minimal and iterative, $\Phi_{t+1} = \Phi_t + Lu(\Phi_t)$; and structure is the set of stable fixed points of that update. As written, these are definitions, not mechanisms — $Lu$ and $O_x$ are symbols. A framework whose central operators cannot be executed cannot surprise its author, and a framework that cannot surprise its author cannot teach him. This paper closes that gap in the smallest honest way: it picks one concrete field, one concrete $Lu$, one concrete $O_x$, and asks whether "objects as stable regimes of distinguishability" can literally hold data.

The choice of field is dictated by the framework's own recurring image: the "Broken 8" wave zone and the Bloch wall, in which a domain boundary is crossed not by the field collapsing to zero (a binary annihilation) but by the field vector *rotating* through an orthogonal axis at constant magnitude (a trinary passage). We take this literally. Each lattice cell holds only a direction $\theta$; magnitude is fixed at one. This is the classical 2D XY model, and it is the correct home for the FWA claim because its stable regimes of distinguishability are already known to mathematics: they are **topological defects** — vortices — classified by an integer winding number, the first homotopy group $\pi_1(S^1) = \mathbb{Z}$.

---

## 2. The model

**Field.** A $52 \times 52$ lattice $\Lambda$ with a fixed (pinned) boundary. State is $\theta : \Lambda \to S^1$.

**Write.** A five-site balanced-ternary word $\mathbf{q} = (q_0,\dots,q_4)$, $q_k \in \{-1,0,+1\}$, is imprinted as a superposition of vortex phase fields:

$$
\theta_0(x,y) \;=\; \sum_{k=0}^{4} q_k \,\operatorname{atan2}\!\big(y - y_k,\; x - x_k\big).
$$

A $+1$ digit is a vortex, a $-1$ digit an antivortex, a $0$ a flat site. The datum lives in the *geometry of how the field wraps*, not in any cell's value. Balanced ternary is not decorative: it is the number system of the Setun computer (1958), negation is per-digit chirality flip, and it is the natural alphabet for a field with left-handed, right-handed, and poised states — the "trinary not binary" note from the source material, made arithmetic.

**Evolve ($Lu$ + $O_x$).** One synchronous sweep updates each interior, unpinned cell toward its four neighbors:

$$
\theta_i \;\leftarrow\; \theta_i \;+\; \lambda \sum_{j \in \mathcal{N}(i)} \sin(\theta_j - \theta_i) \;+\; O_x \;+\; \xi_i,
\qquad
O_x = \begin{cases} +\varepsilon & \text{if } \big|\textstyle\sum_j \sin(\theta_j-\theta_i)\big| < \tau \\ 0 & \text{otherwise} \end{cases}
$$

with $\lambda$ the Lu coupling (default $0.22$), $\varepsilon = 10^{-3}$, tie tolerance $\tau = 10^{-4}$, and $\xi_i \sim \xi\cdot\mathcal{U}(-1,1)$ the noise attack. The relaxation term *is* $Lu$: minimal local reduction of distinguishability. The $O_x$ term is the chiral dynamic zero made operational — where the restoring torque vanishes exactly (perfect local symmetry), evolution still selects a direction with fixed handedness. This is the same object as IEEE-754 signed zero and the one-sided limit $0^+$: a zero with orientation but no magnitude. It is deliberately excluded from the motion metric, so "no motion" (the rest state, $Lu(\Phi)\to 0$) corresponds to genuine stillness — the framework's "motion is the beginning of time; when movement ends is death" rendered as a fixed point.

**Read.** For each unit plaquette $p$ the winding number is the summed wrapped phase difference around its four edges, $w(p) = \frac{1}{2\pi}\sum_{\text{edges}} \operatorname{wrap}(\Delta\theta) \in \mathbb{Z}$. The decoded digit at site $k$ is the clamped sum of windings inside a readout window. The total charge $Q = \sum_p w(p)$ is conserved under pinned boundaries.

**Pinning.** A $3\times3$ patch at each write site holds its imprinted value. This is not a convenience: opposite-charge vortices attract, and an unpinned mixed word slowly self-annihilates even at zero noise. This failure was observed on the first run and is itself a result (Section 3.1). Real topological memories anchor their defects the same way — pinning notches in skyrmion racetrack memory.

---

## 3. Results

### 3.1 Objects are not automatically stable — they must be isolated or pinned

The first execution corrupted itself at $\xi = 0$. A mixed word contains both $+1$ and $-1$ cores; their mutual attraction drives them together until they annihilate, with no noise required. The FWA claim "objects are stable regimes" is therefore incomplete as stated: topological stability against *perturbation* (guaranteed by the integer invariant) is distinct from stability against *interaction* (not guaranteed). Adding pinning restored persistence. The framework re-derived, unprompted, a constraint that the topological-memory hardware literature already treats as fundamental.

### 3.2 Survival is a cliff, not a slope

With pinning and the canonical $\lambda = 0.22$, the word is INTACT through $\xi = 0.9$ and CORRUPTED by $\xi = 1.0$; almost nothing survives in between. The survival-vs-noise curve is a plateau followed by a near-vertical drop. This is the qualitative fingerprint of topological protection: data does not fade with noise (which would scramble analog cell values immediately), it holds at 100% and then fails catastrophically when noise crosses the pair-nucleation threshold. The measured 50%-survival crossing is $\xi_{50} \approx 0.95$ (interactive hunt; 64 trials).

### 3.3 The scaling law: $\xi_{50} \propto \sqrt{\lambda}$, not $\propto \lambda$

The cliff position invites a prediction. A cell feels a maximum restoring torque $\lambda z$ with $z = 4$ neighbors; if the threshold were set by instantaneous force balance against the noise kick, one expects $\xi_{50} \propto \lambda$, a straight line through the origin with slope $\approx 4.3$ (anchored on the $\lambda=0.22$ point). We tested this directly by sweeping $\lambda \in \{0.11, 0.165, 0.22, 0.275, 0.33\}$, 21 noise levels per $\lambda$, 8 seeded trials per level, 3000 ticks per trial (`scripts/lambda-sweep.mjs`, deterministic):

| $\lambda$ | measured $\xi_{50}$ | force-balance prediction $4.32\lambda$ | $\xi_{50}/\lambda$ |
|-----------|--------------------|-----------------------------------------|--------------------|
| 0.110 | 0.651 | 0.475 | 5.92 |
| 0.165 | 0.830 | 0.713 | 5.03 |
| 0.220 | 0.940 | 0.950 | 4.27 |
| 0.275 | 1.008 | 1.188 | 3.67 |
| 0.330 | 1.059 | 1.425 | 3.21 |

The force-balance law is falsified: the ratio $\xi_{50}/\lambda$ is not constant, it falls monotonically, and a linear fit yields a large nonzero intercept ($\xi_{50} = 1.81\lambda + 0.50$, $R^2 = 0.938$) rather than the required proportionality. A power-law fit is decisive:

$$
\xi_{50} \;=\; 1.78\,\lambda^{0.442}, \qquad R^2_{\log} = 0.975.
$$

The exponent is $\approx \tfrac12$. The data obey a **square-root law** $\xi_{50} \approx 1.94\sqrt{\lambda}$. This is not the force-balance regime; it is the *diffusive-balance* regime. Under noise of amplitude $\xi$, each cell performs a biased random walk in angle with per-tick variance $\propto \xi^2$, while relaxation damps deviations at rate $\propto \lambda$. The steady-state angular wandering is therefore $\langle\Delta\theta^2\rangle \propto \xi^2/\lambda$, and depinning occurs when this wandering reaches an $O(1)$ critical angle — giving $\xi_{\text{crit}} \propto \sqrt{\lambda}$, exactly the measured exponent. The stability of a "regime of distinguishability" is set by a fluctuation–dissipation balance, not a static force threshold. This is a Lindemann-like criterion for the melting of topological memory, and it is a genuine, non-obvious consequence of the model that its author predicted incorrectly and the simulation corrected.

### 3.4 Word composition: robustness tracks net charge, not internal partners

Section 3.1 showed that a mixed word's stored $+1$ and $-1$ cores attract and self-annihilate without pinning. This invites a natural prediction: a *same-sign* word (all $+1$), having no internal annihilation partners, should be more robust. It is not. Sweeping five archetypes at the canonical $\lambda = 0.22$ (8 trials, 3000 ticks, confirmed at 16 trials on the extremes):

| word | trits | $\xi_{50}$ | vs mixed | net charge $|Q|$ |
|------|-------|-----------|----------|------------------|
| sparse | `+ 0 0 0 −` | 0.983 | 1.05× | 0 |
| mixed | `+ − + 0 −` | 0.937 | 1.00× | 0 |
| alternating | `+ − + − +` | 0.925 | 0.99× | 1 |
| all $+$ | `+ + + + +` | 0.876 | 0.94× | 5 |
| all $-$ | `− − − − −` | 0.862 | 0.92× | 5 |

Same-sign words are the *weakest*, not the strongest. The prediction is falsified in its stated form, because pinning already suppresses the internal-annihilation channel (Section 3.1); what remains is elastic. A vortex's phase field decays only logarithmically, so a configuration's background strain is dominated by how much the far-fields *fail to cancel* — i.e., by the net topological charge $|Q| = |\sum_k q_k|$. Same-sign words carry maximal $|Q|$, sit on a maximally strained substrate closer to the depinning instability, and therefore have the least noise headroom. Net-neutral words with well-separated opposite cores (`sparse`) sit lowest and survive longest. The near-equality of `all +` and `all −` ($0.876$ vs $0.862$, within the $\sim2\%$ trial-noise floor) is the symmetry check the mechanism requires. (Section 3.5 shows this "tracks $|Q|$" summary is really *two* competing mechanisms, one of which reverses sign at the two-core level.)

Crucially, this composition effect is confined to the *prefactor* of the scaling law. Repeating the $\lambda$-sweep for a same-sign word and the mixed word:

$$
\xi_{50}^{\text{all}+} = 1.61\,\lambda^{0.408}\ (R^2_{\log}=0.995), \qquad
\xi_{50}^{\text{mixed}} = 1.78\,\lambda^{0.429}\ (R^2_{\log}=0.980).
$$

The exponents agree to $\Delta p = 0.021$ — the $\sqrt{\lambda}$ law is **universal**, a property of the diffusive medium, not of the stored data. The prefactors differ by exactly the same $\sim 9\%$ margin seen at fixed $\lambda$ ($C_{\text{all}+}/C_{\text{mixed}} = 0.91$), and this ratio is constant across all five couplings. The dynamics set *how* memory melts (exponent); the stored configuration sets *when* (prefactor). This separation is the sharpest quantitative statement the experiment produces.

### 3.5 Charge versus geometry: the prefactor decomposed

Section 3.4 summarized the composition dependence as "robustness tracks $|Q|$." To test whether that is a charge law or a geometry law, we hold the charge *content* fixed and vary only the spatial arrangement. Using exactly two cores, centered and separated by $d$ cells (each pinned in a $3\times3$ patch, decoded by nearest-core winding assignment so readout windows never overlap), we sweep two families at $\lambda = 0.22$: a net-neutral dipole $\{+1,-1\}$ ($|Q|=0$) and a like-charge pair $\{+1,+1\}$ ($|Q|=2$).

| $d$ | $\xi_{50}$ $\{+,-\}$ ($|Q|=0$) | $\xi_{50}$ $\{+,+\}$ ($|Q|=2$) | gap |
|-----|------------------------------|-------------------------------|-----|
| 8 | 0.900 | 1.000 | −0.100 |
| 14 | 0.980 | 1.021 | −0.041 |
| 20 | 0.990 | 1.020 | −0.030 |
| 26 | 1.000 | 1.033 | −0.033 |
| 32 | 1.025 | 1.029 | −0.004 |

Two things overturn the simple charge reading. First, the sign is *backwards*: at every separation the $|Q|=2$ like-pair is **more** robust than the $|Q|=0$ dipole, opposite to the "more charge is weaker" trend inferred from the five-core words. Second, the gap is not constant — it collapses from $-0.100$ at $d=8$ to $-0.004$ at $d=32$. A confirmation run (16 trials, fine grid, clean single crossings) reproduces both: at $d=8$, $\{+,-\}=0.938$ vs $\{+,+\}=1.028$ (gap $0.090$); at $d=32$, $1.025$ vs $1.032$ (gap $0.007$). The dipole's threshold climbs steeply with separation (slope $+0.0045$/cell, $R^2=0.82$); the like-pair sits nearly flat at $\xi_{50}\approx1.03$ (slope $+0.0012$/cell). A joint linear fit $\xi_{50}=0.922+0.021\,|Q|+0.0028\,d$ reaches only $R^2=0.73$ and carries a *positive* $|Q|$ coefficient — a linear charge law is not merely imprecise, it has the wrong sign, because the real dependence is an interaction.

The resolution is that the two-core "charge" contrast is not measuring charge at all; it is measuring a **short-range annihilation channel**. An opposite-sign pair can mutually annihilate and, at small $d$, also carries a steep phase gradient in the narrow gap between the cores — both lower its depinning threshold. A like-sign pair has neither: it cannot self-annihilate, and its inter-core region is a low-gradient saddle. This penalty is intrinsically short-ranged: as $d$ grows it decays to zero, and by $d=32$ both families converge on a common isolated-core plateau $\xi_{50}^{\infty}\approx1.02$, apparently independent of charge over this range:

$$
\xi_{50}(\text{config}) \;\approx\; \xi_{50}^{\infty} \;-\; A\,e^{-d/\ell}\,\big[\text{opposite-sign neighbor at range } d\big] \;-\; \gamma\,|Q|^2 \quad (\text{hypothesis; tested directly in Section 3.6}),
$$

with the pair experiment fixing the short-range amplitude $A\approx0.09$ (at $d=8$) and decay length $\ell\approx6.5$ cells. The $|Q|^2$ term was proposed to explain why five packed like cores (`all +`, Section 3.4) are markedly weaker ($\xi_{50}=0.876$) than the two-core plateau, on the reasoning that net winding accumulates a logarithmic confinement energy. That reasoning is plausible but was not, at this point, tested independently of the annihilation term — the only two-core data available (`all +` vs `mixed5`) confound charge with core count. Section 3.6 isolates $|Q|^2$ directly and reports the result honestly: **the hypothesis does not survive the test.**

### 3.6 Testing $|Q|^2$ directly: pair vs. triangle, and a falsification

To isolate the $|Q|^2$ term from geometry, we hold the charge *fixed at a nonzero value* and vary only the core arrangement — the same logic as Section 3.5, extended to a third charge point. We add an all-$+1$ equilateral triangle ($|Q|=3$; three cores, each pinned, decoded by nearest-core assignment as before) and sweep its side length $s$ over the identical values used for the pair's separation $d = \{8,14,20,26,32\}$, with every core kept well clear of the boundary (worst case, $s=32$, places one core 8 cells from the pinned edge — the same clearance the $|Q|=2$ pair has at its largest separation).

| $s$ | $\xi_{50}$ $\{+,+,+\}$ ($|Q|=3$) |
|-----|----------------------------------|
| 8 | 0.950 |
| 14 | 1.000 |
| 20 | 1.000 |
| 26 | 1.025 |
| 32 | 1.021 |

The triangle shows the same qualitative shape as the like-charge pair — a mild rise with separation, not a fall — and no sign of extra weakness from the additional core. To compare charge points on equal footing we fit each family's *asymptotic plateau* $\xi_{50}^{\infty}$ with a saturating exponential ($\xi_{50}(d) = \xi_{50}^{\infty} - A\,e^{-d/\ell}$, least squares over $\ell$) rather than reading off the last measured point:

| $|Q|$ | family | $\xi_{50}^{\infty}$ | fit $R^2$ |
|-------|--------|--------------------|-----------|
| 0 | dipole $\{+,-\}$ | 1.017 | 0.958 |
| 2 | pair $\{+,+\}$ | 1.031 | 0.901 |
| 3 | triangle $\{+,+,+\}$ | 1.023 | 0.941 |

A quadratic fit $\xi_{50}^{\infty} = c_0 - \gamma\,|Q|^2$ across these three points gives $\gamma = -0.0006$ with $R^2 = 0.15$ — indistinguishable from zero, and even the sign is wrong for a penalty. A higher-trial confirmation at matched large separation ($s=d=32$, 16 trials, fine grid) reproduces the null result directly: $\xi_{50}^{\infty}(|Q|{=}0)=1.017$, $\xi_{50}^{\infty}(|Q|{=}2)=1.038$, $\xi_{50}^{\infty}(|Q|{=}3)=1.019$ — a non-monotonic scatter of about 2%, matching the trial-noise floor established throughout this paper, with no trend in $|Q|$.

**The $|Q|^2$ hypothesis is falsified over $|Q| \in \{0,2,3\}$.** Robustness at fixed, well-separated core geometry does not measurably depend on net charge in this range. This means the five-core `all +` word's weakness cannot be explained by a smooth charge-squared confinement term that is "negligible below $|Q|=2$–3 and dominant at $|Q|=5$" — the mechanism proposed in Section 3.5 does not survive direct measurement, and the paper is corrected accordingly: the prefactor's dependence on $|Q|$ claimed in Section 3.4 was real (`all +` and `all -` are measurably weaker than `sparse`) but its attribution to a smooth charge-squared law is wrong. Two structural differences remain, uncontrolled here, between the `all +` word and this section's pair/triangle: (i) the word's five cores are *collinear*, each interior core flanked by same-sign neighbors on both sides, whereas the triangle spreads three cores symmetrically in two dimensions; and (ii) the word simply has *more* cores (five) than tested directly here (two, three). Section 3.7 separates these.

### 3.7 Count vs. collinearity: it is density, not the line

To decide between the two candidates left by Section 3.6, we fix everything except arrangement. All cores carry $+1$; nearest-neighbor spacing is held at $s=9$ (matching the word's site spacing); and we compare a **collinear line** of $n$ cores against a **compact regular $n$-gon** (a "ring") of the same side length, for $n \in \{3,4,5\}$. Line and ring share core count, charge, and local spacing; they differ only in whether the cores lie on a line or spread symmetrically in two dimensions.

| $n$ | line ($\xi_{50}$) | ring ($\xi_{50}$) | ring − line |
|-----|-------------------|-------------------|-------------|
| 3 | 0.983 | 0.975 | −0.008 |
| 4 | 0.920 | 0.910 | −0.010 |
| 5 | 0.917 | 0.917 | 0.000 |

**Collinearity is ruled out.** Line and ring are indistinguishable at every count (differences $\le 0.01$, inside the trial-noise floor), so being in a line contributes nothing beyond being close. What *does* move the threshold is core count: both families fall at $\approx -0.03$ per added core ($n{=}3 \to 0.98$, $n{=}4 \to 0.91$, $n{=}5 \to 0.92$), and the collinear five-core line ($0.917$) approximately reproduces the `all +` word ($0.876$), confirming the word's weakness is a geometry effect fully captured by count-at-fixed-spacing.

The apparent tension with Section 3.6 — where charge (hence same-sign count) showed *no* effect — resolves cleanly on the controlled variable. Section 3.6 measured the *large-separation asymptote*, spreading the cores until they no longer interact; there, adding charge does nothing. This section holds the spacing *tight* ($s=9$) throughout. So the operative quantity is neither net charge nor count in isolation but **core density**: the cumulative strain of many same-sign cores packed within an interaction length. Spread them out (Section 3.6) and the penalty vanishes; pack them (here, and the word) and each additional neighbor within range costs $\sim 0.03$ in $\xi_{50}$, independent of whether the packing is a line or a cluster. This closes the decomposition: the survival prefactor is set by *local core density and sign relationships within an interaction length* $\ell \approx 6.5$ cells — a short-range annihilation penalty for opposite neighbors (Section 3.5) and a short-range crowding penalty for like neighbors (this section) — and is flat in everything at larger range.

### 3.8 The depinning law, measured: a universal critical angular wandering

Everything above treats $\xi_{50}$ as an outcome to be fit. The $\sqrt{\lambda}$ law (Section 3.3) came with a proposed *mechanism* — depinning when the accumulated angular wandering $\langle\Delta\theta^2\rangle \propto \xi^2/\lambda$ reaches a critical value — but that wandering was never measured; it was inferred from the threshold. Here we measure it directly and turn the phenomenology into a quantitative theory.

**A stability bound first.** The synchronous (Jacobi) update $\theta \mathrel{+}= \lambda\sum_j\sin(\theta_j-\theta_i)$ linearizes about a smooth field to the iteration matrix $I+\lambda L$, with $L$ the 4-neighbor graph Laplacian (eigenvalues in $[-8,0]$); $|1-8\lambda|<1$ requires $\lambda < 0.25$. Above that, a high-frequency mode saturates — winding readout, being integer, still survives (which is why the survival sweeps returned sensible $\xi_{50}$ up to $\lambda=0.33$), but the continuous field roughens and $\langle\Delta\theta^2\rangle$ is polluted. All measurements in this section stay strictly in the stable regime $\lambda \in [0.10, 0.22]$, and $\xi_{50}$ is re-measured there.

**The wandering law, measured directly.** For the `mixed5` word we compute $\langle\Delta\theta^2\rangle$ as the variance of the free interior cells about the noiseless relaxed field, time-averaged in the intact regime ($\xi$ up to $0.8\,\xi_{50}$), across five couplings. Plotted against $\xi^2/\lambda$, all five $\lambda$ collapse onto a single line:

$$
\langle\Delta\theta^2\rangle \;=\; \kappa\,\frac{\xi^2}{\lambda} \;-\; 0.006, \qquad \kappa = 0.134, \quad R^2 = 0.984.
$$

The intercept is zero within noise and the collapse is tight ($R^2=0.984$) over a $2.2\times$ range in $\lambda$. This is the diffusive-balance mechanism **confirmed as a direct measurement**, not read off the threshold — the wandering really does scale as noise-power over stiffness.

**A universal depinning angle.** Evaluating the measured wandering law at each independently measured $\xi_{50}(\lambda)$ gives the critical wandering at which the word actually fails:

| $\lambda$ | $\xi_{50}$ | $\theta_c^2$ | $\theta_c$ |
|-----------|-----------|--------------|------------|
| 0.10 | 0.676 | 0.607 | 0.779 rad |
| 0.13 | 0.747 | 0.570 | 0.755 rad |
| 0.16 | 0.792 | 0.520 | 0.721 rad |
| 0.19 | 0.881 | 0.542 | 0.736 rad |
| 0.22 | 0.943 | 0.536 | 0.732 rad |

$\theta_c^2 = 0.555 \pm 0.031$ — constant to $5.5\%$ over the whole range. Depinning is a **fixed-wandering (Lindemann-like) criterion**: the memory fails when the free field has wandered a universal angle $\theta_c \approx 0.745\,\text{rad} \approx 0.24\pi$ from its pinned configuration — a quarter-turn local excursion, which is physically just the scale at which a plaquette's phase can slip a full winding. Combining the two measured constants gives a threshold that is no longer fit but *derived*:

$$
\boxed{\;\xi_{50}(\lambda) \;=\; \sqrt{\frac{\theta_c^2}{\kappa}\,\lambda} \;=\; 2.03\,\sqrt{\lambda}\;}
$$

from $\kappa=0.134$ (wandering law) and $\theta_c^2=0.555$ (depinning angle), each measured independently of the threshold itself.

**Honest residual.** The self-measured $\xi_{50}$ exponent in this clean regime is $0.42$, not exactly $0.5$ — essentially the same $0.42$–$0.44$ found earlier, so the sub-$\sqrt{\lambda}$ deviation is *physical*, not the high-$\lambda$ instability. Its source is visible in the table: $\theta_c^2$ drifts weakly downward with $\lambda$ (from $0.61$ to $0.54$, $\sim 12\%$), so the depinning angle is fixed only to leading order and softens slightly as relaxation strengthens. The theory is therefore quantitative and predictive to $\sim 10\%$: one measured wandering law, one nearly-universal depinning angle, and a derived prefactor ($2.03$) that agrees with the phenomenological fit ($1.94$, Section 3.3) within that margin.

### 3.9 Finite-size scaling: how much is the box?

Every threshold above is measured on a single $52^2$ pinned lattice, so absolute values carry a boundary contribution. We isolate it by holding the physical configuration fixed — one pinned $\{+,-\}$ pair at separation $d=16$, $\lambda=0.16$ — and growing only the lattice, $N \in \{36, 52, 72, 100\}$, so the sole change is how far the pinned edge sits from the cores. (The simulation is reimplemented with $N$ as a parameter, since the production core hard-codes $52$.)

| $N$ | edge clearance | $\xi_{50}$ |
|-----|----------------|-----------|
| 36 | 10 | 0.886 |
| 52 | 18 | 0.858 |
| 72 | 28 | 0.853 |
| 100 | 42 | 0.847 |

$\xi_{50}$ falls with box size and converges: successive changes are $-0.028, -0.005, -0.006$, and a boundary-correction fit $\xi_{50}(N) = \xi_\infty + c/N$ gives $\xi_\infty = 0.823$ ($c=2.14$, $R^2=0.93$). A nearby pinned boundary *raises* the threshold — it stiffens the field around the cores — but the effect is small and short-ranged: the $52^2$ value sits $4.2\%$ above the extrapolated bulk, and by $N=100$ the threshold has all but stopped moving. Finite size therefore shifts absolute thresholds by a few percent, cleanly captured by a $1/N$ correction; it does not touch the *ratios* the rest of the paper is built on — scaling exponents, the wandering collapse, the critical angle, and the composition contrasts are all differential and boundary-robust.

---

## 4. Interpretation for the AAMT framework

Three framework claims are now measured rather than asserted. (i) Geometry can hold data: a word survives $\sim10^3$ noisy sweeps with every cell perturbed twice per tick, because the readout is an integer count, not an analog value. (ii) $O_x$ is real and necessary: without the chiral tie-break, perfectly symmetric configurations have no defined evolution; with it, symmetry breaks reproducibly — the same object as signed zero. (iii) "Trinary not binary" is an engineering fact here, not a slogan: the stored alphabet is balanced ternary, and the protecting invariant lives in $\pi_1(S^1)=\mathbb{Z}$, which admits the three local charges $-1, 0, +1$ as the natural resolution of the binary $\pm$ standoff by rotation through a third state — the Bloch-wall lesson.

What the framework should *drop* is equally clear: the survival law is governed by ordinary statistical mechanics (fluctuation–dissipation), with no appeal to the cosmological or metaphysical layers. The symbolic layer (the triangles, the seed cycle, the caduceus) remains a meaning-layer for the author; it earns no claim on the physics. The physics earns its own claims by running.

---

## 5. Reproduction

All results regenerate from the visualizer repository:

```bash
node scripts/lambda-sweep.mjs         # Section 3.3: λ-sweep + both fits, ~20 min
node scripts/word-sweep.mjs           # Section 3.4: word archetypes at fixed λ, ~2 min
node scripts/lambda-word-sweep.mjs    # Section 3.4: √λ universality across words, ~5 min
node scripts/separation-sweep.mjs     # Section 3.5: charge vs geometry decomposition, ~5 min
node scripts/triangle-sweep.mjs       # Section 3.6: |Q|^2 falsification (needs the JSON above first), ~3 min
node scripts/packing-sweep.mjs        # Section 3.7: count vs collinearity (density), ~5 min
node scripts/theory-sweep.mjs         # Section 3.8: measured wandering law + critical angle, ~8 min
node scripts/finiteSize.mjs           # Section 3.9: finite-size scaling of ξ50, ~2 min
```

All sweeps share one seeded trial definition (`scripts/sweepLib.mjs`) so results are directly comparable. The interactive **Chiral Lattice** tab (MaiiaM Alchemist visualizer, EXP-01) exposes the same core: select a word archetype (or click individual trits), drive the noise slider to find the cliff, and run the in-browser Threshold Hunt (Web Worker) to reproduce the survival curve and $\xi_{50}$ with a displayed seed. Simulation core: `src/latticeCore.js`. Sweep engine: `src/latticeWorker.js`. Deterministic RNG: `mulberry32`. Result artifacts: `scripts/*-result.json` (one per experiment).

---

## 6. Limitations and next steps

The remaining gaps are quantitative rather than structural. The density penalty ($\sim0.03$ per like near-neighbor) and the annihilation amplitude and length ($A\approx0.09$, $\ell\approx6.5$) are each estimated from a handful of counts and separations, not a dense grid; a two-dimensional sweep over count and spacing would map the full density kernel. The dynamics are the synchronous Jacobi update, valid only for $\lambda<0.25$ (Section 3.8); a semi-implicit or Metropolis-thermal integrator would extend the accessible coupling range and let us check whether the annihilation length $\ell$ matches the equilibrium vortex-pair correlation length, and whether the $\sim12\%$ downward drift of the critical angle $\theta_c^2$ with $\lambda$ (the source of the $0.42$ vs $0.5$ exponent) is a discretization artifact or a genuine sub-leading correction. Finite-size scaling (Section 3.9) fixed the boundary contribution at a single separation and $\lambda$; whether $\theta_c$ and the composition penalties are themselves $N$-independent is untested. Closing these would promote the present $\sim10\%$ depinning theory to a fully calibrated one — but the mechanism, the scaling law, the critical angle, and the decomposition of the prefactor are now measured, not assumed.

---

*Companion artifacts: `scripts/lambda-sweep-result.json`, `word-sweep-result.json`, `lambda-word-sweep-result.json`, `separation-sweep-result.json`, `triangle-sweep-result.json`, `packing-sweep-result.json`, `theory-sweep-result.json`, `finite-size-result.json` (raw data + fits); MaiiaM Alchemist visualizer EXP-01 tab (interactive reproduction with word-archetype presets).*
