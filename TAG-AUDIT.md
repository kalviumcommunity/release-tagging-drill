# Tag Audit Report: Release History & Incident Analysis

## 1. Executive Summary

This audit assesses the historical tagging practices, release discrepancies, and operational incidents in the Checkout Service repository. Prior to this audit, releases were tracked inconsistently with disparate naming conventions, unannotated lightweight tags, mutable references, and undocumented production deployments. 

This lack of versioning rigor directly caused multiple high-severity production incidents, prolonged outages, and rendered automated rollback mechanisms ineffective. This document identifies the specific legacy tagging defects, analyzes the risks they introduced, and provides actionable remediation steps.

---

## 2. Inventory of Historical Tagging Defects

The table below summarizes the problematic tags identified in the repository history, deployment logs (`docs/deployment-history.md`), incident records (`docs/incident-log.md`), and historical release notes (`docs/release-notes-old.md`):

| Legacy Tag / Reference | Classification / Pattern Issue | Commit / Context | Immediate Risk to Team |
|---|---|---|---|
| `v2-final-FINAL` | Subjective, non-standard suffix | Release Notes | Unpredictable version ordering, ambiguous stability state |
| `release_2` | Non-semantic lightweight tag | Incident 1 (2026-04-22) | Points to wrong/unsupported commit; caused 502 outage rollback failure |
| `latest-good` | Mutable floating alias as tag | Incident 2 (2026-05-04) | Non-reproducible deployments; unknown codebase state |
| `stable-build` | Branch-like environment label | Deployment Log (2026-03-15) | Missing release metadata; unversioned artifact |
| `1.5.0` vs `v1.4.2` | Inconsistent prefix (`v` prefix missing) | Release Notes | Breaks automated SemVer regex filtering and version sorting |
| `version-1.0` | Non-standard verbose prefix & 2-part format | Release Notes | Breaks 3-part SemVer (`MAJOR.MINOR.PATCH`); fails tool parsing |
| `patch-new` | Relative, context-free adjective | Incident 3 (2026-03-31) | Becomes instantly obsolete; gives zero information on bug fixed |
| `Unknown version` | Undocumented production state | Incident 3 (2026-03-31) | Responders unable to identify running code during outage |

---

## 3. Detailed Audit of Specific Tagging Problems

### Problem 1: Ambiguous and Informal Suffixes (`v2-final-FINAL`)
* **Evidence:** Tag `v2-final-FINAL` in `docs/release-notes-old.md` (accompanied by note *"production-ready? maybe"*).
* **What it means for the team:** Developers used informal, subjective qualifiers ("final", "FINAL") to denote release readiness rather than standardized pre-release or stable release semver conventions.
* **Rollback & Traceability Risk:** 
  - Standard SemVer tooling and `git tag --sort=-v:refname` cannot logically sort or compare `v2-final-FINAL` against prior or future versions.
  - If a bug is discovered in `v2-final-FINAL`, engineers have no structured path forward (e.g., creating `v2-final-FINAL-2` or `v2-final-real-FINAL`).
  - During an emergency, operators cannot determine if `v2-final-FINAL` includes breaking changes or if it is backwards-compatible with `v1.x`.

### Problem 2: Unannotated Lightweight Tags Pointing to Unverified Commits (`release_2`)
* **Evidence:** Tag `release_2` used during the 2026-04-22 emergency rollback in `docs/incident-log.md` (Incident 1).
* **What it means for the team:** The tag was created as a lightweight Git reference (`git tag release_2`) without author metadata, creation timestamp, commit verification, or release documentation.
* **Rollback & Traceability Risk:** 
  - In Incident 1, after a payment gateway regression caused `502` errors in production, the team executed an emergency rollback to `release_2`.
  - Because `release_2` lacked semantic structure and deployment linkage, it pointed to an obsolete, unsupported commit lacking critical configuration. The rollback worsened the outage.
  - Lightweight tags cannot be verified with GPG signatures, creating compliance and security audit risks.

### Problem 3: Mutable Floating References Used as Release Tags (`latest-good` & `stable-build`)
* **Evidence:** Tags `latest-good` (deployed 2026-05-04) and `stable-build` (2026-03-15) documented in `docs/deployment-history.md` and `docs/incident-log.md` (Incident 2).
* **What it means for the team:** Teams treated release tags like mutable bookmarks or branch names, creating tags labeled `latest-good` or `stable-build` whenever a build passed testing.
* **Rollback & Traceability Risk:** 
  - Release tags must be immutable snapshots. Floating names like `latest-good` overwrite history if reused or moved (`git tag -f`).
  - Operations deployed `latest-good` to production on 2026-05-04, but no release notes or changelog existed for it. When issues arose, responders had no baseline against which to diff changes (`git diff <tag1>..<tag2>`).

### Problem 4: Inconsistent Version Formatting and Prefix Inconsistency (`1.5.0` vs `v1.4.2` vs `version-1.0`)
* **Evidence:** Coexistence of `1.5.0` (no prefix, 3-digit), `v1.4.2` (`v` prefix, 3-digit), and `version-1.0` (`version-` prefix, 2-digit) across `docs/release-notes-old.md`.
* **What it means for the team:** No repository-wide standard was enforced. Different engineers and automated scripts applied disparate conventions.
* **Rollback & Traceability Risk:** 
  - Automated deployment pipelines filtering for `refs/tags/v*` will silently skip `1.5.0` and `version-1.0`.
  - Git's version sort (`git tag --sort=-v:refname`) sorts `version-1.0` alphabetically against `v1.4.2`, producing an erratic ordering.
  - Rollback scripts that calculate predecessor versions by parsing `vX.Y.Z` crash or produce incorrect target versions.

### Problem 5: Generic, Relative Adjective Tagging (`patch-new`)
* **Evidence:** Tag `patch-new` cited in `docs/release-notes-old.md` and `docs/incident-log.md` (Incident 3).
* **What it means for the team:** A hotfix was tagged with a transient descriptor ("new") that became obsolete the moment the next commit occurred.
* **Rollback & Traceability Risk:** 
  - "New" conveys zero temporal, semantic, or functional information. Responders cannot tell which component was patched, what severity the bug was, or what base version the patch was branched from.
  - Traceability between Git history and customer-facing incident logs is completely severed.

### Problem 6: Undocumented Production State and Disconnected Deployment Logs
* **Evidence:** Deployment log entry `2026-03-31: Unknown version currently running in production` and Incident 3.
* **What it means for the team:** Production environments were deployed manually or through unversioned CI runs without recording the associated Git commit hash or tag.
* **Rollback & Traceability Risk:** 
  - When production failed on 2026-03-31, incident responders spent critical minutes trying to determine what code was currently running.
  - Without knowing the running version, selecting an appropriate rollback target was pure guesswork.

---

## 4. Root Cause Summary & Corrective Action Plan

| Core Failure Area | Legacy State | Required Target State |
|---|---|---|
| **Versioning Scheme** | Ad-hoc strings (`release_2`, `v2-final-FINAL`, `patch-new`) | Strict Semantic Versioning 2.0.0 (`vMAJOR.MINOR.PATCH`) |
| **Git Tag Type** | Unannotated lightweight tags | Annotated tags (`git tag -a -m`) with author, date, and description |
| **Pre-Release Workflow** | Arbitrary words (`final`, `stable-build`) | Standardized SemVer pre-releases (`vX.Y.Z-rc.N`, `vX.Y.Z-beta.N`) |
| **Deployment Mapping** | Missing or disconnected logs | Unified `RELEASE-MAP.md` linking tags, commit SHAs, dates, and changelogs |
| **Rollback Capability** | Guesswork leading to 502 errors | Deterministic rollback via `git tag --sort=-v:refname` and documented targets |
