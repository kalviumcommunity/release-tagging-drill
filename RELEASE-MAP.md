# Release Map

This map records the three structured release tags created from the repository
history. The deployment record states what the team should deploy and provides
the rollback relationship for that release.

| Version (tag) | Commit (short hash) | Date | What Shipped |
| --- | --- | --- | --- |
| `v1.0.0` | `ff1fb9a` | 2026-06-06 | Initial release-tagging exercise scaffold; deployment record: baseline known-good release. |
| `v1.1.0` | `c17d4c4` | 2026-06-06 | Added the dummy checkout service and its initial runtime output; deployment record: deploy the checkout-service implementation. |
| `v1.1.1` | `82535d9` | 2026-06-06 | Refined the checkout-service name and version output; deployment record: deploy the patch release after validating the service output. |

## Traceable rollback

```sh
git tag --sort=-v:refname        # the sortable release history
git describe --tags --exact-match HEAD  # identify the current release, if tagged
git checkout v1.1.0              # roll back to the last known-good tag, then redeploy
```

The annotated, consistently named tags identify exact commits and sort in
release order, so the current and previous releases can be verified before a
rollback. Unlike the original names such as `latest-good` and `release_2`,
`v1.1.0` is an immutable, documented target rather than a guess based on a
mutable label or an unrecorded deployment.
