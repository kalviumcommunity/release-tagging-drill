# Release Map & Deployment Traceability

## Executive Overview

This document provides the definitive Release Map for the `checkout-service` repository. It maps each structured semantic version tag (`vMAJOR.MINOR.PATCH`) to its exact Git commit hash, deployment date, and functional changelog.

This map bridges the gap between source control, build artifacts, and live production environments, providing a single source of truth for operations, release engineering, and incident response teams.

---

## 1. Tag-to-Commit-to-Deployment Map

| Version (Tag) | Commit (Short Hash) | Deployment Date | What Shipped / Release Summary | Environment | Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **`v2.0.0`** | `82535d9` | 2026-06-06 | Major upgrade to checkout service architecture and payment pipeline | Production | Active / Current |
| **`v1.1.1`** | `86989b6` | 2026-05-04 | Fixed checkout button timeout issue and added comprehensive audit logging | Production | Deprecated (Known Good) |
| **`v1.1.0`** | `28bd110` | 2026-04-10 | Added payment token caching and timeout fixes | Production | Deprecated (Known Good) |
| **`v1.0.0`** | `ff1fb9a` | 2026-03-15 | Initial baseline release of checkout service and documentation scaffold | Production | Initial Baseline |

---

## 2. Traceable Rollback Demonstration & Recovery Commands

When a production deployment failure occurs (e.g., `v2.0.0` exhibits elevated error rates or API incompatibilities), the release map and clean tag history enable instant, precise, one-command rollbacks without manual hash inspection.

### Step 1: List and Sort Release History
Inspect the sortable release history in descending semantic version order:
```bash
git tag --sort=-v:refname
```

**Expected Output**:
```
v2.0.0    <-- Current broken release in production
v1.1.1    <-- Target: Last known-good production release
v1.1.0
v1.0.0
```

### Step 2: Roll Back to the Last Known-Good Version
Checkout the exact target tag (`v1.1.1`) to restore code state deterministically:
```bash
git checkout v1.1.1
```

### Step 3: Verify and Redeploy
Verify the checked-out version and redeploy the tagged build artifact to production:
```bash
# Verify current HEAD matches v1.1.1 tag metadata
git describe --tags

# Redeploy container or build artifact built from v1.1.1
```

---

## 3. Why Clean Tags Enable Precise Rollbacks

In the original repository history, tags were a chaotic collection of arbitrary labels (`release_2`, `latest-good`, `v2-final-FINAL`), making rollbacks dangerous guesswork under high-pressure outages.

By enforcing immutable, annotated `vMAJOR.MINOR.PATCH` tags mapped directly to deployment records:
1. **Zero Guesswork**: Incident responders can immediately identify `v1.1.1` as the exact, audited, known-good tag prior to `v2.0.0`.
2. **Deterministic Reproducibility**: Checking out `v1.1.1` builds the exact code and configuration that ran safely in production on 2026-05-04.
3. **Audit Trail Integrity**: Annotated tags store tagger metadata, timestamps, and signed release messages, ensuring full compliance and operational safety.
