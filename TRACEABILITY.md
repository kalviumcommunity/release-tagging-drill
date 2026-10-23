# Rollback Traceability Guide (`TRACEABILITY.md`)

## 1. Objective
This document outlines the standard operating procedure (SOP) for executing a fast, precise, and traceable production rollback using the repository's semantic release tags.

---

## 2. Step-by-Step Traceable Rollback Procedure

When an alert triggers in production (e.g., service health check failures, elevated HTTP 5xx error rates, or payment processing degradation), follow these exact steps:

### Step 1: List and Sort Release History
Identify the current production release and the immediately preceding known-good release:

```bash
git tag --sort=-v:refname
```

**Terminal Output:**
```text
v1.1.1
v1.1.0
v1.0.0
```

From this sortable output:
- **Active Release:** `v1.1.1` (failing release)
- **Last Known-Good Target:** `v1.1.0` (target for immediate redeployment)

---

### Step 2: Inspect Target Metadata
Before initiating redeployment, verify the release author, creation timestamp, and included changes:

```bash
git show v1.1.0
```

**Verification Details:**
- **Tag:** `v1.1.0`
- **Target Commit:** `86989b6`
- **Annotation Message:** `Release 1.1.0: Add structured changelog, historical documentation, and checkout button fixes`

---

### Step 3: Checkout and Redeploy
Check out the clean, known-good tag and trigger redeployment:

```bash
git checkout v1.1.0
```

Deploy the checked-out commit to the production cluster:
```bash
# Example deployment / container build commands
docker build -t checkout-service:v1.1.0 .
docker-compose up -d
```

---

## 3. Why This Rollback Is Precise and Reproducible

Our standardized Semantic Versioning and annotated tag history replaces guesswork with mathematical determinism. Under the original tag history (`latest-good`, `release_2`, `v2-final-FINAL`), engineers were forced to guess which commit was stable, resulting in documented outages like Incident 1 where rolling back to `release_2` deployed an incompatible older commit. 

With strict `vMAJOR.MINOR.PATCH` annotated tags, running `git tag --sort=-v:refname` guarantees that the previous known-good version (`v1.1.0`) is always the immediate preceding entry, anchored to an immutable cryptographic commit object with verified author and changelog metadata.
