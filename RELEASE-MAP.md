# Release Map

This map ties each structured release tag to an immutable commit and the
deployment content associated with it.

| Version (tag) | Commit (short hash) | Date | What Shipped |
| --- | --- | --- | --- |
| `v0.1.0` | `4febcc6` | 2026-06-06 | Initial repository baseline and release-tagging exercise entry point. |
| `v1.0.0` | `ff1fb9a` | 2026-06-06 | Checkout-service scaffold, placeholder application, and initial operational documentation. |
| `v1.1.0` | `c17d4c4` | 2026-06-06 | First dummy checkout-service implementation added to the scaffold. |
| `v1.1.1` | `82535d9` | 2026-06-06 | Checkout-service startup/version output update, treated as a backward-compatible patch. |

## Traceable rollback

```sh
git tag --sort=-v:refname        # the sortable release history
git describe --tags --abbrev=0   # identify the current release from the checked-out commit
git checkout v1.1.0              # roll back to the last known-good tag, then redeploy
```

The consistent `vMAJOR.MINOR.PATCH` names sort deterministically, and each
annotated tag resolves to the commit recorded above. Unlike the original
labels such as `latest-good` and `release_2`, a responder can identify the
current release, select the previous known-good tag, and reproduce the rollback
without guessing which commit a mutable name meant.
