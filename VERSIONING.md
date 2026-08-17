# Versioning Policy & Tagging Conventions

## Overview

This document establishes the official release tagging and versioning policy for the `checkout-service` repository. All engineering team members, release managers, and automated CI/CD pipelines must adhere strictly to these conventions to guarantee release auditability, predictable deployments, and instant, reproducible rollbacks.

---

## 1. Semantic Versioning (SemVer) Rules

The repository strictly follows **Semantic Versioning 2.0.0** (`MAJOR.MINOR.PATCH`). Each position in the version number communicates the risk and nature of the release:

```
v MAJOR . MINOR . PATCH
  │       │       └── Backward-compatible bug fixes
  │       └────────── Backward-compatible new features
  └────────────────── Breaking / incompatible changes
```

### Position Definitions & Concrete Bump Examples

| Position | When to Bump | Concrete Example Bump | Description |
| :--- | :--- | :--- | :--- |
| **MAJOR** | Breaking, incompatible API, database schema, or architectural changes | `v1.4.2` → `v2.0.0` | Upgrading payment gateway API payload structure, breaking legacy API consumers. Resets MINOR and PATCH to 0. |
| **MINOR** | New backward-compatible feature added | `v1.4.2` → `v1.5.0` | Adding an optional payment token caching module or export endpoint. Resets PATCH to 0. |
| **PATCH** | Backward-compatible bug fix or hotfix | `v1.4.2` → `v1.4.3` | Resolving a checkout button timeout issue or fixing audit logging output without altering public interfaces. |

### Fundamental Rules
1. **Resetting Lower Positions**: Bumping a higher position always resets lower positions to zero (`v1.4.2` after a MINOR bump becomes `v1.5.0`, NOT `v1.5.2`).
2. **Immutability**: Once a version tag is published and deployed, its target commit must NEVER be changed or retagged.
3. **No Skipping Versions**: Version numbers must advance sequentially without skipping increments.

---

## 2. Tag Naming Format

All release tags MUST follow the exact pattern:

$$\text{Format: } \mathbf{v\text{MAJOR}.\text{MINOR}.\text{PATCH}}$$

- **Prefix**: Lowercase `v` prefix is **mandatory** for every release tag (e.g., `v1.5.0`, `v2.0.0`).
- **Forbidden Patterns**: `version-1.0`, `release_2`, `1.5.0` (missing `v`), `latest-good`, `v2-final-FINAL`, `stable-build`, `patch-new`.

### Standard Example
- **Correct**: `v1.5.0`
- **Incorrect**: `1.5.0`, `v1.5`, `version-1.5.0`, `v1.5.0-final`

*Rationale*: Consistency in the `v` prefix is essential so that Git's version-sort algorithms (`git tag --sort=-v:refname`) and container registry tags operate deterministically.

---

## 3. Tag Type Policy: Annotated vs Lightweight

### Policy Standard
**ALL production and staging releases MUST use ANNOTATED Git tags (`git tag -a`).**

Lightweight tags are strictly prohibited for release markers.

### Why Annotated Tags are Mandatory for Releases

| Feature | Annotated Tag (`git tag -a`) | Lightweight Tag (`git tag`) |
| :--- | :--- | :--- |
| **Git Object Type** | Full Git object with unique SHA | Bare pointer to commit |
| **Audit Metadata** | Stores author name, email, & timestamp | None (inherits commit date/author) |
| **Release Message** | Required message explaining changes | None |
| **Verification & Signing** | Can be GPG signed for security compliance | Cannot be signed |

### Exact Git Commands

To create an annotated release tag:
```bash
git tag -a v1.5.0 -m "Release 1.5.0: add payment token caching and timeout fixes"
```

To push tags to the remote repository:
```bash
git push origin v1.5.0
# Or push all tags:
git push origin --tags
```

---

## 4. Pre-Release Tagging Policy

Before a final production release, release candidates and beta builds must be tagged using SemVer pre-release identifiers.

### Pre-Release Format
$$\mathbf{v\text{MAJOR}.\text{MINOR}.\text{PATCH}-\text{type}.\text{N}}$$

- **Release Candidate (RC)**: `v1.5.0-rc.1`, `v1.5.0-rc.2`
- **Beta Build**: `v2.0.0-beta.1`

### Relative Ordering & SemVer Precedence
According to SemVer rules and Git's version-sort engine:
$$\text{v1.5.0-rc.1} < \text{v1.5.0-rc.2} < \text{v1.5.0}$$

When listing tags in descending order using `git tag --sort=-v:refname`:
1. `v1.5.0` (Final Release)
2. `v1.5.0-rc.2` (Release Candidate 2)
3. `v1.5.0-rc.1` (Release Candidate 1)

This guarantees that automated deployment pipelines always favor final releases over pre-release candidates while preserving full pre-release auditability.
