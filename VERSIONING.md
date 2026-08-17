# Repository Versioning Policy & Tagging Standard

## 1. Overview and Purpose

This document establishes the official software versioning, Git tagging, and release management standard for the Checkout Service repository. Adherence to this standard ensures deterministic deployments, reproducible rollbacks, clear auditability, and automated tool compatibility across all environments.

---

## 2. Semantic Versioning Specification (SemVer 2.0.0)

All releases follow the **Semantic Versioning 2.0.0** specification (`MAJOR.MINOR.PATCH`). Version numbers convey specific architectural and operational meaning regarding backward compatibility:

$$\text{Format: } \mathbf{vMAJOR.MINOR.PATCH}$$

```
                       v1 . 4 . 2
                       ──   ─   ─
                        │   │   │
  MAJOR Version ────────┘   │   │  (Breaking API changes, backward-incompatible shifts)
  MINOR Version ────────────┘   │  (New backward-compatible functionality / features)
  PATCH Version ────────────────┘  (Backward-compatible bug fixes, security patches)
```

### 2.1 MAJOR Version Bumps
* **When to Bump:** When introducing breaking changes, backward-incompatible API changes, removing existing public interfaces, database schema migrations requiring manual intervention, or major architectural overhauls.
* **Compatibility:** Incompatible with prior versions; requires downstream consumer migration.
* **Concrete Example:** 
  $$\mathbf{v1.4.2 \longrightarrow v2.0.0}$$
  *Example context:* Dropping legacy SOAP/XML payment payload support in favor of strict JSON REST endpoints, or changing mandatory request headers.

### 2.2 MINOR Version Bumps
* **When to Bump:** When adding new functionality in a backward-compatible manner, deprecating existing features (without removing them), or introducing substantial non-breaking performance improvements.
* **Compatibility:** Fully backward-compatible with all `v1.x` releases with a lower minor number.
* **Concrete Example:** 
  $$\mathbf{v1.4.2 \longrightarrow v1.5.0}$$
  *Example context:* Adding Apple Pay and Google Wallet payment provider integrations alongside existing credit card processing without altering existing request/response schemas.

### 2.3 PATCH Version Bumps
* **When to Bump:** When applying backward-compatible bug fixes, emergency hotfixes, dependency vulnerability patches, or minor internal optimizations that do not alter the public interface.
* **Compatibility:** 100% backward-compatible; drop-in replacement for the preceding patch version.
* **Concrete Example:** 
  $$\mathbf{v1.4.2 \longrightarrow v1.4.3}$$
  *Example context:* Fixing a checkout submit button race condition or handling payment gateway connection timeout retries without changing endpoint signatures.

---

## 3. Tag Naming Format

All official release tags must strictly adhere to the following naming pattern:

$$\mathbf{vMAJOR.MINOR.PATCH}$$

### 3.1 Syntax Rules
1. **Prefix:** Must start with a lowercase `v` (e.g., `v1.0.0`).
2. **Components:** Three non-negative integers separated by dots (`.`): `MAJOR`, `MINOR`, `PATCH`. Leading zeros within an integer segment are not allowed (e.g., `v1.01.0` is invalid; use `v1.1.0`).
3. **Format Regex:** 
   ```regex
   ^v(0|[1-9]\d*)\.(0|[1-9]\d*)\.(0|[1-9]\d*)$
   ```
4. **Concrete Standard Example:**
   ```bash
   v1.2.0
   ```

### 3.2 Prohibited Tag Naming Patterns
The following patterns are strictly forbidden in this repository:
- **No informal suffixes:** `v2-final`, `v2-final-FINAL`, `v1.0-prod`
- **No environment or bookmark names:** `latest-good`, `stable-build`, `production`
- **No missing prefix or 2-part numbers:** `1.5.0`, `version-1.0`, `v1.2`
- **No relative descriptors:** `patch-new`, `release_2`, `hotfix_urgent`

---

## 4. Annotated vs. Lightweight Tags Policy

### 4.1 Strict Policy: Annotated Tags for All Releases
**All staging, production, and candidate releases MUST be created as Annotated Tags (`git tag -a`).**

Lightweight tags (created with `git tag <tagname>`) are merely mutable pointers to a commit hash. They are strictly prohibited for deployment releases.

### 4.2 Why Annotated Tags Are Mandatory
| Attribute | Annotated Tags (`git tag -a`) | Lightweight Tags (`git tag`) |
|---|---|---|
| **Git Object Type** | First-class `tag` object in Git database | Simple ref pointer in `.git/refs/tags/` |
| **Author / Tagger Info** | Captures Tagger Name and Email | ❌ None (only points to commit author) |
| **Timestamp** | Immutable tag creation date & time | ❌ None |
| **Release Message** | Required release summary and changelog metadata | ❌ No message allowed |
| **GPG Cryptographic Signing**| Fully supported (`git tag -s`) for compliance | ❌ Not supported |
| **Audit Trail** | Complete audit traceability for SOC2/ISO27001 | ❌ Unusable for compliance audits |

### 4.3 Standard Command for Release Tag Creation
To tag a release commit, execute:
```bash
git tag -a v1.1.0 -m "Release 1.1.0: Add payment token caching and timeout handling"
```

To verify tag metadata before deployment:
```bash
git show v1.1.0
```

---

## 5. Pre-Release Versioning & Ordering Rules

### 5.1 Pre-Release Format
Pre-releases (Alphas, Betas, Release Candidates) append a hyphen and a series of dot-separated identifiers following the patch number:

$$\mathbf{vMAJOR.MINOR.PATCH-[rc|beta|alpha].N}$$

- **Release Candidates (RC):** For staging verification and release testing prior to production promotion.
  *Example:* `v1.5.0-rc.1`, `v1.5.0-rc.2`
- **Beta Releases:** For feature-complete builds undergoing QA / internal testing.
  *Example:* `v2.0.0-beta.1`
- **Alpha Releases:** For early developmental milestones.
  *Example:* `v2.0.0-alpha.1`

### 5.2 Semantic Ordering Precedence
In accordance with SemVer 2.0.0, a pre-release version has **lower precedence** than the associated normal version:

$$\mathbf{v1.5.0-alpha.1 < v1.5.0-beta.1 < v1.5.0-rc.1 < v1.5.0-rc.2 < v1.5.0}$$

### 5.3 Git Sort Compatibility
Using Git's version sort option (`--sort=-v:refname`) automatically evaluates semantic version precedence:
```bash
$ git tag --sort=-v:refname
v1.5.0
v1.5.0-rc.2
v1.5.0-rc.1
v1.4.3
```

### 5.4 Release Promotion Workflow
1. A release candidate tag `v1.5.0-rc.1` is tagged on the candidate branch for staging deployment.
2. If QA passes without bug fixes, the exact same commit is tagged with the final production release tag `v1.5.0`.
3. If issues are found, fixes are committed and tagged as `v1.5.0-rc.2`.
4. Once verified, the final release `v1.5.0` is cut and pushed to production.
