# Tag History Audit

## Audit result

`git tag` returned no Git tag references in this repository. The names below are
therefore legacy deployment labels found in the repository's historical
documentation, not reproducible Git objects. That distinction is itself an
operational risk: the team cannot resolve any of these names to a commit with
Git.

| Evidence | Problem | Team impact | Rollback or traceability risk |
| --- | --- | --- | --- |
| `latest-good` in `docs/deployment-history.md` and `CHANGELOG.md` | The name is mutable and does not identify a version, commit, or release date. It is not present in `git tag`. | Operators can apply different code while using the same label at different times. | A rollback command using `latest-good` cannot be reproduced or audited, and the intended hotfix commit is unknown. |
| `release_2` in `docs/incident-log.md` and `docs/deployment-history.md` | The name uses an undocumented counter instead of a semantic version, and the incident log says it was a lightweight tag pointing to an older unsupported commit. | Engineers cannot infer what changed in release 2 or whether it is compatible with the running service. | The documented emergency rollback already selected an unsafe commit; the tag cannot establish a known-good deployment boundary. |
| `v1.4.2` in `docs/release-notes-old.md` and `docs/deployment-history.md` | The same version is described as an assumed staging promotion, while the deployment history says the production tag is undocumented. It is also absent from `git tag`. | Staging and production cannot be proven to contain the same source. | A responder may roll back to a version number that was never the production commit, creating an unverifiable recovery. |
| `stable-build` in `docs/release-notes-old.md` and `docs/deployment-history.md` | The label describes an environment or status, not an immutable release, and has no version or commit identity. | “Stable” can mean different builds to different people, especially after subsequent deployments. | The missing 2026-03-15 deployment record means there is no precise commit to redeploy after an outage. |
| `patch-new` in `docs/release-notes-old.md` | The label gives no major, minor, patch, or release date information and is not a Git tag. | Teams cannot tell whether it is a bug fix, feature release, or replacement for another patch. | A rollback to `patch-new` is guesswork because neither the affected issue nor the exact source is recorded. |
| `v2-final-FINAL` in `docs/release-notes-old.md` | The duplicated “final” suffix signals ad hoc renaming and does not follow `vMAJOR.MINOR.PATCH`. | Automated sorting and release tooling cannot reliably treat it as version 2. | A later “final” label can be mistaken for the same release, obscuring which commit was approved for deployment. |
| `version-1.0` and `1.5.0` in `docs/release-notes-old.md` | These names use two incompatible formats: a word-prefixed partial version and a version without the required `v` prefix or patch-level policy. | Scripts and humans must special-case names rather than applying one release policy. | Version ordering and comparisons can select the wrong rollback candidate, particularly around `1.0` versus `1.5.0`. |

The corrective convention and a new immutable release map are defined in
[`VERSIONING.md`](./VERSIONING.md) and [`RELEASE-MAP.md`](./RELEASE-MAP.md).
