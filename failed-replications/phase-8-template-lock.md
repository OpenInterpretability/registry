# Phase 8 — L55 CoT-Integrity probe is template-locked epiphenomenal

**Original claim:** L55 CoT-Integrity linear probe (AUROC 0.91 at N=240, stronger than Phase 7's L43 0.83) detects whether the agent will emit `<think>` content and can be steered to flip thinking on/off.
**Walked back:** 2026-05-07
**Failure mode:** `template_lock`
**Diagnostic that caught it:** structural-rigidity α-sweep (α up to 200 × residual norm)

## Evidence

Bidirectional α-sweep on the L55 probe direction AND a random direction, both with control-token normalization:

| α | L55 probe flip rate | Random direction flip rate |
|---|---|---|
| ±2, ±5 | 0% | 0% |
| ±50 | 0% | 0% |
| ±100 | 0% | 0% |
| **±200 (> ‖residual‖)** | **0%** | **0%** |

Zero behavioral change for probe AND random direction at amplitudes that exceed the residual norm itself.

## Root cause

`enable_thinking=False` chat template injects `<think></think>` token pair into the input **before generation begins**. The "thinking decision" is encoded in input tokens, not in the residual stream. No residual intervention can flip it because there is nothing to flip — the decision was already made by the template before the model ever ran.

## What it teaches

When forward-hook steering at α∈{±2,±5} produces zero change, **sweep α to multiples of residual norm BEFORE declaring null**. This distinguishes amplitude-null (try bigger α) from structural-null (decision is in input tokens — no α will work).

If random direction also produces zero change at α=200, you have a **structural lock**, not an amplitude problem. < 60 seconds of compute to disambiguate.

## Links
- Paper: https://openinterp.org/research/papers/two-forms-epiphenomenal-probes
- Memory: `project_swebench_v8_phase8_template_lock_verdict`
- Methodology rule: `feedback_steering_structural_rigidity_diagnostic`
