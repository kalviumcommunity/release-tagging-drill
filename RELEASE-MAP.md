# Release Map

## Purpose

This document maps semantic-version tags to Git commits and summarizes the changes represented by each release milestone.

**Historical limitation:** The repository's original deployment records are incomplete. The release milestones below are established for this tagging exercise and must not be interpreted as proof that these versions were deployed to production on the dates shown.

## Release Mapping

| Tag | Commit (short hash) | Commit date | What shipped |
|---|---|---|---|
| `v0.1.0` | `ff1fb9a` | 2026-06-06 | Assignment repository scaffold and initial release-tagging exercise structure. |
| `v0.2.0` | `86989b6` | 2026-06-06 | Release-documentation milestone containing updates to the old release notes. |
| `v1.0.0` | `82535d9` | 2026-06-06 | Checkout-service placeholder implementation with service name, placeholder version output, and startup messages. |

## Verification

After creating the annotated tags, verify the mapping using:

```bash
git show --no-patch --format=fuller v0.1.0
git show --no-patch --format=fuller v0.2.0
git show --no-patch --format=fuller v1.0.0
```

Confirm the short commit hashes with:

```bash
git rev-parse --short v0.1.0^{commit}
git rev-parse --short v0.2.0^{commit}
git rev-parse --short v1.0.0^{commit}
```

The tag dates may differ from the commit dates because annotated tags have their own tagger timestamps.

## Deployment Status

These tags establish traceability within the exercise repository. The supplied historical deployment records do not prove that these specific commits were deployed to production.

Production deployment records should separately identify the verified release tag, commit hash, deployment date, and environment.