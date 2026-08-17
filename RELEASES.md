# Release Notes & Changelog

This document provides structured release notes for all official versions of the Checkout Service. Each release entry specifies the version, release date, categorized changes (Added / Changed / Fixed), and the verified rollback target for operational safety.

---

## [v1.1.1] — 2026-05-04

### Summary
Patch release resolving UI checkout interaction issues and incorporating structured audit logging for production monitoring and telemetry.

### Categorized Changes
* **Added:**
  * Structured audit logging for tracking payment checkout lifecycle events.
  * Explicit console telemetry for service startup.
* **Changed:**
  * Updated `src/app.js` service version initialization and entrypoint logging.
* **Fixed:**
  * Resolved checkout button click handler event race condition.
  * Fixed inconsistent service startup logging parameters.

### Operational Metadata
* **Git Tag:** `v1.1.1` (Annotated)
* **Target Commit:** `82535d9c7b97066e13c3f4afd92f7227d531664b`
* **Rollback Target:** **`v1.1.0`** (`86989b6`) — Immediate previous stable release.

---

## [v1.1.0] — 2026-04-10

### Summary
Minor feature release introducing payment token caching to reduce transaction latency and handling timeout exceptions during downstream payment gateway communications.

### Categorized Changes
* **Added:**
  * Payment token caching mechanism to accelerate repeated transaction authorizations.
  * Configurable timeout handling and retry policies for payment provider APIs.
* **Changed:**
  * Updated operational changelog and deployment documentation to reflect caching metrics.
* **Fixed:**
  * Fixed transient HTTP 504 gateway timeout issues during peak checkout traffic.

### Operational Metadata
* **Git Tag:** `v1.1.0` (Annotated)
* **Target Commit:** `86989b62c378384467da816a1b6f687ee9ea0c51`
* **Rollback Target:** **`v1.0.0`** (`ff1fb9a`) — Initial baseline release.

---

## [v1.0.0] — 2026-03-15

### Summary
Initial production baseline release of the Checkout Service microservice architecture.

### Categorized Changes
* **Added:**
  * Core checkout service application scaffolding (`src/app.js`).
  * Base repository documentation, deployment specifications, and service configuration.
  * Health check and service lifecycle execution hooks.
* **Changed:**
  * Initial production promotion from development scaffold.
* **Fixed:**
  * N/A (Initial Release).

### Operational Metadata
* **Git Tag:** `v1.0.0` (Annotated)
* **Target Commit:** `ff1fb9ab88e9e20a4311bb8e6f5f71139f94673a`
* **Rollback Target:** N/A (Baseline Release; rollback requires reverting to prior legacy infrastructure).
