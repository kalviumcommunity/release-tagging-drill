# Tag Audit

The repository currently has no active git tags, but the historical release notes and deployment documentation reference several tags that caused real operational confusion. The audit below captures the specific problems those references create.

## Problems found

1. `release_2`
   - Evidence: referenced in the changelog and deployment history as the rollback target after a payment gateway regression.
   - What it means for the team: the team used a non-standard tag name as a rollback marker even though it did not carry a semantic version or release date.
   - Risk: rollback becomes ambiguous because the tag is not tied to a clear release record and cannot be confidently traced to a known-good deployment.

2. `latest-good`
   - Evidence: listed as the deployment reference for 2026-05-04 in the deployment history.
   - What it means for the team: operations treated a descriptive label as if it were the authoritative release identifier.
   - Risk: a rollback decision is guesswork because the tag does not map to a semantic version, release notes, or a specific commit in a consistent format.

3. `stable-build`
   - Evidence: referenced in the deployment history and old release notes as a production candidate.
   - What it means for the team: the release was labeled as if it were a stable build, but the name did not convey whether it was a patch, feature release, or hotfix.
   - Risk: support and audit teams cannot reliably determine whether this build is the correct production target or whether it should be replaced by a newer release.

4. `patch-new`
   - Evidence: appears in the old release notes as a patch release marker.
   - What it means for the team: the team used a tag name that suggests a patch without actually encoding the version sequence.
   - Risk: operations cannot tell which release this patch superseded or whether it should be the rollback target for a prior incident.

5. `v2-final-FINAL`
   - Evidence: appears in the old release notes as a version heading.
   - What it means for the team: the tag mixes a version-like prefix with informal suffixes and a non-standard “final” marker.
   - Risk: release ordering becomes misleading, and the team cannot determine whether this should be treated as a stable release or as a temporary placeholder.

6. `1.5.0`
   - Evidence: appears in the old release notes as a standalone version heading.
   - What it means for the team: the release was named without the `v` prefix that most git tag conventions expect.
   - Risk: automation and human operators are more likely to misread or mis-sort this tag compared with a standard `vMAJOR.MINOR.PATCH` form.
