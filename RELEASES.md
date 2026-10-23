# Release Notes

## v0.1.0 — 2026-06-06

### Added

- Initial repository baseline for the checkout-service release exercise.

### Rollback target

This is the first known release, so there is no earlier tagged version. Keep
the pre-release source or deployment snapshot as the fallback until a later
release establishes `v0.1.0` as its rollback target.

## v1.0.0 — 2026-06-06

### Added

- Checkout-service scaffold and placeholder application.
- Initial deployment, incident, and legacy release-note records.

### Changed

- Established the repository structure used for release operations.

### Rollback target

Rollback to `v0.1.0` if the scaffold release fails.

## v1.1.0 — 2026-06-06

### Added

- Dummy checkout-service implementation on top of the scaffold.

### Changed

- Promoted the service from an assignment scaffold to a runnable checkout
  service baseline.

### Rollback target

Rollback to `v1.0.0` if the service implementation fails.

## v1.1.1 — 2026-06-06

### Fixed

- Corrected the checkout-service startup and version output in the patch
  update.

### Rollback target

Rollback to `v1.1.0` if this patch release fails.
