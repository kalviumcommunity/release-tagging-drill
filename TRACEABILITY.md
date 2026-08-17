# Release Traceability & Rollback Guide

## 1. Overview

This guide defines the procedures for establishing end-to-end release traceability and executing rapid, deterministic rollbacks for the Checkout Service. By adopting Semantic Versioning (`vMAJOR.MINOR.PATCH`) and annotated release tags, every deployed artifact in production directly traces back to an immutable Git commit, an author audit trail, and a verified rollback target.

---

## 2. Release Identification & Rollback Playbook

When an incident occurs in production, operators must not guess or manually inspect unversioned commit histories. Instead, follow the standard four-step incident rollback workflow:

### Step 1: Identify Current and Available Releases
Query the repository release history sorted in descending semantic version order:
```bash
git tag --sort=-v:refname
```

**Terminal Output:**
```text
v1.1.1
v1.1.0
v1.0.0
```
- **`v1.1.1`**: Current production release (failing).
- **`v1.1.0`**: Immediate previous known-good release (rollback candidate).
- **`v1.0.0`**: Baseline release.

---

### Step 2: Inspect and Verify the Rollback Target Tag
Inspect the annotated tag metadata, tagger identity, creation timestamp, and commit SHA:
```bash
git show v1.1.0
```

**Terminal Output:**
```text
tag v1.1.0
Tagger: Bala <balagiri702@gmail.com>
Date:   Mon Aug 17 10:43:54 2026 +0530

Release 1.1.0: Add payment token caching and checkout timeout handling

commit 86989b62c378384467da816a1b6f687ee9ea0c51
Author: sriman <srimandgl2004@gmail.com>
...
```

---

### Step 3: Check Out the Known-Good Release
Check out the verified rollback tag directly or deploy the artifact matching `v1.1.0`:
```bash
git checkout v1.1.0
```

*Note:* In automated CI/CD environments, deployment triggers run `deploy --tag v1.1.0`.

---

### Step 4: Validate Service Health
Verify that the service boots cleanly on the target version:
```bash
node src/app.js
```

---

## 3. Precision & Reproducibility Analysis

### 3.1 Comparison: Legacy Chaos vs. Semantic Release Policy

| Attribute | Legacy State (Before) | Structured State (After) |
|---|---|---|
| **Tag Sorting** | Failed due to names like `v2-final-FINAL`, `release_2`, `1.5.0` | Clean descending sort via `git tag --sort=-v:refname` |
| **Commit Linkage** | Lightweight tags pointed to unverified / obsolete commits | Annotated tags link directly to immutable commit SHAs |
| **Rollback Target** | Unknown / guesswork (caused 2026-04-22 502 outage) | Explicitly documented in `RELEASE-MAP.md` and `RELEASES.md` |
| **Auditability** | Zero tagger metadata or signatures | Complete tagger identity, timestamp, and message |
| **Automation** | CI/CD pipelines failed to parse version strings | Strict SemVer regex enables automated validation and deployments |

### 3.2 Key Takeaway
Our new, consistent tag history eliminates ambiguity by binding every release to an immutable semantic tag and a verified predecessor. In an emergency, operations no longer relies on guesswork or informal communication; running `git tag --sort=-v:refname` immediately surfaces `v1.1.0` as the exact, tested rollback point. This makes production recovery deterministic, auditable, and 100% reproducible across all environments.
