# Versioning Convention

## Semantic versioning rules

The team will use semantic versioning in the form `vMAJOR.MINOR.PATCH`.

- MAJOR: increment when a release introduces a breaking change or removes prior compatibility. Example: `v1.4.2 -> v2.0.0`
- MINOR: increment when new backward-compatible features are added. Example: `v1.4.2 -> v1.5.0`
- PATCH: increment when backward-compatible bug fixes or small corrections are shipped. Example: `v1.4.2 -> v1.4.3`

## Tag naming format

All release tags will use the exact pattern `vMAJOR.MINOR.PATCH` with a lowercase `v` prefix. Example: `v1.2.3`

## Annotated vs lightweight tags

The team uses annotated tags for production releases because they store a message, author, and date alongside the tag object. That metadata makes the release trail auditable and easier to inspect in a support or rollback situation.

Use this command for a production release tag:

```bash
git tag -a v1.2.3 -m "Release 1.2.3: <summary>"
```

## Pre-release rule

Release candidates and betas will use the pattern `vMAJOR.MINOR.PATCH-rc.N` or `vMAJOR.MINOR.PATCH-beta.N`. Example: `v1.5.0-rc.1`.

Pre-releases always sort before the final release. In other words, `v1.5.0-rc.1` and `v1.5.0-beta.1` come before `v1.5.0`, and the final release closes the sequence.
