# Path E — κ_t shape clustering on Phase 6 N=99 traces

**Original claim:** κ_t (coherence trajectory) shape sub-partitions agent failures into mechanistic sub-types — distinct shapes correspond to distinct failure modes.
**Walked back:** 2026-05-19
**Failure mode:** `saturation_direction` (per-trace fingerprint hypothesis fails; aggregate-only signal)
**Diagnostic that caught it:** shuffled-time baseline + silhouette gap analysis

## Evidence

K-means clustering (k=2..6) on κ_t shape descriptors over Phase 6 SWE-bench Pro traces:

| Subset | Silhouette gap vs shuffled-time | n |
|---|---|---|
| Failures | **−0.014** (WORSE than shuffled) | ~50 |
| Successes | +0.020 (weak positive) | ~49 |

Both p > 0.10 after permutation test. The clustering structure that "looked obvious" by eye was indistinguishable from temporally-shuffled control.

## What it teaches

κ_t is an **aggregate statistic** that captures useful information about a population of agents (mean κ̄ comparisons across conditions are sound — see [κ_t paper](https://openinterp.org/research/papers/kappa-t-coherence-buildup)). But it is **not a per-trace fingerprint**: individual κ_t shapes do not cluster into mechanistic sub-types.

The lesson: aggregate-validity does not imply instance-validity. Always test whether a statistic discriminates at the unit of analysis you actually want to claim — population vs individual are different inferential regimes.

## Links
- Memory: `project_path_e_kappa_t_clustering_null_2026-05-19`
- κ_t paper (aggregate claim survives): https://openinterp.org/research/papers/kappa-t-coherence-buildup
- Failure analysis run: `~/Library/CloudStorage/.../openinterp_runs/path-e-clustering/`
