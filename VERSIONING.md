# Versioning Policy

## 1. Purpose

This policy standardizes release versioning and tagging for the Checkout service. It aims to make releases identifiable, traceable to source commits, and safer to deploy or roll back.

## 2. Semantic Versioning

Production releases follow the format:

`vMAJOR.MINOR.PATCH`

- **MAJOR:** Increment when a release introduces backward-incompatible changes.
- **MINOR:** Increment when a release adds backward-compatible functionality.
- **PATCH:** Increment when a release contains backward-compatible bug fixes.

Examples:

| Version | Example change | Reason |
|---|---|---|
| `v1.0.0` | First stable production release | Initial stable release |
| `v1.1.0` | Add a backward-compatible checkout feature | MINOR increment |
| `v1.1.1` | Fix a checkout bug without breaking existing behavior | PATCH increment |
| `v2.0.0` | Introduce an incompatible API change | MAJOR increment |

These examples explain the versioning policy; they do not claim that these historical releases already exist in this repository.

## 3. Tag Naming

Production release tags must use the `vMAJOR.MINOR.PATCH` format.

Examples:

- `v1.0.0`
- `v1.1.0`
- `v1.1.1`

Avoid informal or ambiguous release names such as `latest-good`, `stable-build`, `patch-new`, and `v2-final-FINAL`.

## 4. Annotated Tags

Production releases must use annotated Git tags.

Annotated tags include a tagger, date, and message, making them more suitable for release records than lightweight tags.

Create an annotated tag with:

```bash
git tag -a v1.0.0 -m "Release v1.0.0: initial release"
```

Replace the version and message with the details of the release being tagged. Before creating a tag, verify that `HEAD` or the specified commit represents the intended release.

Inspect a tag with:

```bash
git show v1.0.0
```

## 5. Pre-release Versions

Pre-release versions identify builds that are not yet final production releases.

Examples:

- `v1.5.0-alpha.1` — early development build
- `v1.5.0-beta.1` — feature testing build
- `v1.5.0-rc.1` — release candidate

A release candidate should be validated before the corresponding final release is published.

For example, after validating `v1.5.0-rc.1`, the team may publish `v1.5.0` if the release is approved.

Pre-release tags must not be treated as equivalent to the final production release.

## 6. Release Traceability

Each production release must have:

1. An annotated semantic-version tag.
2. A corresponding Git commit.
3. A release date.
4. Structured release notes describing what changed.
5. A documented deployment or rollback reference where applicable.

Maintain these records in `RELEASE-MAP.md` and `RELEASES.md`.

Do not claim a release was deployed to production unless the deployment record supports that claim.

## 7. Verification Commands

List tags in descending version order:

```bash
git tag --sort=-v:refname
```

Inspect the commit associated with a tag:

```bash
git show --no-patch --format=fuller v1.0.0
```

Inspect the annotated tag object and its message:

```bash
git cat-file -p v1.0.0
```

Check out a release for inspection:

```bash
git checkout v1.1.0
```

Checking out a tag directly normally puts Git into detached HEAD state. To make changes, create a branch from the tag:

```bash
git switch -c inspect-v1.1.0 v1.1.0
```

## 8. Release Approval

Before publishing a production release:

1. Confirm the intended commit.
2. Verify the version increment.
3. Create an annotated tag.
4. Write the release notes.
5. Update the release map.
6. Verify that the tag resolves to the intended commit.
7. Push the tag to the remote repository.

This process keeps release naming consistent and helps the team identify the exact source revision associated with a release.