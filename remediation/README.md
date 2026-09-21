# Remediation

Fixes plaintext that is already committed, at fleet scale. This half assumes the
multi-tenant reality: a shared chart across many tenant repositories, and
plaintext scattered through an unknown number of them. Hand-remediation does not
scale there - which is why each step exists.

Contents (landing incrementally):

- `inventory/` - full-history scan across repositories to enumerate the debt
  (repo, kind, when introduced, still live).
- `triage/` - classify findings by rotation cost: automatable vs manual.
- `rotate/` - automated rotation where an endpoint exists; a ticket generator
  that removes the friction where a human must act.
- `replace/` - move the rotated value into the external-secret reference.
- `scrub/` - OPTIONAL history cleanup, AFTER rotation only. Rotation closes the
  exposure; the scrub is hygiene, never the fix.
