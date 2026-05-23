# Failed-Replication Registry

A public record of mechanistic interpretability claims that **didn't survive their own diagnostics**.

Each entry documents an original claim, the diagnostic that caught it, the evidence, and what it teaches the next iteration.

The six first entries are from the OpenInterp portfolio — claims we walked back when the Six Diagnostics protocol caught them.

> **Why this exists.** When the field publishes only positive results, the next paper repeats the same pitfalls. A registry of named failure modes — and worked examples of each — is the cheapest way to compound community learning.

## Failure-mode taxonomy

Each entry tags the failure mode it exemplifies. Six are currently recognized:

1. **`saturation_direction`** — probe direction levers in the direction of baseline residual saturation regardless of behavior class
2. **`env_coupling`** — probe trained in env A (e.g. flash attention) collapses in env B (e.g. sdpa) due to attention-impl-dependent residual
3. **`template_lock`** — probe AUROC is high because the decision is encoded in input tokens (chat template), not residual stream
4. **`marginal_fit`** — recall@k looks high because the probe learns the marginal of the target, not per-prompt predictive structure
5. **`epiphenomenal_prior`** — model-specific prior that linear/single-SAE probes are epiphenomenal (Qwen3.6-27B documented; sister models tbd)
6. **`capacity_overparameterization`** — probe at N=50, K=128 features hits AUROC 1.0 because K > N (shatters trivially)

## Submission

To submit a failed replication:
1. Fork this repo
2. Add `failed-replications/<slug>.md` following the template
3. Optionally attach a `causal_report.json` per [`schemas/causal_report.json`](../schemas/causal_report.json)
4. Open a PR

## Entry template

```markdown
# <Title>

**Original claim:** <one-line>
**Walked back:** YYYY-MM-DD
**Failure mode:** <tag from taxonomy>
**Diagnostic that caught it:** <which of Six Diagnostics>

## Evidence
<numbers, gaps, p-values>

## What it teaches
<one paragraph>

## Links
- Original artifact / paper
- Walk-back analysis
- Source code
```

## Current entries (6)

| Slug | Failure mode | Diagnostic |
|---|---|---|
| [path-e-kappa-t-clustering](path-e-kappa-t-clustering.md) | saturation_direction | shuffled-time baseline |
| [psae-v1-5-marginal-fit](psae-v1-5-marginal-fit.md) | marginal_fit | shuffled-source baseline |
| [phase-7-epiphenomenal-softmax](phase-7-epiphenomenal-softmax.md) | saturation_direction | control-token normalization |
| [phase-8-template-lock](phase-8-template-lock.md) | template_lock | structural-rigidity α-sweep |
| [agent-probe-guard-detect-only](agent-probe-guard-detect-only.md) | epiphenomenal_prior | α-sweep (no behavioral lever) |
| [nb46-ensemble-ood-walkback](nb46-ensemble-ood-walkback.md) | env_coupling (distributional) | cross-distribution eval |
