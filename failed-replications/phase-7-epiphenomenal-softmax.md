# Phase 7 — L43 pre_tool probe is epiphenomenal-softmax

**Original claim:** L43 pre_tool linear probe (AUROC 0.764, cross-repo LORO 0.958 at L23 turn_end) is a causal lever — steering on the probe direction at α=+5 boosts log-prob(finish_tool) by +0.479.
**Walked back:** 2026-05-07
**Failure mode:** `saturation_direction`
**Diagnostic that caught it:** control-token normalization

## Evidence

Triple-source convergent verdict:

| Source | Raw effect | After control-token norm |
|---|---|---|
| Log-prob proxy at α=+5 | Δ log-prob(finish) = +0.479 | **Δrel = −0.046** |
| Single-shot α=+5 behavioral | 4/4 fail trajectories — | All 4 select same tool |
| Continuous α=+5 | 3/4 keep same tool | 1 degenerates without redirecting to "finish" |

The +0.479 boost was **uniform softmax-temperature shift** — every token's log-prob shifted by a similar amount. The probe direction wasn't selecting "finish"; it was lowering the temperature.

## What it teaches

Always report **Δrel = Δ(target) − mean(Δ(controls))**, never raw Δ(target). Without control-token normalization, any direction with non-zero projection onto the saturation axis will look like a causal lever for whatever target you measure.

This is now a **hard rule** in the OpenInterp Six Diagnostics protocol — see [`intervention_trace.json` schema](../schemas/intervention_trace.json), `controls.control_token_normalization`.

The Phase 7 verdict — combined with [Phase 8 template-lock](phase-8-template-lock.md) — motivated the **agent-probe-guard SDK shipping as detect-only**, with no "boost mode."

## Links
- Paper: https://openinterp.org/research/papers/two-forms-epiphenomenal-probes
- Memory: `project_swebench_v7_phase7_causality_verdict`
- Methodology rule: `feedback_steering_control_token_normalization`
