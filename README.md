# OpenInterp Registry

Public index of mechanistic-interpretability publications submitted via [openinterp-mcp](https://github.com/OpenInterpretability/openinterp-mcp).

Each entry references a HuggingFace dataset (artifacts), a Zenodo DOI (citation), and a methodology-check verdict from the [Causality Protocol](https://openinterp.org/research/papers/two-forms-epiphenomenal-probes) (paper-6).

## Layout

```
registry/
├── index.json                    ← machine-readable index (this is what openinterp.org reads)
├── schema.json                   ← JSON schema for entries
├── atlas/<year>/<sha>.json       ← general atlas entries (probe results, observations)
├── replications/<paper>/<sha>.json ← replications of published papers
├── probebench/<sha>.json         ← submissions to ProbeBench leaderboard
└── contributors/<handle>.json    ← auto-generated per-contributor index
```

## How entries land

1. Researcher runs `openinterp-mcp` against their Colab session, gets a result
2. Researcher invokes the `/publish` skill (or calls `openinterp_mcp.publish.publish()`)
3. The publish pipeline:
   - Creates a HF dataset under `openinterp-community/atlas-<slug>`
   - Optionally creates a Zenodo deposit (free DOI)
   - Opens a PR against this repo with a new entry under `atlas/<year>/<sha>.json`
4. CI (when wired up) runs the Causality Protocol on the submitted artifacts via Claude-Code-as-judge
5. If methodology check passes (verdict ∈ {causal, weak-causal, epiphenomenal-* (honest)}), PR auto-merges

## License

Apache-2.0. Entries individually carry their own license declarations (apache-2.0 / mit / cc-by-4.0 / cc0).

## Adding a manual seed entry

For the bootstrap phase (before the publish pipeline is fully wired), entries can be added by hand:

1. Fork this repo
2. Add a JSON file under the appropriate path matching `schema.json`
3. Run `python -m openinterp_mcp.publish.manifest --validate path/to/entry.json` locally
4. Append a one-line summary to `index.json`
5. Open a PR

## Stats

See `index.json` for the live list.

---
Part of the [OpenInterpretability](https://openinterp.org) ecosystem.
