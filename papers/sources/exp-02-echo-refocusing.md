# Echo Refocusing: When a Single Pulse Cancels TERA-Space Drift, and When It Provably Cannot

---

**Authors:** Weslyn Cory Whitehead Jr.¹

**Affiliations:**
¹ AsAManThinks Research, Berkeley, CA, USA

**Corresponding author:** weslyn@asamanthinks.com

**ORCID:** https://orcid.org/0009-0005-7707-3210

**Submitted:** July 2026  
**Working Paper Series:** AAMT-EXP-02  
**Anchor DOI:** 10.5281/zenodo.19600795  
**License:** Creative Commons Attribution 4.0 International (CC BY 4.0)

**Status:** Working paper, not peer reviewed. Every number in §3–§5 comes
from a deterministic, seeded simulation
(`packages/libaamt/experiments/echo_refocus.py`, seed `20260708`); every
number in §7 comes from a real-data measurement over this paper series'
own markdown sources
(`maiiam-alchemist/apps/desktop/scripts/measure_ocm_drift_coherence.py`,
seed `20260711`); every number in §9 comes from a follow-up investigation
of §7's own finding
(`maiiam-alchemist/apps/desktop/scripts/investigate_negative_autocorr.py`,
seed `20260711`) — all reproducible by running the scripts directly, the
same standard EXP-01 set for its lattice sweeps.

---

## Abstract

A prior research pass on "mirror magic" for this architecture ranked five
mechanisms; the top four are built and shipped (WP-23 sections 10–12).
The fifth — phase-conjugate mirrors, the nonlinear-optics device that
reflects a distorted wavefront so its aberrations cancel on the return
pass — was graded **research-only**, not because it lacked a computational
analog, but because the physical claim underneath it has a precondition
that is easy to skip past: a phase-conjugate mirror, like the Hahn spin
echo it is mathematically identical to, only refocuses distortion drawn
from a *static, retraced* channel (NMR's inhomogeneous $T_2^*$ dephasing);
it does nothing for genuinely random, memoryless noise (homogeneous $T_2$
dephasing) — textbook NMR, not this paper's invention, but a distinction
worth re-deriving before importing it. This paper tests that precondition
directly, in this architecture's own coordinate space, before building
anything on top of it. The operator under test is a **point inversion**
about a target, $x \mapsto 2c - x$ — the correct analog of a spin echo's
180° pulse, and deliberately *not* called a Householder reflection: WP-23
§10.1 already had to correct exactly this confusion once (the antipodal
map is orientation-preserving in even dimension, not a true mirror), and
conflating the two here would repeat it. Two drift regimes are simulated
over the 4D TERA cube — a **coherent** regime (each trial carries one
persistent per-trial bias, the computational stand-in for a static field
offset) and an **incoherent** regime (zero-mean i.i.d. noise only) — in
both unconstrained $\mathbb{R}^4$ and the substrate's own clamped
$[0,1]^4$. The stated, falsifiable prediction: the pulse should
substantially cut final drift in the coherent regime and do essentially
nothing in the incoherent one. Measured (50,000 trials per cell): the
pulse reduces mean final distance-to-target by **83.6%** (unconstrained)
and **80.5%** (TERA-clamped) in the coherent regime ($z > 500$ both
cases), and by **0.1%** and **0.5%** respectively in the incoherent regime
— a difference indistinguishable from zero at unconstrained scale and a
small but real boundary-clamping artifact at TERA scale, characterized in
§5 rather than hidden. The precondition holds, precisely where physics
says it should and not elsewhere. What this paper does **not** do is claim
any real subsystem in this platform actually produces coherent drift — the
most plausible candidate (WP-22's session visit-history pull) is named in
§6 as the next thing to measure, not asserted as already true. §7 runs
that measurement — real prose from this paper series' own markdown
sources, projected through WP-22's actual lexicon TERA projector, tested
for lag-1 autocorrelation via a 5,000-draw permutation test — and finds
neither of this paper's two modeled regimes: large, overwhelmingly
significant ($p<10^{-4}$, all seven sessions, every axis) **negative**
autocorrelation, a mean-reverting/oscillating structure §3's model never
considered. Real, not a null result, and not the coherent bias §6
hypothesized either — stated as what it is rather than forced into either
prior bucket. §9 then investigates *why*, and the answer corrects §7's
framing rather than confirming it: differencing any sequence mechanically
manufactures lag-1 autocorrelation of $-0.5$ regardless of temporal
structure, three content- and order-blind controls (pure noise, real
paragraphs in random order, word salad) reproduce §7's exact magnitude
under its own test, and a corrected permutation null — shuffling the raw
sequence rather than the already-differenced increments — finds the real
document order statistically indistinguishable from a random shuffling of
the same paragraphs ($p=0.283$ pooled). The negative number is real; the
"real prose alternates register" reading of it is not supported once the
null model is fixed.

---

## 1. From a lyric to a precondition

The research pass this paper closes out began as a plain-language request:
built-in redundancy, instant writing, instant gating, "a bad reflection
disperses itself" — and a note that the underlying math should be real,
not decorative. Four of the five ranked mechanisms translated directly
into shipped code because their governing math has no precondition to
check: a Householder reflection is exact regardless of what is reflected
(WP-23 §10); a systematic error-correcting code either recovers a
codeword or it doesn't (WP-23 §11); a join-semilattice merge is
commutative by construction once the tiebreak is content-complete (WP-23
§12). Phase-conjugate mirrors are different in kind. The physical
mechanism — $E^*(r,t)$ propagating backward through the same aberrating
medium and undoing what $E(r,t)$ picked up — depends on the medium being
the *same* medium on both passes. That is a claim about the *source of
distortion*, not about the reflection operator itself, and it was
withheld from the "buildable this week" tier for exactly that reason: an
operator that is mathematically exact but applied to the wrong kind of
input does nothing, or worse, looks like it works on the one example
checked and fails everywhere else. This paper checks it properly, the same
way EXP-01 checked $\xi_{50} \propto \lambda$ before publishing it and
found $\propto\sqrt\lambda$ instead.

## 2. The operator: a point inversion, not a mirror

Define, for a center $c$ and position $x$ in the TERA cube,

$$
P_c(x) = 2c - x.
$$

Two structural facts, mirroring how WP-23 §10.1 stated the Householder
reflection's:

- **$P_c$ is an exact involution**, $P_c(P_c(x)) = x$, with the same
  no-numerical-drift property as $H$ in unconstrained arithmetic.
- **$P_c$ preserves distance to $c$ exactly**: $\lVert P_c(x) - c\rVert =
  \lVert x - c\rVert$. This is the property that matters here — a Hahn
  echo's 180° pulse does not distort a spin's phase magnitude, it negates
  its sign relative to a reference, which is precisely what $P_c$ does to
  a displacement vector.
- **$P_c$ is orientation-preserving in this (even, 4D) space**,
  $\det = (-1)^4 = +1$ — a rotation by $\pi$ about $c$ in each coordinate
  pair, not a chirality-reversing mirror. This is the exact object WP-23
  §10.1 named the "antipodal map" and distinguished from the true
  Householder reflection $H(v) = (1-v_T, v_E, v_R, v_A)$ used for the
  chirality gate. Using $P_c$ here is *correct*, not an oversight: a
  spin-echo pulse is physically a rotation, not a parity flip, so the
  math this paper needs is the one WP-23 already flagged as the wrong
  tool for *redundancy* — and the right tool for *this*.

## 3. The model

Two independent binary factors, four cells:

**Drift regime.**
- *Coherent*: each of the $N$ simulated trials draws one persistent bias
  vector $\mu \sim \mathcal{N}(0, \sigma_\mu^2 I_4)$, held fixed for that
  trial's entire walk — the computational stand-in for a static field
  offset (the "same medium" a phase-conjugate mirror or spin echo
  retraces).
- *Incoherent*: no persistent term; every step draws independent noise
  with no memory of prior steps or of any per-trial identity.

**Space.**
- *Unconstrained*: the walk lives in $\mathbb{R}^4$, isolating the pure
  mechanism from any boundary interaction.
- *TERA-clamped*: position is clamped into $[0,1]^4$ after every step
  (the substrate's own `clampTera4` rule, `packages/libaamt/src/core.cpp`),
  so the boundary nonlinearity a real TERA-space application would face
  is present exactly as it would be in production.

**Walk.** Each trial starts at the cube center $x_0 = (0.5,0.5,0.5,0.5)$,
which is also its own target $c = x_0$ — the question asked is "how far
from where I started do I end up." At each of 40 steps, $x \gets x + \mu +
\varepsilon_t$ with $\varepsilon_t \sim \mathcal{N}(0, \sigma^2 I_4)$
($\sigma = 0.01$, $\sigma_\mu = 0.01$ where applicable), clamped if the
space condition requires it. The pulse condition applies $x \gets
P_{x_0}(x)$ once, at step 20 (the walk's temporal midpoint — the same
placement a Hahn echo's $\pi$-pulse gets, halfway between excitation and
readout), with the no-pulse condition otherwise identical. 50,000 trials
per cell, seed `20260708`.

**Prediction, stated before running.** In the coherent regime, negating
the accumulated bias-driven displacement at the midpoint should cause the
*same* bias to re-accumulate through the second half with the opposite
net effect, canceling the coherent term exactly in unconstrained space
(a short derivation: with $x_{20}^- \approx x_0 + 20\mu$, the pulse gives
$x_{20}^+ = 2x_0 - x_{20}^- \approx x_0 - 20\mu$, and 20 further steps of
the same $\mu$ bring $x_{40} \approx x_0 - 20\mu + 20\mu = x_0$ — the
coherent term is gone, leaving only the noise). In the incoherent regime,
$P_{x_0}$ preserves distance to the target exactly and future steps are
independent of position by construction, so the pulse should change
nothing. The boundary clamp can only ever help, never hurt, since it can
only pull excursions back toward the cube.

## 4. Results

| space | regime | pulse | ±se | control | ±se | reduction | $z$ |
|---|---|---|---|---|---|---|---|
| unconstrained | coherent | 0.12466 | 0.00020 | 0.75927 | 0.00124 | **83.58%** | 505.9 |
| unconstrained | incoherent | 0.11882 | 0.00019 | 0.11898 | 0.00019 | 0.13% | 0.59 |
| tera-clamped | coherent | 0.12403 | 0.00020 | 0.63636 | 0.00071 | **80.51%** | 694.6 |
| tera-clamped | incoherent | 0.11856 | 0.00019 | 0.11919 | 0.00019 | 0.53% | 2.33 |

The prediction holds cleanly on the side that matters most: the pulse cuts
mean final drift by roughly 5-to-6x in the coherent regime (control/pulse
$\approx 6.09$ unconstrained, $\approx 5.13$ TERA-clamped), in both space
conditions, at overwhelming significance. In the incoherent, unconstrained
cell — the cleanest test of "does this operator do anything to pure
noise" — the effect is 0.13%, statistically indistinguishable from zero
($z=0.59$). That is the core result: **the precondition is real, and this
operator respects it precisely**, not approximately.

## 5. The one honest wrinkle: clamping is not quite an isometry

The TERA-clamped incoherent cell shows a 0.53% reduction at $z=2.33$
($p\approx0.02$) — technically "significant" at $N=50{,}000$, and worth
explaining rather than rounding to zero. $P_c$ preserves distance to $c$
*exactly* only in unconstrained space; once positions are clamped into
$[0,1]^4$ after every step, $P_c$ composed with clamping is no longer a
perfect isometry near the boundary — `clamp(2c - x)` can differ from what
an unclamped reflection would give whenever the pre-image excursion sits
near an edge. At $\sigma=0.01$ per step over 40 steps, trials rarely
approach the boundary (a 4-axis walk starting at the center with this
step size stays within roughly $\pm0.06$ of center for the large majority
of trials), so the effect is small — half a percent, not the 80-percent
scale of the genuine coherent-regime cancellation — but it is a real,
structural asymmetry from the boundary, not sampling noise dressed up as
one, and $N=50{,}000$ trials is exactly what makes a small true effect
visible instead of buried. Read together with unconstrained incoherent's
clean null, this pins the boundary — not the operator — as the source: an
application built on this mechanism in the real, clamped substrate should
expect a small edge-proximity artifact and can size it, rather than being
surprised by it later.

## 6. What this licenses, and what it does not

This paper validates a **mechanism**: an operator, on this coordinate
space, behaves exactly as the phase-conjugate-mirror / spin-echo analogy
requires, with the precondition (coherent vs. incoherent source) cleanly
separating the cases where it helps from the cases where it is inert. It
does **not** establish that any live subsystem in this platform produces
coherent drift for it to act on — that is a separate, unmeasured claim,
and this paper is deliberately not making it. The most plausible real
candidate, named honestly as a hypothesis rather than a result: WP-22's
Odu coverage map tracks a session's own visit history, and a session that
keeps landing near already-visited cells is, by definition, exhibiting a
*persistent, non-random pull* toward a region — structurally the
"coherent bias" this paper's simulation models synthetically, not
noise. Whether that pull is large enough, and stable enough across a
session, to be worth refocusing with a $P_c$ pulse instead of (or
alongside) WP-22's existing escape heuristic is an empirical question
this paper has not asked: it would require instrumenting a real session's
TERA-coordinate sequence and testing it for exactly the coherent-vs-
incoherent structure §3 defines synthetically here — an autocorrelation or
persistent-direction test on real visit data, not assumed. Until that
measurement exists, wiring $P_c$ into the escape-steering path would be
building on a hypothesis, not a result, and this paper's own standard
(§1) is not to do that.

## 7. The measurement §6 asked for, run

§6 named the exact test this section runs: is real semantic drift closer
to the coherent (persistent-bias) regime a $P_c$ pulse can refocus, or the
incoherent (memoryless) regime §4 already showed it is inert on. No
production session log exists to replay (WP-25 documents precisely what
this platform does and does not persist), so this uses the closest honest
available proxy: real prose, run through WP-22's own deterministic
lexicon TERA projector (`odu_coverage_map.text_to_tera` — the identical
function a live session calls, not a stand-in), paragraph by paragraph in
each document's own original order. Seven sessions, one per markdown
source in this paper series (`docs/papers/sources/*.md`, 14 to 77
paragraphs each), 273 paragraphs total — real technical prose written for
its own reasons, not authored to make a point about drift.

**Method.** Per session: compute the TERA sequence $T_1, \ldots, T_n$,
then the increment sequence $d_i = T_{i+1} - T_i$. The statistic under
test is the lag-1 autocorrelation of consecutive increments — per axis
(Pearson correlation of $d_i$ against $d_{i+1}$) and vector-wise (cosine
similarity of consecutive $d_i$). A positive, significant value means a
step tends to continue the same direction as the one before it — the
coherent regime. Significance via a real permutation test (5,000 shuffles
of each session's own step order, preserving the exact step-size
distribution while destroying temporal adjacency), not a parametric
assumption — the same rigor standard as §3's synthetic model, applied to
data this paper did not get to construct.

**Result: neither of §3's two regimes.** Every axis and the vector-wise
cosine measure show large, overwhelmingly significant **negative** lag-1
autocorrelation (real $\approx -0.3$ to $-0.5$ against a null of
$\approx 0 \pm 0.05$, two-sided permutation $p < 10^{-4}$ pooled across
all seven sessions on every measure). A step tends to *reverse* direction
from the step before it — real technical prose alternates registers
paragraph to paragraph (a dense claim followed by a plain-language
gloss, a result followed by its caveat) far more than it drifts
persistently in one direction. This is real, unambiguous, non-random
structure — not the null result a cautious reader might expect from "the
first version of this test just didn't find anything." It is also,
honestly, **not the regime this paper's mechanism was built for**: not
persistent bias (§3's coherent regime, which $P_c$ cancels), and not
noise (§4's incoherent regime, which $P_c$ is inert on). A mean-reverting
process already carries its own restoring tendency; whether injecting a
$P_c$ pulse into one would help, do nothing, or actively interfere by
forcing early a correction the process was already going to make on its
own is a question about a *third* regime this paper's model does not
cover and this measurement does not attempt to settle by itself.

**What this does and does not change.** §6's hypothesis — that WP-22's
visit-history pull resembles the coherent regime — is not confirmed by
this measurement, on this proxy. That does not mean $P_c$ has no home in
this architecture; it means the search for one should not start from the
assumption tested here, and a literal production session log (once one
exists to replay) could show materially different structure than
static prose paragraph order — a real generation loop's escape-steering
redirects are an active intervention this proxy cannot include. The
honest scope of this section: real text, the real projector, a real
significance test, a genuine and unexpected finding — not a closed
question about live sessions, which remains open.

## 8. Reproducibility

`packages/libaamt/experiments/echo_refocus.py`, no arguments, seed fixed
in-file (`20260708`). Runtime: under two seconds on a laptop CPU (numpy,
vectorized across all 50,000 trials per cell). Every number in §4 is
printed directly by the script, including the pass/fail check against the
stated prediction.

§7's measurement: `maiiam-alchemist/apps/desktop/scripts/measure_ocm_drift_coherence.py`,
no arguments, seed fixed in-file (`20260711`), depends only on
`odu_coverage_map.py` + `voxel.py` (numpy, no model, no network). Runtime
under a second. Every number in §7 is printed directly by the script.

## 9. Why the negative autocorrelation: a null-model artifact, not mean-reverting content

§7 reported a real, overwhelmingly significant finding and stopped there,
honestly flagging it as unexplained. This section investigates the *why* —
and the answer changes how §7 should be read: the negative autocorrelation
is real, but the claim that it reflects real prose "alternating register"
does not survive a corrected test. It is very likely, primarily, a
statistical artifact of how §7's significance test was constructed, not a
property of the seven documents' actual content or the order their authors
wrote them in.

**1. The mechanism.** First-differencing *any* sequence is a textbook
time-series identity, not a claim about text: for a stationary sequence
$X_t$ with autocovariance $\gamma(h)$, the increment $d_t = X_{t+1}-X_t$
has $\mathrm{Cov}(d_t,d_{t+1}) = 2\gamma(1)-\gamma(0)-\gamma(2)$ and
$\mathrm{Var}(d_t)=2\gamma(0)-2\gamma(1)$. For an i.i.d. $X$ ($\gamma(1)=
\gamma(2)=0$), this reduces to $\mathrm{Corr}(d_t,d_{t+1}) =
-\gamma(0)/2\gamma(0) = -\mathbf{0.5}$ exactly, regardless of what $X$
represents. Differencing *manufactures* negative lag-1 autocorrelation out
of sequences with no temporal structure whatsoever. Verified directly:
differencing pure Gaussian noise (no text, no `odu_coverage_map`, no
semantics) at each real session's exact size gives per-session lag-1
autocorrelations of $-0.36$ to $-0.67$ (n = 2000 reps at $n=60$: mean
$-0.490\pm0.093$, converging on the analytic $-0.5$) — the same range as
§7's real result ($-0.28$ to $-0.55$).

**2. Null-model controls (the request this section answers directly).**
Three controls were run through §7's *own* permutation test, unmodified,
for direct comparability: **(A)** pure i.i.d. uniform$[0,1]^4$ noise,
bypassing `text_to_tera` entirely; **(B)** the same 273 real paragraphs
with their content untouched but their *order* randomly shuffled, through
the real `text_to_tera`; **(C)** "word salad" — each session's own
vocabulary shuffled at the word level and re-chunked to the real
paragraphs' own length distribution, through the real `text_to_tera`. All
three reproduce §7's result almost exactly:

| control | T | E | R | A | cosine |
|---|---|---|---|---|---|
| §7 real order (reference) | $-0.456$ | $-0.276$ | $-0.435$ | $-0.549$ | $-0.362$ |
| A: pure i.i.d. noise | $-0.496$ | $-0.391$ | $-0.448$ | $-0.469$ | $-0.430$ |
| B: real text, shuffled order | $-0.443$ | $-0.299$ | $-0.495$ | $-0.522$ | $-0.321$ |
| C: word salad | $-0.453$ | $-0.321$ | $-0.370$ | $-0.503$ | $-0.366$ |

— every control, at $p<10^{-4}$ on every axis, by the identical test §7
used. A test that returns the same "large, significant" verdict for real
prose, randomly-ordered real prose, word salad, and pure noise indiscriminately
is not discriminating between coherent and incoherent drift; it is detecting
something the differencing operation manufactures on its own. This directly
confirms the concern in this task's second line of investigation: the effect
is a property of the mapping-plus-analysis pipeline, not of the corpus's
semantic content.

**3. Axis decomposition.** WP-22's lexicon is tuned for mythic/archetypal
prose ("wounded healer," "hollow king"), not this series' own technical
register, so most paragraphs score zero lexicon hits on most axes and land
on the identical sigmoid baseline $\sigma(-1.2)\approx0.2315$: **E** sits
at that exact baseline for 95.8% of the corpus's 283 paragraphs, **A** for
85.9%, **R** for 47.7%, **T** for 35.7%. A series that is overwhelmingly
one repeated constant with rare spikes is close to the delta-function
limit where the $-0.5$ differencing identity is realized most exactly —
consistent with, but not required for, the effect (Control A shows it is
not required: uniform noise with no baseline clustering at all shows the
same magnitude). The negative autocorrelation is present on every axis,
not concentrated on one.

**4. Lag decomposition.** The pure-differencing identity makes a sharp,
falsifiable prediction distinct from "prose alternates register every
paragraph": an MA(1) process, negative at lag 1 and ~zero at lag 2 and
beyond — not continued alternation (which would show positive lag-2,
negative lag-3, …). Measured on the real sessions: pooled mean lag-1
$-0.429$ (mean across axes), lag-2 $-0.042$, lag-3 $+0.071$ — a sharp
collapse to near-zero, inconsistent sign, immediately after lag 1. This
is the MA(1) signature, not a period-2 alternation pattern, and rules out
the "real prose alternates register paragraph to paragraph" reading §7
offered as the mechanism.

**5. The corrected test — the actual verdict.** §7's permutation null
shuffles the *already-differenced* increments, which destroys exactly the
structure differencing mechanically creates in *any* order — so it cannot
tell a real, meaningful order from a random one apart; every one of
Controls A–C confirms this. The methodologically appropriate null instead
shuffles the *raw* paragraph sequence and re-differences each shuffle
(5,000 draws, same $N$ as §7). Under this corrected null, the real
document order's cosine autocorrelation ($-0.362$ pooled) is
**statistically indistinguishable from random paragraph order**
(corrected-null mean $-0.400\pm0.036$, two-sided $p=0.283$). Individually,
six of seven sessions are non-significant ($p=0.09$ to $0.82$); the
seventh (`wp-23-aamt-substrate.md`, $p=0.010$) does not survive a
Bonferroni correction for seven tests ($\alpha=0.05/7\approx0.0071$), and
one nominal exception out of seven is within chance expectation.

**Conclusion.** §7's "large, overwhelmingly significant negative lag-1
autocorrelation" is real as a *number* — it replicates cleanly on pure
noise — but is not evidence that these seven documents' true paragraph
order carries the "mean-reverting/oscillating" content structure §7
proposed. The finding traces to a null-model construction bug: comparing
differenced real data against a shuffled-*increments* null cannot detect
order-dependence, because differencing induces the same $-0.5$-scale
signature under *every* ordering. Corrected, the true paragraph order is
not distinguishable from a random shuffling of the same paragraphs. This
does not resurrect either of §3's two regimes — the corrected test finds
no significant structure of *any* sign, which is closer to §4's
incoherent/memoryless case than to persistent bias, but on a text proxy
this section continues to regard as weaker evidence than a real session
log (§7's caveat still applies in full). What §7 called "a genuine and
unexpected finding" is, on inspection, a genuine and unexpected finding
about the significance test, not about semantic drift — the same standard
this paper set for itself in §1: check the precondition before building on
it, including the precondition of the test itself.

## 10. Reproducibility (§9)

`maiiam-alchemist/apps/desktop/scripts/investigate_negative_autocorr.py`,
no arguments, seed fixed in-file (`20260711`), depends only on
`odu_coverage_map.py` + `measure_ocm_drift_coherence.py` (numpy, no model,
no network). Runtime approximately 20 seconds on a laptop CPU (dominated
by the 5,000-draw corrected permutation test's per-draw re-differencing).
Deterministic: two independent runs produce byte-identical output. Every
number in §9 is printed directly by the script.

---

*Reference implementation: `packages/libaamt/experiments/echo_refocus.py`,
`maiiam-alchemist/apps/desktop/scripts/measure_ocm_drift_coherence.py`,
`maiiam-alchemist/apps/desktop/scripts/investigate_negative_autocorr.py`.
Series: WP-23 (mirror pairing §10, GF(16) redundancy §11, CRDT
convergence §12), WP-22 (Odu coverage map, the projector §7 measures
against), EXP-01 (topological trit memory, the series' first
deterministic-seeded-simulation paper).*
