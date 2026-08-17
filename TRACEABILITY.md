# Rollback Traceability & Incident Recovery Guide

## Executive Overview

This document demonstrates how enforcing a strict semantic versioning convention (`vMAJOR.MINOR.PATCH`) and annotated release tags restores complete operational predictability during production incidents.

In a production failure scenario, incident response time is directly determined by how fast and accurately a team can identify and check out the last known-good release. This guide provides exact, tested commands to execute a 1-command rollback.

---

## 1. Step-by-Step Rollback Workflow

### Step 1: Query Release History in Sorted Order
Run the standard semver descending sort command to view all release tags:
```bash
git tag --sort=-v:refname
```

**Output**:
```
v2.0.0      <-- Currently deployed release in production (Failing)
v1.1.1      <-- Target: Last known-good production release
v1.1.0      <-- Previous release
v1.0.0      <-- Baseline release
```

### Step 2: Identify current release and target rollback version
From `RELEASE-MAP.md` or `RELEASES.md`, locate the current release (`v2.0.0`) and note its explicit **Rollback Target** (`v1.1.1`).

### Step 3: Checkout the Target Known-Good Tag
Execute a single git checkout command to move HEAD directly to the audited tag object:
```bash
git checkout v1.1.1
```

### Step 4: Verify checked-out state and trigger redeployment
Verify that the working tree matches `v1.1.1`:
```bash
git describe --tags
# Output: v1.1.1
```

Redeploy the verified code or container artifact built from `v1.1.1` to the production environment.

---

## 2. Comparative Analysis: Clean History vs. Past Tag Chaos

### The Past Tagging Chaos
In the original repository history, tags were an unorganized mix of arbitrary strings (`release_2`, `latest-good`, `v2-final-FINAL`, `version-1.0`).
- **Incident 1 (2026-04-22)**: Responders attempted to roll back to `release_2`, but `release_2` was an unannotated lightweight tag pointing to an untested commit. The rollback failed, extending production downtime.
- **Incident 3 (2026-03-31)**: Responders could not determine what code was running because tags like `stable-build` and `patch-new` provided no version order or audit metadata.

### The Clean Tag Solution
With our new structured annotated tags (`v1.0.0`, `v1.1.0`, `v1.1.1`, `v2.0.0`) and explicit release notes:
1. **Precise Target Identification**: `git tag --sort=-v:refname` guarantees deterministically ordered release tags, making target selection immediate and unambiguous.
2. **Reproducible Code State**: `git checkout v1.1.1` guarantees checking out the exact, immutable commit (`86989b6`) that passed production verification on 2026-05-04.
3. **Complete Audit Trail**: Each annotated tag embeds the tagger identity, creation timestamp, and signed release notes, eliminating guesswork during high-pressure outages.
