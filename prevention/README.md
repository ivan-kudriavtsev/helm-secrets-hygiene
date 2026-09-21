# Prevention

Stops new plaintext from entering the repository.

Contents (landing incrementally):

- `chart-contract/` - the target-state chart: never emits a `kind: Secret`, only
  references an externally provisioned one.
- `anti-pattern-chart/` - a chart that templates a `kind: Secret` with inline
  data, used to demonstrate render-time exposure.
- `values.schema.json` - rejects raw credential fields, requires a secret
  reference, enforced by Helm before anything renders.
- `sops/` - SOPS + age setup for material that must live in git encrypted.
- `policy/` - conftest/OPA policy evaluated against rendered output.
- `.pre-commit-config.yaml` - Layer 1 (local, fast, bypassable).
- `ci/` - Layer 2 (CI, authoritative, non-bypassable): render, then enforce.
