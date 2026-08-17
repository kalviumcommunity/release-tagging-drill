# Tag Audit Report: Checkout Service

## Executive Summary

An audit of the git tagging history in the `release-tagging-drill` repository revealed severe inconsistencies, non-standard naming conventions, widespread use of lightweight (unannotated) tags, and subjective floating tag names. These defects directly contributed to production outages, misidentified rollback targets, and undocumented deployments (as recorded in `docs/incident-log.md` and `docs/deployment-history.md`).

Below are the detailed audit findings documenting seven specific tagging problems discovered in the repository history.

---

## Audit Findings

### Problem 1: Unsortable, Ambiguous Tag Naming (`v2-final-FINAL`)
- **Evidence Tag**: `v2-final-FINAL` (Commit `82535d9`)
- **Tag Type**: Lightweight tag (`commit`)
- **Team Impact**: Developers and operations staff cannot determine what changes are included, whether this version is backwards compatible, or whether subsequent hotfixes exist (e.g., what happens when `v2-final-FINAL-2` is created?). Standard git sorting commands like `git tag --sort=-v:refname` fail to parse `v2-final-FINAL` semantically.
- **Rollback & Traceability Risk**: In an emergency outage, responders cannot ascertain what code is running in production or whether it is safe to roll back to. The word "FINAL" creates false certainty while masking the lack of semantic version structure.

### Problem 2: Inconsistent Prefix and Truncated Version Number (`version-1.0`)
- **Evidence Tag**: `version-1.0` (Commit `28bd110`)
- **Tag Type**: Annotated tag (`tag`), Tagger message: *"Annotated historical release tag for version 1.0"*
- **Team Impact**: The `version-` prefix breaks repository-wide consistency (where other tags use `v` or no prefix). Omitting the patch number (`1.0` instead of `1.0.0`) violates Semantic Versioning 2.0.0 standards and breaks automated CI/CD release scripts and sorting algorithms expecting `vX.Y.Z`.
- **Rollback & Traceability Risk**: Automated release tooling attempting to parse semver tags fails or misinterprets the version sequence. Incident responders searching for patch-level releases cannot determine if `version-1.0` includes bug fixes or represents a bare baseline.

### Problem 3: Non-Semantic Arbitrary Label & Lightweight Tag (`release_2`)
- **Evidence Tag**: `release_2` (Commit `28bd110`)
- **Tag Type**: Lightweight tag (`commit`)
- **Team Impact**: `release_2` points to the exact same commit (`28bd110`) as `version-1.0`, creating duplicate ambiguous references for a single commit. As a lightweight tag, it stores no tagger identity, creation timestamp, or release message.
- **Rollback & Traceability Risk**: High Risk. As recorded in **Incident 1 (2026-04-22)**, when production returned 502 errors, the operations team attempted an emergency rollback to `release_2`. Because `release_2` was an unannotated lightweight tag with no deployment record linking it to a verified good build, the rollback deployed an unsupported commit, prolonging the outage.

### Problem 4: Missing Version Prefix (`1.5.0`)
- **Evidence Tag**: `1.5.0` (Commit `86989b6`)
- **Tag Type**: Annotated tag (`tag`), Tagger message: *"Annotated legacy release tag for version 1.5.0"*
- **Team Impact**: While `1.5.0` uses semver numbers, it lacks the standard `v` prefix (`1.5.0` vs `v1.4.2`). Mixing prefixed and non-prefixed tags in the same repository breaks lexical and version-ref sorting in `git tag --sort=-v:refname` and docker container image tagging pipelines.
- **Rollback & Traceability Risk**: Tooling sorting tags alphabetically or via standard regex filters will sort `1.5.0` incorrectly relative to `v1.4.2` and `v2.0.0`, potentially leading automated rollback pipelines to select an incorrect release artifact.

### Problem 5: Subjective and Moving-Target Tags (`latest-good` & `stable-build`)
- **Evidence Tags**: `latest-good` (Commit `da6b2ed`) and `stable-build` (Commit `ff1fb9a`)
- **Tag Type**: Annotated tags (`tag`)
- **Team Impact**: Using subjective names like `latest-good` or `stable-build` treats git tags as floating pointers rather than immutable release anchors. Commit `da6b2ed` (`latest-good`) came *after* `release_2` and *before* `1.5.0`, yet its name claims it is "latest-good" without describing any feature set.
- **Rollback & Traceability Risk**: High Risk. Recorded in **Incident 2 (2026-05-04)** and **Incident 3 (2026-03-31)**. Operations deployed hotfixes from `latest-good`, but because the tag carried no semantic version or linked release notes, incident responders during subsequent outages could not determine what features or fixes were running in production.

### Problem 6: Unannotated Release Tag Lacking Audit Metadata (`v1.4.2`)
- **Evidence Tag**: `v1.4.2` (Commit `ff1fb9a`)
- **Tag Type**: Lightweight tag (`commit`)
- **Team Impact**: `v1.4.2` is a lightweight tag pointing directly to a commit object. It lacks an embedded tagger name, email address, creation timestamp, or tag message explaining the release context.
- **Rollback & Traceability Risk**: Compliance and security audits fail because there is no cryptographically signed or metadata-backed proof of who cut the release or when it was created. Additionally, lightweight tags are easily deleted or moved by accident without leaving an audit trail.

### Problem 7: Vague Feature Label (`patch-new`)
- **Evidence Tag**: `patch-new` (Commit `82535d9`)
- **Tag Type**: Lightweight tag (`commit`)
- **Team Impact**: Points to commit `82535d9` alongside `v2-final-FINAL`. The name `patch-new` provides zero information about which component was patched, what version bump occurred, or whether breaking changes were introduced.
- **Rollback & Traceability Risk**: Engineers searching the release history during post-mortems cannot correlate `patch-new` with customer-reported issues or changelog entries.

---

## Conclusion & Recommended Action

The existing tag history contains 8 tags, none of which provide clean, sortable, auditable release traceability. To restore operational safety and enable 1-command rollbacks:
1. Define a strict semantic versioning policy (`vMAJOR.MINOR.PATCH`).
2. Require annotated tags (`git tag -a`) for all production releases.
3. Establish a clear release map tying semver tags to exact commit hashes and deployment records.
