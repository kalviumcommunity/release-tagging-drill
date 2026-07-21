# Release Map

This map links each release tag to the commit that should be treated as the deployment target and the rollback target.

| Version (tag) | Commit (short hash) | Date | What Shipped |
| --- | --- | --- | --- |
| v1.0.0 | 4febcc6 | 2026-06-06 | Initial repository scaffold and assignment baseline. |
| v1.1.0 | c17d4c4 | 2026-06-06 | Added the dummy checkout service as the first functional milestone. |
| v1.1.1 | 82535d9 | 2026-06-06 | Finalized the placeholder release and documented the release trail. |

## Deployment record

- Use the highest version in this table as the current production release.
- If that release fails, roll back to the previous row in this table and redeploy from that tag.
