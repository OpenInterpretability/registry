# nb46 — multi-probe ensemble OOD claim walked back

**Original claim:** Inference-time fusion of multiple Qwen3.6-27B activation probes (FG + RG ensemble) produces +6.7 pp accuracy lift on TruthfulQA, generalizing the "ProbePack universal-middleware" thesis across distributions.
**Walked back:** 2026-05-01
**Failure mode:** distributional `env_coupling` (within-distribution vs cross-distribution)
**Diagnostic that caught it:** cross-distribution evaluation (TruthfulQA + StrategyQA + TriviaQA)

## Evidence

Cross-distribution test on three benchmarks, mean lift over baseline:

| Benchmark | Single-probe (FG) | Ensemble (FG + RG) | Δ Ensemble vs Baseline |
|---|---|---|---|
| TruthfulQA | +6.7 pp (original within-distribution) | varies | **−0.002 mean** |
| StrategyQA | 0.605 AUROC | similar | within noise |
| TriviaQA | 0.710 AUROC (FG alone survives) | similar | within noise |

**0 of 3 distributions** show the ensemble lift surviving cross-distribution. Mean Δ = −0.002. The original +6.7 pp was a within-distribution-only effect.

## What it teaches

A probe trained and evaluated on the same distribution captures patterns specific to that distribution. Reporting performance on the same distribution as training gives a near-upper-bound estimate; performance on truly new distributions is often the actual production-relevant number.

The lesson: **always test cross-distribution before claiming generalization**, even when within-distribution numbers look strong. Single-probe FG survives the cross-test (0.710 on TriviaQA) and ships in FabricationGuard v0.2.0. Multi-probe ensemble does not, and the "ProbePack universal-middleware" framing was reframed to "FG single-probe holds OOD on factual; ensemble doesn't transfer."

Paper-4 was rewritten as an honest-negative reporting this exact gap.

## Links
- Paper-4 (honest-negative reframe): https://openinterp.org/research/papers/ensemble-ood-walkback
- Memory: `project_nb46_verdict_ensemble_walkback`
- Original notebook (nb45 within-distribution): `notebooks/nb45_inference_ensemble.ipynb`
- Cross-distribution notebook: `notebooks/nb46_cross_distribution_ensemble.ipynb`
