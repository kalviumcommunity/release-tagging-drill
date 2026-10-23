# Tag History Audit (`TAG-AUDIT.md`)

## Executive Summary
An audit of the `release-tagging-drill` repository revealed a fragmented, non-standardized tag history that severely compromises deployment safety, automated CI/CD releases, audit compliance, and incident rollback capabilities. Prior to this audit, multiple developers utilized ad-hoc naming patterns, mixed annotated and lightweight tags, encoded subjective states into tag names, and created tags out of chronological order.

Below is the detailed audit of six specific tagging failures observed in the repository.

---

## 1. Subjective and Unparseable Tag Naming
- **Evidence (Exact Tag):** `v2-final-FINAL` (Commit `82535d9`)
- **Tag Type:** Lightweight Tag
- **What It Means for the Team:**
  - Developers resorted to informal, emotional naming ("final-FINAL") rather than adhering to formal release candidate progression (e.g., `v2.0.0-rc.1`).
  - Automated deployment tools, release scripts, and CI/CD pipelines expecting standard Semantic Versioning (SemVer) syntax (`vMAJOR.MINOR.PATCH`) fail to parse or extract numeric version segments from this string.
  - Furthermore, `v2-final-FINAL` points to the exact same commit (`82535d9`) as another ad-hoc tag `patch-new`, creating total ambiguity over which tag represents the authorized release artifact.
- **Rollback and Traceability Risk:**
  - **Traceability Risk:** "Final" communicates false certainty. Teams cannot determine whether subsequent emergency hotfixes would be called `v2-final-FINAL-2` or `v2-final-really-final`.
  - **Rollback Risk:** During an outage, automated rollback scripts that run `git describe` or sort by semantic version cannot determine what preceded `v2-final-FINAL`. Operators cannot safely roll back or forward because this tag has no predictable ordering in semver comparison algorithms.

---

## 2. Inverted Version Ordering and Semantic Conflict on the Same Commit
- **Evidence (Exact Tags):** `release_2` and `version-1.0` (Both pointing to Commit `28bd110`)
- **Tag Type:** `release_2` is Lightweight; `version-1.0` is Annotated
- **What It Means for the Team:**
  - Two contradictory version labels point to the identical Git commit (`28bd110`). One tag asserts the release is major version 2 (`release_2`), while the other asserts it is version 1.0 (`version-1.0`).
  - Both tags violate the standard prefix format (`release_` with underscore vs. `version-` with hyphen). `version-1.0` also omits the required patch digit (`.0`).
  - More critically, commit `28bd110` was created **after** commit `ff1fb9a` (which was tagged `v1.4.2`). The history chronologically regressed from 1.4.2 back down to 1.0.
- **Rollback and Traceability Risk:**
  - **Audit Risk:** As documented in `docs/incident-log.md` (Incident 1), production experienced 502 errors on 2026-04-22. Incident responders attempted an emergency rollback to `release_2` under the belief it was a stable Version 2 release.
  - **Rollback Risk:** Because `release_2` actually resolved to commit `28bd110` (a broken older state prior to modern API contracts), the rollback deployed unsupported legacy code, prolonging downtime. It proved that without semantic consistency, rolling back becomes dangerous guesswork.

---

## 3. Mutable / Floating State Encoded into a Static Tag
- **Evidence (Exact Tag):** `latest-good` (Commit `da6b2ed`)
- **Tag Type:** Annotated Tag ("Annotated rollback candidate, but points to an older commit")
- **What It Means for the Team:**
  - Developers treated Git tags as dynamic branch pointers (like `main` or `staging`) rather than immutable point-in-time release releases.
  - What is considered "latest good" changes with every successful build, but a Git tag is intended to be permanently anchored to a single release milestone.
  - As documented in `docs/deployment-history.md` and `docs/incident-log.md` (Incident 2), on 2026-05-04 a production hotfix was deployed from `latest-good` with no associated semantic version, ticket reference, or changelog record.
- **Rollback and Traceability Risk:**
  - **Traceability Risk:** Permanent loss of audit history. If someone updates or retags `latest-good` tomorrow, all past deployment records referencing `latest-good` become untraceable and unverifiable.
  - **Rollback Risk:** Responders seeking a safe rollback target cannot know what features, dependencies, or bugfixes were present in `latest-good` without doing a manual file diff.

---

## 4. Inconsistent Tag Prefixing and Broken Refname Sorting
- **Evidence (Exact Tags):** `1.5.0` (Commit `86989b6`) vs. `v1.4.2` (Commit `ff1fb9a`)
- **Tag Type:** `1.5.0` is Annotated; `v1.4.2` is Lightweight
- **What It Means for the Team:**
  - Tag prefixing was not standardized: `1.5.0` has no prefix, while `v1.4.2` uses a lowercase `v`.
  - When querying the repository release order using Git's built-in version sorting tool:
    ```bash
    git tag --sort=-v:refname
    ```
    `1.5.0` is sorted to the very bottom of the tag list below `v1.4.2` and non-semver strings because Git lexicographically groups prefixed and non-prefixed strings differently.
- **Rollback and Traceability Risk:**
  - **Traceability Risk:** Automated build systems (such as GitHub Actions or Kubernetes deployment controllers) that determine the latest release via `git tag --sort=-v:refname | head -n 1` will fail to recognize `1.5.0` as newer than `v1.4.2`.
  - **Rollback Risk:** If automated rollback logic attempts to identify "previous tag before current", it will select the wrong commit or skip releases entirely, deploying incorrect code into production.

---

## 5. Non-SemVer Environment/Status Tags and Redundant Duplicate Targets
- **Evidence (Exact Tags):** `stable-build` and `v1.4.2` (Both pointing to Commit `ff1fb9a`)
- **Tag Type:** `stable-build` is Annotated ("Annotated but misleading stable build tag"); `v1.4.2` is Lightweight
- **What It Means for the Team:**
  - The team conflated release versioning with environment deployment statuses. Release tags should identify code state (`v1.4.2`), whereas "stability" is a transient status tracked in release management dashboards or environment manifests.
  - Having two tags pointing to the same commit causes confusion over which identifier should be referenced in tickets, Jira issues, and pull requests.
- **Rollback and Traceability Risk:**
  - **Audit Risk:** As recorded in `docs/deployment-history.md`: *"Missing deployment record for the release tagged stable-build"* and *"A staging promotion was assumed to match v1.4.2, but the production tag is undocumented."*
  - **Rollback Risk:** Engineers rolling back cannot determine whether `stable-build` includes hotfixes added after `v1.4.2` or if they are identical. Furthermore, `v1.4.2` is a lightweight tag containing no author, email, or timestamp metadata, leaving zero cryptographic or verifiable paper trail.

---

## 6. Meaningless Ephemeral Tagging Lacking Release Context
- **Evidence (Exact Tag):** `patch-new` (Commit `82535d9`)
- **Tag Type:** Lightweight Tag
- **What It Means for the Team:**
  - The tag provides no version number, no scope (patch of what?), and no metadata.
  - It exists simultaneously with `v2-final-FINAL` on the exact same commit, confusing developers and release managers.
- **Rollback and Traceability Risk:**
  - **Rollback Risk:** If `patch-new` introduces a critical defect, there is no corresponding `patch-old` or previous semantic baseline to identify what broke or where to return.
  - **Audit Risk:** Auditors cannot verify whether `patch-new` was an emergency bug fix, an approved security patch, or an experimental test branch pushed to the remote repository.

---

## Summary of Audit Findings

| Problem Area | Offending Tags | Root Cause | Severity |
| :--- | :--- | :--- | :--- |
| **Informal Naming** | `v2-final-FINAL` | Subjective labeling without SemVer compliance | High |
| **Conflicting & Inverted Tags** | `release_2`, `version-1.0` | Chronological regression and duplicate contradictory tags | Critical |
| **Floating State Tags** | `latest-good` | Using static tags for mutable deployment state | Critical |
| **Inconsistent Prefixing** | `1.5.0` vs `v1.4.2` | Missing tag naming convention breaking sort order | High |
| **Quality/Status Naming** | `stable-build` | Conflating deployment status with immutable release | Medium |
| **Unversioned Ad-Hoc Tag** | `patch-new` | Lightweight tag with no version or metadata | Medium |
