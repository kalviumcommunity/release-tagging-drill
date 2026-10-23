# Versioning Convention

## Semantic versioning

Production releases use `MAJOR.MINOR.PATCH`:

- **MAJOR** increases for incompatible API or behavior changes. Example:
  `v1.4.2 -> v2.0.0` for a breaking change.
- **MINOR** increases for backward-compatible functionality. Example:
  `v1.4.2 -> v1.5.0` for a new feature.
- **PATCH** increases for backward-compatible bug fixes. Example:
  `v1.4.2 -> v1.4.3` for a defect fix.

## Release tag format

The exact release tag pattern is `vMAJOR.MINOR.PATCH`, such as `v1.5.0`.
Tags are created on the commit that is deployed and are never moved to a
different commit.

## Annotated release tags

Releases use annotated tags rather than lightweight tags. An annotation records
the release message, author, and timestamp, which makes the deployment audit
and rollback decision inspectable:

```sh
git tag -a v1.5.0 -m "Release 1.5.0: add checkout audit logging"
```

## Pre-releases

Release candidates and betas use a hyphenated identifier after the base
version, for example `v1.5.0-rc.1` or `v1.5.0-beta.1`. Pre-release tags sort
before their final release, so `v1.5.0-rc.1` precedes `v1.5.0`; the final
`v1.5.0` is the production release after validation. Increment the numeric
suffix for later candidates (`rc.2`, then `rc.3`).
