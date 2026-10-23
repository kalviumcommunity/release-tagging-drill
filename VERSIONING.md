# Semantic Versioning & Release Tagging Policy (`VERSIONING.md`)

## 1. Overview and Purpose
This document establishes the official software versioning and release tagging standard for the Checkout Service repository. The goal of this policy is to eliminate deployment ambiguity, guarantee automated sortability, maintain strict audit compliance, and provide deterministic rollback targets during production outages.

All releases must adhere strictly to [Semantic Versioning 2.0.0 (SemVer)](https://semver.org/) and use Git annotated tags.

---

## 2. Semantic Versioning Rules (MAJOR.MINOR.PATCH)
Versions are represented as three numeric segments separated by dots:
```
MAJOR.MINOR.PATCH
```

Each segment conveys specific guarantees regarding backward compatibility:

### 2.1 MAJOR Version
- **Definition:** Incremented when incompatible API changes, breaking architectural shifts, schema-breaking database migrations, or breaking protocol alterations are introduced.
- **Backward Compatibility:** NOT guaranteed. Downstream consumers, integrations, or services must update their code or configuration.
- **Reset Rule:** When MAJOR is incremented, MINOR and PATCH reset to `0`.
- **Concrete Example Bump:**
  - Previous Version: `v1.4.2`
  - Change: Removal of legacy `/api/v1/checkout` endpoint in favor of GraphQL checkout API.
  - New Version: `v2.0.0`

### 2.2 MINOR Version
- **Definition:** Incremented when new functionality, endpoints, or features are added in a backward-compatible manner, or when internal components are deprecated without removal.
- **Backward Compatibility:** Guaranteed. Existing integrations continue functioning without modification.
- **Reset Rule:** When MINOR is incremented, PATCH resets to `0`.
- **Concrete Example Bump:**
  - Previous Version: `v1.4.2`
  - Change: Added Apple Pay support and optional promotional code field to existing checkout payload.
  - New Version: `v1.5.0`

### 2.3 PATCH Version
- **Definition:** Incremented when backward-compatible bug fixes, security patches, performance improvements, or non-breaking configuration updates are introduced.
- **Backward Compatibility:** Guaranteed. No API contracts or behaviors are altered except for resolving unintended defects.
- **Concrete Example Bump:**
  - Previous Version: `v1.4.2`
  - Change: Fixed an unhandled timeout exception when connecting to payment gateway provider.
  - New Version: `v1.4.3`

---

## 3. Tag Naming Format
All release tags must adhere to a strict, uniform naming convention:

### 3.1 Pattern Specification
```
v<MAJOR>.<MINOR>.<PATCH>
```
- **Lowercase `v` prefix:** Mandatory. Standardizes refname sorting across Git, GitHub Releases, and Docker tags.
- **Digits only:** Each segment must be a non-negative integer without leading zeros (e.g., `0`, `1`, `12`).
- **No informal suffixes:** Phrases like `-final`, `-FINAL`, `_release`, `-patch`, or `-latest` are strictly prohibited on release tags.

### 3.2 Concrete Examples
- **Valid:** `v1.0.0`, `v1.1.0`, `v1.1.1`, `v2.0.0`
- **Invalid:** `1.0.0` (missing `v`), `v1.0` (missing patch), `release_2` (wrong format), `v2-final-FINAL` (informal suffix)

---

## 4. Annotated vs. Lightweight Tags Policy

### 4.1 Policy Mandate
> **All production releases and staging release candidates MUST use Annotated Tags.**  
> Lightweight tags are strictly prohibited for production deployments.

### 4.2 Technical Rationale
| Attribute | Lightweight Tags | Annotated Tags |
| :--- | :--- | :--- |
| **Storage Object** | Pointer / Bookmark to a commit hash | Full independent Git object (`tag` object type) |
| **Creator Metadata** | None (adopts commit author/date) | Preserves tagger's name, email, and timestamp |
| **Release Message** | None supported | Supports full changelog and release notes summary |
| **Audit Compliance** | Non-compliant (cannot verify who tagged) | Fully compliant with traceability and security audits |
| **Signing Support** | Cannot be cryptographically signed | Supports GPG/SSH cryptographic signing (`git tag -s`) |

Annotated tags provide an unalterable audit trail answering: **Who** released the artifact, **When** it was tagged, and **Why** (the release notes).

### 4.3 Exact Tag Creation Command
To create an annotated release tag:
```bash
git tag -a v1.1.0 -m "Release 1.1.0: Add structured changelog, historical documentation, and checkout button fixes"
```

To push tags to the remote origin:
```bash
git push origin <tag-name>      # Push a specific tag
git push origin --tags          # Push all local tags
```

To verify tag details and author metadata:
```bash
git show v1.1.0
```

---

## 5. Pre-Release Versioning Rules

### 5.1 Format Specification
Pre-release versions (such as Alpha, Beta, or Release Candidates) append a hyphen followed by a dot-separated identifier and sequence counter:
```
v<MAJOR>.<MINOR>.<PATCH>-<stage>.<sequence>
```
Allowed stages:
1. `alpha` (e.g., `v1.5.0-alpha.1`): Early internal test build.
2. `beta` (e.g., `v1.5.0-beta.1`): Feature-complete build open for integration testing.
3. `rc` (e.g., `v1.5.0-rc.1`): Release candidate ready for final QA and staging sign-off.

### 5.2 Precedence and Sort Order Relative to Final Release
Under SemVer 2.0.0 specification and Git's `version:refname` sorting logic, a pre-release version has **lower precedence** than the final release:
```
v1.5.0-alpha.1 < v1.5.0-beta.1 < v1.5.0-rc.1 < v1.5.0-rc.2 < v1.5.0
```

When sorting tags in descending order:
```bash
git tag --sort=-v:refname
```
Output:
```text
v1.5.0
v1.5.0-rc.2
v1.5.0-rc.1
v1.5.0-beta.1
```
This guarantees that automated CI/CD deployment tools picking the topmost tag will always select the final production release (`v1.5.0`) over any pre-release candidate (`-rc.1`).
