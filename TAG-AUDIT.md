# Tag Audit

After auditing the repository's tag history and deployment records, here are five specific tagging problems and the risks they create:

1. **Meaningless Tag Names (`v2-final-FINAL`)**
   - **Evidence:** The tag `v2-final-FINAL` appears in the old release notes.
   - **Meaning & Risk:** This name does not follow semantic versioning. It creates confusion about whether this is actually the final production build or just a working branch. When issues occur, engineers cannot reliably determine its order in the release history (`git tag --sort=-v:refname` fails), making it dangerous to use as a rollback target.

2. **Inconsistent Prefixing (`version-1.0` and `1.5.0` vs `v1.4.2`)**
   - **Evidence:** The history contains `version-1.0`, `1.5.0`, and `v1.4.2` mixed together.
   - **Meaning & Risk:** Lack of a consistent naming convention breaks automated sorting and parsing tools. If deployment scripts expect a `v` prefix, they will fail on `1.5.0`. This inconsistency makes it difficult to quickly trace which version is currently running in production or identify the previous known-good release during an incident.

3. **Lightweight and Unannotated Tags (`release_2`)**
   - **Evidence:** The incident log shows an emergency rollback to `release_2`, which was a lightweight tag.
   - **Meaning & Risk:** Lightweight tags are merely bookmarks to a commit; they lack metadata (author, date, release message). Without annotations, there is no audit trail of who created the release or what it contains. In this case, `release_2` pointed to an older unsupported commit without anyone realizing it, exacerbating the production incident during rollback.

4. **Ambiguous / Mutable Tag Names (`latest-good` / `stable-build`)**
   - **Evidence:** Tags like `latest-good` and `stable-build` were used for deployments.
   - **Meaning & Risk:** Tags should be immutable snapshots of a specific version. Using a "rolling" tag name like `latest-good` means the tag could be deleted and recreated to point to different commits over time. When an incident occurs (as in the May 4th deployment), the team cannot definitively trace what code was actually deployed or rolled back to, destroying traceability.

5. **Vague Patch Tags (`patch-new`)**
   - **Evidence:** The tag `patch-new` is listed as a new patch release.
   - **Meaning & Risk:** This tag completely omits the base version it is patching. Is it a patch for v1 or v2? This lack of context means engineers cannot determine if this patch is safe to deploy or if it introduces breaking changes from a newer major version. It makes identifying a safe rollback target impossible.
