# Release Map

This table maps semantic version tags to their underlying Git commits and deployment records, providing clear traceability of what code is running in production.

| Version (tag) | Commit (short hash) | Date       | What Shipped                               |
|---------------|---------------------|------------|--------------------------------------------|
| `v1.0.0`      | `4febcc6`           | 2026-03-01 | Initial release of the Checkout service    |
| `v1.1.0`      | `da6b2ed`           | 2026-04-10 | Add deployment history documentation       |
| `v1.1.1`      | `c17d4c4`           | 2026-05-04 | Update dummy checkout service              |

## Traceable Rollback

The new consistent tag history ensures that rolling back production is precise and reproducible, instead of relying on guesswork. If a recent deployment fails, operations can easily list and sort tags, check out the previous known-good version, and redeploy.

To demonstrate a traceable rollback from `v1.1.1` to the previous known-good version (`v1.1.0`), run the following commands:

```bash
# View the cleanly sorted release history to identify the last known-good tag
git tag --sort=-v:refname

# Check out the previous known-good tag (v1.1.0)
git checkout v1.1.0

# You can now safely trigger a deployment or build from this stable state.
```

Compared to the original messy history, this approach provides a clear chronological order. Tags are now immutable snapshots, allowing incident responders to deterministically roll back to the exact code that was previously running in production.
