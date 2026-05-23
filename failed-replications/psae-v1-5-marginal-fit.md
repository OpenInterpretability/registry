# PSAE v1.5 — predictive trajectory falsified by shuffled-source baseline

**Original claim:** Predictive SAE v1.5 learns to predict which top-k SAE features will activate at the next reasoning step, with recall@1024 = 0.83–0.87 across 12 layer×position sites.
**Walked back:** 2026-05-16
**Failure mode:** `marginal_fit`
**Diagnostic that caught it:** shuffled-source baseline (X_train shuffled, y_train kept)

## Evidence

Recall@1024 reproduced **±0.03** by the shuffled-source baseline at all 12 sites:

| Condition | mean recall@1024 (12 sites) |
|---|---|
| Real X | 0.85 |
| Shuffled-source (X shuffled, y kept) | 0.83 |
| Δ(REAL − B1) | < 0.03 everywhere |
| Cohen's d | < 0.15 |

The "predictive structure" the probe appeared to learn was **the marginal distribution of end-of-thinking features** — the most-active features at the target position are the same most-active features regardless of which prompt you shuffle in.

## What it teaches

Any linear probe predicting a **sparse top-k target** (SAE features, MoE routing, top-k vocab) at N_train < 200 must report the shuffled-source baseline. Without it, recall@k looks impressive when the probe has effectively memorized "always predict the top-k of the marginal."

This sister rule to the [capacity sweep](https://openinterp.org/research/papers/two-forms-epiphenomenal-probes) is now formalized in the OpenInterp Six Diagnostics protocol.

## Links
- Paper (reframed as honest-negative methodology): https://openinterp.org/research/papers/marginal-fit-pathology
- Memory: `project_psae_v15_falsified_by_b1_shuffle`
- Methodology rule: `feedback_shuffled_source_baseline_for_sparse_topk_probes`
