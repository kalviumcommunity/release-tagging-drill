# Tag Audit: Checkout Service

## Purpose

This audit identifies release-tagging and deployment-traceability problems in the existing repository. Findings are based on `CHANGELOG.md`, `docs/release-notes-old.md`, `docs/deployment-history.md`, `docs/incident-log.md`, and the available Git history.

## Findings

### 1. Inconsistent release tag naming

**Evidence:** `docs/release-notes-old.md` contains names such as `v1.4.2`, `version-1.0`, `release_2`, `1.5.0`, `v2-final-FINAL`, `stable-build`, `latest-good`, and `patch-new`.

**Problem:** These names do not follow one consistent versioning convention. Some use semantic versions, while others use informal labels or inconsistent prefixes.

**Impact:** Team members cannot reliably identify release order, determine which version is newer, or distinguish official releases from temporary references.

**Recommendation:** Use the `vMAJOR.MINOR.PATCH` naming convention for releases, such as `v1.4.2`.

### 2. Missing semantic-versioning information

**Evidence:** The release notes list `1.5.0` and `v1.4.2`, but do not explain the compatibility or change-type decisions behind these versions.

**Problem:** The repository does not document a consistent policy for deciding when to increment MAJOR, MINOR, or PATCH.

**Impact:** Different contributors may assign versions inconsistently, making release history harder to interpret.

**Recommendation:** Document Semantic Versioning rules and apply them consistently: MAJOR for incompatible changes, MINOR for backward-compatible functionality, and PATCH for backward-compatible bug fixes.

### 3. Production deployments are not reliably mapped to versions

**Evidence:** `CHANGELOG.md` says the 2026-05-04 deployment used `latest-good`. The deployment history also records an undocumented production tag for 2026-04-10 and an unknown production version on 2026-03-31.

**Problem:** The deployment records do not consistently identify a specific, traceable release.

**Impact:** During an incident, responders may be unable to determine the exact code running in production.

**Recommendation:** Record the semantic version, commit hash, deployment date, and release details for each production deployment.

### 4. Rollback target is ambiguous and potentially unsafe

**Evidence:** `docs/incident-log.md` states that on 2026-04-22, the team rolled back to `release_2`, which pointed to an older unsupported commit.

**Problem:** The rollback reference does not clearly identify a supported, verified release.

**Impact:** Rolling back to an unsuitable commit can introduce further failures or restore unsupported behavior.

**Recommendation:** Use a verified release tag as the rollback target and document the tag-to-commit mapping and deployment history.

### 5. Lightweight rollback tag lacks release metadata

**Evidence:** `docs/incident-log.md` identifies `release_2` as a lightweight tag without an annotation.

**Problem:** The tag itself does not include an annotated release message describing the release or rollback context.

**Impact:** Operators have less information available when inspecting the tag during an incident.

**Recommendation:** Create annotated release tags with messages describing the version and the changes included.

### 6. Release notes use vague descriptions

**Evidence:** `docs/release-notes-old.md` describes `release_2` as containing "minor changes and rollback candidate," labels `v2-final-FINAL` as "production-ready? maybe," and describes `stable-build` only as a stable build from a branch.

**Problem:** These descriptions do not clearly specify what changed or establish whether the referenced version is an approved release.

**Impact:** Developers and operators cannot confidently determine what shipped or whether a version is suitable for deployment.

**Recommendation:** Maintain structured release notes with version, date, Added, Changed, Fixed, and rollback-target sections.

### 7. Deployment history contains gaps

**Evidence:** `docs/deployment-history.md` records a missing deployment record for `stable-build` on 2026-03-15 and an unknown production version on 2026-03-31.

**Problem:** The deployment history does not provide a complete record of the versions deployed.

**Impact:** It is difficult to reconstruct release history or investigate incidents accurately.

**Recommendation:** Maintain a release map linking every release tag to its commit hash, date, and shipped changes. Update deployment records whenever a release is deployed or rolled back.

### 8. Changelog entries do not provide sufficient traceability

**Evidence:** `CHANGELOG.md` contains entries such as "Production deployment assumed to be `v1.4.2`" and "Unknown version running in production."

**Problem:** The changelog records uncertainty without identifying the exact commit or verified release associated with each deployment.

**Impact:** The changelog cannot reliably serve as an operational record of what code was released.

**Recommendation:** Link each release entry to its semantic version and commit hash, and distinguish confirmed deployments from unverified historical information.

## Conclusion

The repository has inconsistent tag naming, incomplete release documentation, weak rollback traceability, and gaps in its deployment history. Standardizing on annotated semantic-version tags and maintaining a release map and structured release notes will make releases easier to audit and safer to deploy or roll back.