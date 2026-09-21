# helm-secret-hygiene

A prevention-first method for keeping plaintext secrets out of multi-tenant Helm
delivery - and for remediating the ones already committed across a fleet of
repositories.

This repository is the reference implementation for the method described in the
accompanying article. It is built in the open; see **Status** below for what is
landed and what is in progress.

---

## The two facts this is built on

**A plaintext secret on a shared remote is compromised the moment it is pushed.**
It exists in every clone, fork, mirror, backup, and CI log that has read the
commit. Encrypting the file afterward hides the leak; it does not undo it.
Rewriting history is unreliable. The only correct response to a leaked credential
is to rotate it.

**Content scanners cannot see plaintext that only exists after a chart renders.**
Tools that scan files as they sit in the repository miss a `kind: Secret` whose
data is templated from innocent-looking values, a credential in a subchart
default, or a value assembled by template logic. That exposure is produced by
`helm template`, not stored in any source file. You must evaluate what the cluster
will receive, not only what sits in the source tree.

## Governing principles

1. **Prevention over remediation.** The only safe secret is one that was never
   committed in plaintext. Remediation belongs at authoring time, never in the
   pipeline after a leak.
2. **Inspect what the cluster receives.** The primary scan runs against
   `helm template` output. Source-file scanning is a secondary pass.
3. **Encrypted is the intended state; flag only plaintext.** SOPS-state awareness
   is a classification step inside every scan, not a separate stage.
4. **Two layers, two guarantees.** A local pre-commit hook (fast, bypassable) and
   a non-bypassable CI gate (authoritative). You need both.
5. **Fail and rotate, never encrypt in the pipeline.** If CI finds plaintext, it
   was pushed and is compromised; the gate fails loudly and instructs rotation.
6. **Build on established primitives.** gitleaks, SOPS + age, conftest/OPA, the
   pre-commit framework, GitHub Actions. The contribution is the selection,
   sequencing, and enforcement pattern for a multi-tenant brownfield - not a new
   detector.

Guiding sentence: **make complying cheaper than cheating.**

## What this is, and is not

**It is** a documented method plus a runnable reference implementation for the
credential lifecycle in Helm-based delivery.

**It is not** a general-purpose secret manager, a history-cleaning tool, or a
replacement for gitleaks or SOPS. It orchestrates those tools around a rule they
do not enforce on their own.

## Repository layout

```
prevention/    stop new plaintext: contract-pattern chart, values schema,
               SOPS setup, the two gates, and the render-time demonstration
remediation/   fix existing plaintext at fleet scale: inventory, triage,
               rotate, replace, and (post-rotation only) history scrub
examples/      one-command demonstrations
docs/          diagrams: two-gate topology, secret lifecycle, remediation flow
```

## Status

This repository is under active development.

- [ ] Contract-pattern chart and anti-pattern chart
- [ ] `values.schema.json` enforcement
- [ ] Rendered-output policy (conftest/OPA)
- [ ] Pre-commit and CI wiring
- [ ] Render-time demonstration (content scanner passes, rendered-output gate catches)
- [ ] Remediation tooling: inventory, triage, rotation example, ticket generation
- [ ] Diagrams

## License

Apache-2.0. See [LICENSE](LICENSE) and [NOTICE](NOTICE).
