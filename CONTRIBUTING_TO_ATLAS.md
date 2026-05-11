# Contributing to the Atlas

The Atlas is a public, read-only registry of mechanistic-interpretability findings.
Every entry is hashed (sha256, content-only) so claims are verifiable across forks,
mirrors, and time. This document is the authoritative reference for contributing.

## Two paths

### Path A — Recommended: via openinterp-mcp

If you ran your experiment with [openinterp-mcp](https://github.com/OpenInterpretability/openinterp-mcp)
on a Colab/vast.ai/runpod session, publishing is one Python call:

```python
from openinterp_mcp.publish import publish

publish(
  title="My finding in one line",
  type="probe-result",            # probe-result / atlas-entry / adversarial-finding / replication / sae-feature
  model_id="Qwen/Qwen3.6-27B-Instruct",
  claim="One-line plain-language summary readable by non-experts.",
  numbers={"auroc": 0.88, "n_samples": 200},
  methodology_check={"verdict": "weak-causal", "baselines_run": [...]},
  artifacts=["probe.joblib", "scaler.joblib"],
  hf_repo_id="myhandle/my-artifact",
)
```

This creates the HF dataset, mints a Zenodo DOI (if `ZENODO_TOKEN` is set), and
opens a PR against this repo. CI runs schema + sha validation. Auto-merges on green.

See [openinterp.org/start](https://openinterp.org/start) for the 10-minute setup.

### Path B — Manual PR

When the agent-callable path doesn't fit (existing artifact, external methodology,
non-Colab compute), write the manifest by hand:

1. **Open a proposal issue first**
   File the [Atlas entry proposal](https://github.com/OpenInterpretability/registry/issues/new?template=atlas_entry.yml)
   so maintainers can flag scope or duplication before you invest in the manifest.

2. **Write the manifest JSON**
   Required fields: `title, author, type, license, schema_version, created_at,
   manifest_sha256`. See [`schema.json`](./schema.json) for the full specification.
   Set `manifest_sha256: null` first.

3. **Compute the canonical sha256**
   Build with the same Python module the MCP pipeline uses so the hash is
   verifiable by reviewers:
   ```python
   from openinterp_mcp.publish.manifest import build_publication_manifest
   m = build_publication_manifest(
     title="...",
     author="...",
     type="probe-result",
     # ... all other fields
   )
   print(m.manifest_sha256)   # 64-char hex; slug = first 10 chars
   ```

4. **Open the PR**
   - Place the JSON at `atlas/<year>/<slug>.json`
   - Append a one-line entry to `index.json` (preserve the existing entries,
     bump `count`, set `last_updated`)
   - CI re-hashes the manifest and gates auto-merge

## What gets accepted

### Yes
- Probes with measurable AUROC + a reproducer
- Causal verdicts in any of the 5 classes (causal / weak-causal / epiphenomenal-softmax
  / epiphenomenal-template / undetermined)
- **Honest-negative findings** (walked-back claims, OOD failures) — these are
  first-class citizens, not second-tier
- Replications (positive or negative)
- SAE features with auto-interp labels + max-activating examples

### Needs review
- Small-N findings (N < 50) without random-feature baseline
- Steering claims without control-token normalization (paper-6 protocol)
- Cross-model claims without per-model Pearson_CE
- Single-shot evaluations without N>1 confidence intervals

### Not accepted
- Closed-weight artifacts (must be reproducible by someone re-running)
- Unhashed claims (`manifest_sha256` is required)
- Pure literature reviews with no novel measurement
- Marketing claims without numbers

## Schema overview

| Field | Required | Notes |
|---|---|---|
| `title` | ✓ | 1–200 chars |
| `author` | ✓ | GitHub handle preferred |
| `type` | ✓ | probe-result / atlas-entry / adversarial-finding / replication / sae-feature |
| `license` | ✓ | apache-2.0 / mit / cc-by-4.0 / cc0 |
| `model_id` | when type=probe-result | e.g. `Qwen/Qwen3.6-27B-Instruct` |
| `claim` | recommended | one-line plain-language summary |
| `numbers` | optional | free-form dict of metrics |
| `artifacts` | optional | filenames in the HF dataset |
| `methodology_check` | optional | output of causality_protocol |
| `reproduces` | when type=replication | paper slug being reproduced |
| `schema_version` | ✓ (= 1) | currently fixed at 1 |
| `created_at` | ✓ | ISO 8601 UTC |
| `manifest_sha256` | ✓ | 64-char hex |

See [`schema.json`](./schema.json) for the JSON Schema authoritative source.

## Reproducibility expectations

Atlas entries should be **reproducible from their methodology alone**. This means:

- Steering claims include the α-sweep used + which control tokens were normalized
- Probe results include the train/test split methodology + cross-task evaluation
- Causality verdicts include the 3 mandatory baselines (random-direction-random-acts,
  random-direction-real-acts, shuffled-labels)
- Small-N findings flag their N-adaptive threshold (paper-6 protocol)

When in doubt, over-report rather than under-report.

## Versioning the methodology itself

Schema v2 (in development) will add `protocol_id` + `protocol_sha256` so a future
reviewer can distinguish "v1 verdict from v1 protocol" vs "v1 verdict from v2
protocol." Today, all entries are implicitly v1.

## License

The registry as a whole is Apache-2.0. Individual entries declare their own license
via the `license` field (must be one of `apache-2.0 / mit / cc-by-4.0 / cc0`).

## Questions

- File a [discussion](https://github.com/OpenInterpretability/registry/discussions) in the registry repo
- Email `hi@openinterp.org`
- Or grab a "good-first-issue" label across the org and start there.
