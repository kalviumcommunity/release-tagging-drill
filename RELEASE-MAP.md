# Release to Deployment Map (`RELEASE-MAP.md`)

## 1. Overview
The Release Map provides an authoritative, single source of truth connecting Git tags, Git commits, deployment dates, operational status, and shipped functionality. It directly answers the two most critical operational questions during normal maintenance and emergencies:
1. **"What is currently running in production?"**
2. **"What is the verified last known-good release to roll back to?"**

---

## 2. Release to Deployment Mapping Table

| Version (tag) | Commit (short hash) | Date | What Shipped | Deployment Environment | Operational Status | Rollback Target |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **`v1.1.1`** | `82535d9` | 2026-06-06 | Standardized checkout service runner, modular service naming, configuration architecture update | Production | **Active (Current)** | `v1.1.0` |
| **`v1.1.0`** | `86989b6` | 2026-06-06 | Structured changelog, historical documentation integration, checkout button fix | Staging / Pre-Prod | **Known-Good (Stable)** | `v1.0.0` |
| **`v1.0.0`** | `ff1fb9a` | 2026-06-06 | Baseline checkout service initialization, payment scaffold, initial application layout | Production Baseline | **Archived (Baseline)** | N/A (Initial Release) |

---

## 3. Legacy Tag to Semantic Version Reconciliation
To resolve the legacy tagging defects identified in `TAG-AUDIT.md`, the historical ad-hoc tags are mapped to their clean semantic counterparts:

| Legacy Ad-Hoc Tag | Target Commit | Issues Resolved | Official Clean Semantic Tag |
| :--- | :--- | :--- | :--- |
| `v2-final-FINAL`, `patch-new` | `82535d9` | Eliminated informal naming and duplicate tagging; established clean patch bump | **`v1.1.1`** |
| `1.5.0` | `86989b6` | Added missing `v` prefix, aligned with chronological commit sequence | **`v1.1.0`** |
| `v1.4.2`, `stable-build` | `ff1fb9a` | Converted lightweight/status tag to annotated baseline release | **`v1.0.0`** |
| `version-1.0`, `release_2` | `28bd110` | Deprecated conflicting/inverted tags on intermediate doc commit | *Deprecated* |
| `latest-good` | `da6b2ed` | Replaced floating state tag with explicit release notes rollback pointers | *Deprecated* |

---

## 4. Traceable Rollback Demonstration

During a production outage or release regression (e.g., unexpected 5xx responses or third-party payment gateway timeouts), incident responders must execute an immediate, deterministic rollback without guesswork.

### Step 1: Query the Sortable Release History
Run Git's version refname sort command to list all official releases in descending order:
```bash
git tag -l "v[0-9]*" --sort=-v:refname
```
**Output:**
```text
v1.1.1
v1.1.0
v1.0.0
```
- **Current Production Release:** `v1.1.1`
- **Immediate Rollback Target:** `v1.1.0`

### Step 2: Checkout the Last Known-Good Version
Inspect the release metadata and check out the verified rollback version:
```bash
# Verify the target commit and release notes
git show v1.1.0

# Check out the target release tag
git checkout v1.1.0
```

### Step 3: Trigger Redeployment
Initiate the service rebuild and container redeployment from tag `v1.1.0`:
```bash
# Example operational redeployment trigger
npm start # or docker run -d checkout-service:v1.1.0
```

---

## 5. Traceability & Precision Impact
By replacing informal, ambiguous tags (`latest-good`, `release_2`, `v2-final-FINAL`) with an immutable sequence (`v1.0.0` -> `v1.1.0` -> `v1.1.1`), the rollback process shifts from subjective guesswork to a single deterministic command. Incident responders no longer need to speculate about which commit is stable or whether a tag includes unvetted changes. The semantic sort order immediately points to `v1.1.0` as the exact prior state, guaranteeing that redeployment returns the system to a verified, reproducible baseline.
