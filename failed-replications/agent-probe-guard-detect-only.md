# agent-probe-guard probes — confirmed epiphenomenal (detect-only design)

**Original claim:** The two trained probes shipping in agent-probe-guard v0.1 (L55 thinking probe AUROC 0.855, L43 capability probe AUROC 0.863) detect AND lever behavior — useful both as monitors and as steering directions.
**Walked back:** 2026-05-08
**Failure mode:** `epiphenomenal_prior`
**Diagnostic that caught it:** α-sweep with structural-rigidity check + control-token normalization

## Evidence

From the official agent-probe-guard-qwen36-27b HuggingFace dataset card:

> *"Probes don't causally steer behavior per paper experiments — confirmed epiphenomenal."*

Both probes ship as **detect-only**. The SDK's `causality_protocol` returns verdicts of `correlative_only` for both. Steering modes are intentionally **not exposed** in the public API.

This is one of three convergent prior findings on Qwen3.6-27B that establish the epiphenomenal prior for linear/single-SAE probes on this model family:

1. **ICML paper-1 (FabricationGuard)**: linear probe beats single-SAE on AUROC, 0.89 vs 0.81 at L31
2. **qwen36-feature-circuits investigation**: 4-phase circuit study identified only 3 microcircuits, all task-format, ZERO reasoning circuits
3. **agent-probe-guard**: explicit detect-only design ships because steering didn't pass the protocol

## What it teaches

For Qwen3.6-27B specifically, **pre-register H-PROBE-EPIPHENOMENAL as the modal (~60–70%) outcome** of any new probe study, not as a remote possibility. Frame the paper expecting a null — a positive result is breakthrough-shaped and demands extra-careful baselining.

Don't generalize this prior to other models without evidence. The rule is **Qwen3.6-27B-specific** until tested on Gemma-3, Llama-3.3, Qwen-Next, etc.

## Links
- agent-probe-guard SDK: https://github.com/OpenInterpretability/cli
- HF dataset card: https://huggingface.co/datasets/caiovicentino1/agent-probe-guard-qwen36-27b
- Memory: `feedback_h_probe_epiphenomenal_prior_qwen36`
- Paper: https://openinterp.org/research/papers/two-forms-epiphenomenal-probes
