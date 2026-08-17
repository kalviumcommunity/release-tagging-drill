# Versioning Convention

## 1. Semantic Versioning Rules
The team strictly adheres to Semantic Versioning (SemVer). The version number is composed of `MAJOR.MINOR.PATCH`.
- **MAJOR**: Incremented for incompatible API or breaking changes. Example: `v1.4.2 -> v2.0.0`
- **MINOR**: Incremented for adding functionality in a backwards-compatible manner (new features). Example: `v1.4.2 -> v1.5.0`
- **PATCH**: Incremented for backwards-compatible bug fixes. Example: `v1.4.2 -> v1.4.3`

## 2. Tag Naming Format
All release tags must begin with the `v` prefix followed exactly by the SemVer version.
- **Pattern**: `vMAJOR.MINOR.PATCH`
- **Example**: `v2.0.0`

## 3. Annotated vs Lightweight Tags
For all releases, the team **must use annotated tags**. 
- **Why**: Annotated tags are stored as full objects in the Git database. They include the tagger name, email, date, and a tagging message. This provides a clear audit trail and traceability for every release, unlike lightweight tags which are just pointers to a commit.
- **Exact Command**: `git tag -a v1.1.0 -m "Release 1.1.0: Added payment token caching"`

## 4. Pre-release Rule
When marking a release candidate or beta, a hyphen and a pre-release identifier are appended to the SemVer version.
- **Format**: `vMAJOR.MINOR.PATCH-rc.X` or `vMAJOR.MINOR.PATCH-beta.X`
- **Example**: `v1.5.0-rc.1`
- **Ordering**: Pre-releases always order *before* their respective final releases. For instance, `v1.5.0-rc.1` comes before `v1.5.0-rc.2`, which comes before the final release `v1.5.0`.
