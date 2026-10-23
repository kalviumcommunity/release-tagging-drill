# Release Traceability and Recovery

## Purpose

This document describes how to map a release tag to its Git commit, inspect the associated changes, and identify a candidate rollback point.

The release tags created for this assignment are exercise milestones. They do not prove that the corresponding commits were deployed to production.

## 1. List Release Tags

List tags in descending version order:

```bash
git tag --sort=-v:refname
```

## 2. Inspect a Release

Inspect an annotated tag and its associated commit:

```bash
git show v1.0.0
```

Display the commit hash, date, and message:

```bash
git show -s --format=fuller v1.0.0
```

Resolve a tag to its commit hash:

```bash
git rev-parse v1.0.0^{commit}
```

Inspect the commit's file changes:

```bash
git diff v0.2.0..v1.0.0
```

## 3. Inspect an Earlier Release

Check out a release for inspection:

```bash
git checkout v0.1.0
```

This normally places Git in detached HEAD state. To return to the feature branch:

```bash
git switch feature/release-tagging
```

To create a separate branch from a release for investigation:

```bash
git switch -c investigate-v0.1.0 v0.1.0
```

## 4. Push Tags to the Remote Repository

Push all local tags:

```bash
git push origin --tags
```

Push a specific tag:

```bash
git push origin v1.0.0
```

## 5. Rollback Investigation

Before considering a rollback:

1. Identify the affected production deployment.
2. Verify its release tag and commit hash from deployment records.
3. Inspect the proposed rollback target and its changes.
4. Confirm that the target is supported and compatible with the environment.
5. Follow the team's approval and deployment procedures.
6. Record the rollback date, target version, commit hash, and reason.

A tag alone does not prove that a release was deployed or that it is a safe rollback target. The historical deployment records in this repository contain gaps, so they must not be treated as verified mappings for the new exercise tags.

## 6. Traceability Record

For each release, maintain the following information in `RELEASE-MAP.md`:

- Semantic version tag
- Git commit hash
- Commit date
- Summary of changes
- Verified deployment date and environment, when available

Keep release notes in `RELEASES.md`, and update both documents whenever a new release milestone is added.