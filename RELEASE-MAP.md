# Release Map & Deployment Traceability Record

## 1. Production Release Mapping Matrix

This release map connects Git release tags directly to their immutable commit SHAs, deployment dates, feature sets shipped, and verified rollback targets. It serves as the single source of truth for answering:
1. **What code is currently running in production?**
2. **What previous known-good version do we roll back to during an incident?**

| Version (Tag) | Commit (Short Hash) | Date | What Shipped | Rollback Target |
|---|---|---|---|---|
| **`v1.1.1`** | `82535d9` | 2026-05-04 | Fixed checkout button issue, added structured audit logging, standardized service version initialization | **`v1.1.0`** (`86989b6`) |
| **`v1.1.0`** | `86989b6` | 2026-04-10 | Added payment token caching, improved checkout timeout resilience, updated operational changelogs | **`v1.0.0`** (`ff1fb9a`) |
| **`v1.0.0`** | `ff1fb9a` | 2026-03-15 | Initial baseline release of the Checkout Service with core architecture, app scaffolding, and documentation | N/A (Baseline Release) |

---

## 2. Legacy Mapping vs. Structured SemVer Mapping

The table below contrasts the legacy unversioned deployment references against the newly established traceable release tags:

| Historical Date | Legacy Flawed Tag / Entry | Resolved Semantic Tag | Commit SHA | Resolved Status |
|---|---|---|---|---|
| 2026-03-15 | `stable-build` / Missing record | `v1.0.0` | `ff1fb9ab88e9e20a4311bb8e6f5f71139f94673a` | Production Verified Baseline |
| 2026-04-10 | Assumed `v1.4.2` / Undocumented | `v1.1.0` | `86989b62c378384467da816a1b6f687ee9ea0c51` | Production Verified Stable |
| 2026-04-22 | Flawed rollback to `release_2` | Rollback to `v1.0.0` | `ff1fb9ab88e9e20a4311bb8e6f5f71139f94673a` | Recovered from 502 Incident |
| 2026-05-04 | Deployed from `latest-good` | `v1.1.1` | `82535d9c7b97066e13c3f4afd92f7227d531664b` | Current Production Version |

---

## 3. Rollback Traceability Demonstration

### 3.1 Step-by-Step Incident Rollback Procedure
In the event of an outage or regression in production (e.g., in current release `v1.1.1`), the operations team executes this deterministic rollback procedure:

```bash
# 1. Query all releases in descending semantic version order
git tag --sort=-v:refname

# Expected Output:
# v1.1.1   <-- Current faulty release
# v1.1.0   <-- Immediate known-good rollback target
# v1.0.0   <-- Baseline release

# 2. Inspect the rollback target tag metadata and commit
git show v1.1.0

# 3. Check out the verified previous known-good tag
git checkout v1.1.0

# 4. Trigger deployment / start service
node src/app.js
```

### 3.2 Precision and Reproducibility Rationale
With our standardized Semantic Versioning and annotated release tagging policy, every release tag maps immutably to a verified commit SHA and explicitly documents its predecessor rollback target. In an incident, engineers can instantly run `git tag --sort=-v:refname` to identify the current release and immediately check out the previous known-good tag (`v1.1.0`) without ambiguity. This transforms emergency recovery from high-risk guesswork into an automated, deterministic, and 100% reproducible operational workflow.
