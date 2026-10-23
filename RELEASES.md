# Release Notes (`RELEASES.md`)

This document provides formal release notes for all annotated releases within the Checkout Service repository. Each entry records the version, release date, functional changes grouped by category (Added, Changed, Fixed), and an explicit, pre-defined rollback target.

---

## Release v1.1.1

- **Release Date:** 2026-06-06
- **Git Tag:** `v1.1.1`
- **Commit:** `82535d9`
- **Release Type:** Patch Release
- **Rollback Target:** `v1.1.0`

### Summary of Changes
- **Added:**
  - Introduced explicit `SERVICE_NAME` configuration variable in `src/app.js` to decouple runtime logging from raw package metadata.
  - Added structured service initialization logs indicating application bootstrap lifecycle.
- **Changed:**
  - Standardized runner logic: transitioned from deprecated `runCheckout()` invocation pattern to uniform `run()` entrypoint.
  - Refactored version string reference to standard `SERVICE_VERSION` token placeholder.
- **Fixed:**
  - Removed hardcoded local placeholder constants that conflicted with dynamic container orchestration environment variables.

### Operational Guidance
- **Deployment Command:** `git checkout v1.1.1`
- **Rollback Procedure:** In the event of application startup crashes or configuration parsing errors in `v1.1.1`, immediately roll back to **`v1.1.0`** using:
  ```bash
  git checkout v1.1.0
  ```

---

## Release v1.1.0

- **Release Date:** 2026-06-06
- **Git Tag:** `v1.1.0`
- **Commit:** `86989b6`
- **Release Type:** Minor Release
- **Rollback Target:** `v1.0.0`

### Summary of Changes
- **Added:**
  - Added centralized documentation records including historical release logs and changelog indexes.
  - Introduced audit logging references for tracking payment transactions and operator deployments.
- **Changed:**
  - Standardized repository documentation architecture under `docs/`.
  - Harmonized release notes structure across all service modules.
- **Fixed:**
  - Resolved checkout submission button event handler bug causing intermittent customer checkout timeouts.

### Operational Guidance
- **Deployment Command:** `git checkout v1.1.0`
- **Rollback Procedure:** If documentation links or checkout button event listeners introduce customer regressions, roll back to **`v1.0.0`** using:
  ```bash
  git checkout v1.0.0
  ```

---

## Release v1.0.0

- **Release Date:** 2026-06-06
- **Git Tag:** `v1.0.0`
- **Commit:** `ff1fb9a`
- **Release Type:** Major Baseline Release
- **Rollback Target:** None (Initial Production Baseline)

### Summary of Changes
- **Added:**
  - Initial scaffolding for the Checkout Service application (`src/app.js`).
  - Core service entrypoint `runCheckout()` with initial logging outputs.
  - Project documentation foundation (`README.md`, `CHANGELOG.md`).
  - Initial operational records (`docs/deployment-history.md`, `docs/incident-log.md`).
- **Changed:**
  - First production-ready baseline established.
- **Fixed:**
  - N/A (Initial release).

### Operational Guidance
- **Deployment Command:** `git checkout v1.0.0`
- **Rollback Procedure:** This is the baseline release. If a critical failure occurs, engage the incident response lead to evaluate maintenance mode.
