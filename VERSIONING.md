# Versioning Convention

## Semantic versioning

Releases follow `MAJOR.MINOR.PATCH`:

- **MAJOR** increments for breaking changes that require consumers or
  deployments to adapt. Example: `v1.4.2 -> v2.0.0`.
- **MINOR** increments for backward-compatible functionality. Example:
  `v1.4.2 -> v1.5.0` for a new optional checkout capability.
- **PATCH** increments for backward-compatible bug fixes or small corrections.
  Example: `v1.4.2 -> v1.4.3` for a checkout timeout fix.

## Tag naming

Every production release uses the exact pattern `vMAJOR.MINOR.PATCH`, with a
lowercase `v` prefix and numeric components. For example, `v1.5.0`.

## Annotated release tags

Production releases use annotated tags because the tag object records a
message, tagger, and timestamp in addition to the target commit. Create one
with:

```sh
git tag -a v1.5.0 -m "Release 1.5.0: add optional checkout capability"
```

Lightweight tags are not used for releases because they do not carry that
release metadata.

## Pre-releases

Release candidates and betas append a dot-separated identifier, such as
`v1.5.0-beta.1` or `v1.5.0-rc.1`. Pre-releases sort before the corresponding
final release: `v1.5.0-beta.1 < v1.5.0-rc.1 < v1.5.0`. A pre-release must not be
used as the production rollback target until the final tag is published.
