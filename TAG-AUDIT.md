# Tag Audit

The repository currently has no Git tags (`git tag` returns no entries), even
though the operational records refer to several tags. This disconnect means
the names below cannot be resolved to commits today and is itself a release
traceability failure.

| Evidence | Specific problem | Team impact | Rollback or traceability risk |
| --- | --- | --- | --- |
| `release_2` | The name uses an ordinal rather than a version and the incident log says it is a lightweight tag with no annotation. | Engineers cannot tell which product version or changes the tag represents. | The 2026-04-22 rollback could restore an older unsupported commit, and there is no annotation explaining why it was safe. |
| `latest-good` | The name is a mutable-sounding status label, not an immutable release identifier. | Operations cannot distinguish one “good” deployment from another or know when the label changed. | A future move of the label can silently change the rollback target, making the same incident unreproducible. |
| `v1.4.2` | The deployment history says staging was assumed to match `v1.4.2`, while the production tag is undocumented; the repository has no such Git tag. | Promotion decisions rely on an assumption instead of a commit verified in Git. | A responder cannot check the exact production commit or reliably redeploy the supposed release. |
| `stable-build` | This name describes a branch/build state rather than a released version, and the deployment history has no deployment record for it. | Release managers cannot correlate the build with a change set, date, or environment. | The missing record makes it impossible to identify what to roll back to after a deployment from this build. |
| `patch-new` | The name gives no major, minor, or patch number and has no indication of which baseline it patches. | Teams cannot order it against other releases or determine whether it contains a compatible fix. | Rollback selection becomes guesswork because “new” is not a stable relationship to a known-good commit. |
| `v2-final-FINAL` | The suffix is duplicated and informal rather than a valid semantic-version release or pre-release identifier. | Humans and automation may disagree about whether it is the final v2 release. | It cannot be safely sorted or compared with other versions, so an automated rollback may choose the wrong release. |
| `version-1.0` | The prefix and incomplete version omit the patch component required by the team’s release format. | Tooling cannot consistently parse it as a semantic version. | It is unclear whether this is the first release, a branch label, or an abbreviated pointer, weakening audit evidence. |
| `1.5.0` | The release notes omit the required `v` prefix even though other entries use different conventions. | A search for `v1.5.0` will not find the documented release, and naming rules cannot be enforced. | The team may deploy or roll back to different objects depending on whether the prefix is assumed. |

The incident records reinforce the operational consequence: production was
listed as an “Unknown version” on 2026-03-31, and the 2026-05-04 deployment
used `latest-good` without semantic release notes. A consistent annotated tag
sequence and release map are required to turn a name into a commit and a
deployment record.
