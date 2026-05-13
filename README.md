# AAMT Technical Papers

Preprint series accompanying the MaiiaM Alchemist project, documenting
the novel ML techniques developed alongside the AsAManThinks platform.
Target audience: ML engineers who read arXiv daily.

Each paper is a self-contained preprint with abstract, math, related
work, proposed evaluation, and honest limitations. Empirical results
are preliminary; benchmark runs are in progress and will land in v1.1
versions of each preprint.

## Series

| # | Paper | Version | Words | DOI | Headline contribution |
|---|---|---|---|---|---|
| 1 | [Vortex-Keyed Mixture-of-Experts Routing](01-vortex-keyed-moe-routing.md) | v1.0 | 4,140 | [10.5281/zenodo.20150167](https://doi.org/10.5281/zenodo.20150167) | Closed-form, deterministic, interpretable MoE gate via TERA → Meji bit-mask |
| 2 | [Yare 3-6-9 Rotary Positional Encoding](02-yare-369-rope.md) | v1.0 | 4,004 | [10.5281/zenodo.20150179](https://doi.org/10.5281/zenodo.20150179) | Digital-root frequency schedule for RoPE (`VORTEX_DOUBLING_CYCLE = (1, 2, 4, 8, 7, 5)`) — two implemented variants with HuggingFace `inv_freq` contract compatibility |
| 3 | [Odu-256 Training Curriculum](03-odu-256-curriculum.md) | v1.0 | 5,213 | [10.5281/zenodo.20150181](https://doi.org/10.5281/zenodo.20150181) | 256-state product taxonomy (16 Meji × 16 Meji) for stratified training + evaluation, with lineage-token validator preventing Ifá names from leaking into user-facing strings |
| 4 | [Frequency Abliteration with Lineage Substitution](04-frequency-abliteration-lineage-substitution.md) | v1.0 | 5,037 | [10.5281/zenodo.20150193](https://doi.org/10.5281/zenodo.20150193) | Activation-space editing that substitutes ablated directions with corpus-grounded projections; rank-1 math, per-edit perplexity guard, schema-v1 ledger |
| 5 | [Multiverse Superposition Inference](05-multiverse-superposition-inference.md) | v1.0 | 4,573 | [10.5281/zenodo.20150195](https://doi.org/10.5281/zenodo.20150195) | Multi-path decoding with deferred collapse + externally-signal-weighted aggregation; adapter-level implementation shipped, token-level proposed |

**Total: 22,967 words across 5 preprints.** All v1.0 published on Zenodo
2026-05-13 under CC BY 4.0. Series root deposit:
[10.5281/zenodo.19600795](https://doi.org/10.5281/zenodo.19600795)
(*AAMT Mathematical Foundations: A Unified Framework for Biological and
Artificial Intelligence (Papers I-V)*). Empirical sections clearly
distinguish "shipped" vs "proposed for v1.1".

## Themes

The papers are individually contributory and collectively form a
**stack** for interpretable, auditable, and grounded language models:

- **Paper 1 (Vortex)** — the routing primitive. How do we decide
  which expert processes which token, in a way that's auditable?
- **Paper 2 (Yare RoPE)** — the positional substrate. How do we
  encode position with predictable frequency structure?
- **Paper 3 (Odu-256)** — the training/evaluation taxonomy. How do
  we organize data and stratify evaluation by a principled 256-state
  space?
- **Paper 4 (Frequency abliteration)** — the activation-space
  intervention. How do we *substitute* model behavior with curated
  content rather than *remove* or *steer*?
- **Paper 5 (Multiverse superposition)** — the decoding strategy.
  How do we maintain multiple inference paths and collapse them with
  principled signal aggregation?

Each paper stands alone but the five together describe a coherent
alternative architecture for transformer LMs in safety-critical
deployment. Cross-references between papers are explicit (e.g.,
Paper 1's TERA register is reused as a credence signal in Paper 5;
Paper 3's curriculum naturally aligns with Paper 1's archetypes;
Paper 4's directions can be defined per-expert in a Vortex-routed
model).

## Code grounding

Every paper is grounded in real implementation found in
`/Volumes/Abundance/maiiam-alchemist/`. Notable artifacts:

| Paper | Key implementation files |
|---|---|
| 1 | `packages/harmonic-routing/src/vortex_gate.py` (TERAProjection + meji_resolve) |
| 2 | `packages/training-pipeline/training_pipeline/yare/vortex_rope.py` (`patch_rope`/`revert_rope`, two schedules) |
| 3 | `packages/aamt-foundations/yare-vortex-mathematics.json` + `seed_oracle_cards.py` + `apps/oracle/lib/odu-data.ts` |
| 4 | `training_pipeline/abliteration/direction_probe.py` + `stages/abliterate.py` + `concept_ledger.py` |
| 5 | `training_pipeline/multiverse/{parallel_train,heartscale_blender,adapter_bank}.py` + `harmonic-engine/.../multiverse_endpoint.py` |

## Conventions

- **Markdown** (`.md`). Convertible to PDF via `pandoc <file>.md -o
  <file>.pdf --pdf-engine=xelatex` once a LaTeX engine is installed.
- **Math notation** uses LaTeX-style `$$...$$` blocks compatible with
  pandoc/MathJax.
- **Honest limitations** in every paper. No fabricated benchmark
  numbers. Preliminary status clearly marked. v1.1 will report actual
  empirical runs.
- **Real prior-work citations.** Each paper has a references section
  with legitimate ML literature.
- **Code references** point to actual files in the MaiiaM Alchemist
  and AsAManThinks platform repositories.
- **Naming policy.** Where AAMT/Ifá-derived names are used (archetypes,
  Odu names), the paper treats them as documentation/labels for the
  mathematical structure, not as claims of divinatory or metaphysical
  efficacy. Papers 3 and 4 in particular have explicit disclaimers
  on this point.

## Patent strategy

The techniques described here are subject to a coordinated patent
strategy:

- **Provisional filings** within 90 days on the TERA / Vortex /
  HeartScale / Wings core IP.
- **Method patents** on the interpretable-routing primitive (Paper 1)
  and the activation-substitution mechanism (Paper 4).
- **Continuation patents** on the self-improvement loop (Alchemist
  closed-loop training) and the activity-stream-driven context
  architecture.

Preprint publication establishes priority date for non-patentable
contributions and supports the technical case in patent prosecution.

## What's next (v1.1)

The following empirical work is planned for v1.1 of each preprint:

- **Paper 1** — Train Gemma-2-2B with Vortex routing vs. Switch
  Transformer + Hash Layers baselines on MMLU/HellaSwag/ARC/TruthfulQA;
  report routing entropy + expert specialization + counterfactual
  TERA ablations.
- **Paper 2** — Long-context evaluation on LongBench, Needle-in-a-
  Haystack at 8k/32k/128k, passkey retrieval. Compare both Yare
  schedules against vanilla RoPE / NTK-aware / YaRN / LongRoPE.
- **Paper 3** — Pretrain a small model with Odu-stratified data
  mixture vs. uniform random; stratified per-Odu eval on diversity
  metrics + standard benchmarks.
- **Paper 4** — Run direction probes on Qwen 2.5 3B / Gemma 2 2B /
  LLaMA 3.2 3B; ablation vs. ablation+substitution; faithfulness +
  hallucination + perplexity metrics with the Akashic Vault as
  substrate corpus.
- **Paper 5** — Token-level MSI implementation (currently adapter-
  level); reasoning benchmarks (GSM8K, MATH, HotpotQA); compute +
  latency trade-off vs. greedy / beam / self-consistency baselines.

## License

These preprints are made available under CC BY 4.0 for research and
educational purposes. The underlying implementations are subject to
the licenses of their respective repositories.

## Citing

If you reference any preprint in academic or commercial work, please
cite the Zenodo DOI for permanent record. Example:

> Whitehead, W. C. (2026). *Vortex-Keyed Mixture-of-Experts Routing:
> A Deterministic, Interpretable Gating Primitive.* AsAManThinks
> Technical Report, Preprint v1.0. Zenodo.
> https://doi.org/10.5281/zenodo.20150167

BibTeX is auto-generated on each paper's reader page at
`asamanthinks.com/research/{slug}` (click "Copy BibTeX").

## Contact

Author: Weslyn Cory Whitehead Jr.
yarethewatchman@gmail.com
[asamanthinks.com](https://asamanthinks.com)
