# Rollback Traceability

Use the following commands to inspect the release history and make a rollback explicit:

```bash
git tag --sort=-v:refname
git checkout v1.1.0
```

This new history is precise because each release has a standard semantic version and points to a specific commit. Unlike the original history, where rollback targets such as `release_2` and `latest-good` were ambiguous, the team can now identify the current release, the previous known-good release, and the commit to redeploy with confidence.
