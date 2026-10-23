# Release Notes

## v1.0.0 — 2026-06-06

Initial known-good release of the release-tagging exercise.

### Added

- Repository scaffold and the baseline release record.

### Rollback target

None; this is the first known-good release. If a later release fails, return
to `v1.0.0`.

## v1.1.0 — 2026-06-06

Added the initial dummy checkout service.

### Added

- Checkout-service startup behavior and version output.

### Changed

- Replaced the initial repository-only baseline with a runnable service
  placeholder.

### Rollback target

Return to `v1.0.0` if this release fails.

## v1.1.1 — 2026-06-06

Refined the checkout-service runtime output and service identity.

### Changed

- Updated the runtime output to use the `checkout-service` service name and
  `0.0.0-placeholder` service version.

### Fixed

- Corrected the service startup wording and output structure.

### Rollback target

Return to `v1.1.0` if this patch release fails.
