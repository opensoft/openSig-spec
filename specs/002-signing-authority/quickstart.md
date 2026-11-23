# Feature 002 Acceptance Quickstart

## Gate 1 - Isolated synthetic intake

1. Identify the intended Feature 001 integration base and exact Feature 002
   candidate. Use the existing runner for baseline regression. Use a synthetic
   tenant, stub registry, deterministic SharePoint adapter fixture and test
   browser for the new intake checks. Register their modes only through the
   governed runner amendment.
2. Using the mock Viktory Suite 485 consent and synthetic governance records,
   exercise direct upload, reviewer requests for missing evidence, verification
   and supersession. Confirm exact bytes, digest, provenance and review history
   after retry, restart and authorized restore.
3. Import the same bytes from the SharePoint fixture. Verify upload and import
   produce equivalent review decisions and different origin receipts. Exercise
   denial outside the entity or configured root, revoked access, stale version,
   change during download, timeouts, invalid formats and oversized files.
4. Assert that denied or interrupted imports create no reviewable evidence,
   reasons expose no private data, and provider requests contain no writes.
   Walk the browser UI as submitter and reviewer. Retain case IDs and sanitized
   evidence for the intake portions of VIK-02, VIK-03, VIK-06 and VIK-12.

The first gate establishes private intake and review behavior. The signing-time
part of VIK-02 and full certified outcomes are exercised in Gate 3.

## Gate 2 - Controlled Teams and SharePoint qualification

Requires an approved Microsoft 365 test tenant, an HTTPS testing host, a test
Entra app and Teams personal tab, and one version-enabled synthetic SharePoint
library. An authorized administrator provisions the narrowly scoped read grant
and test user roles. Record manifest and candidate digests, tested client
surfaces, authorized scope, and evidence of the start and end settings.

- Install the test app under the tenant's custom app policy. Verify Teams SSO
  in desktop and web, then compare the authenticated standalone route.
- Verify the exact delegated grant is sufficient for approved listing, item and
  version metadata, and the version-content endpoints used by the pilot. Use
  independently known fixture bytes for version A and version B. Qualify the
  selected previous-version path and attempt a source update between selection
  and read for the proposed current-version path. Repeat with a concurrent edit
  during transfer and compare accepted bytes to the requested version.
- Demonstrate that an in-app submitter without source permission and a user
  outside the approved library receive no item information. Remove the
  synthetic grant and confirm subsequent imports fail. Verify writes and
  permission changes are absent from normal import.
- Keep import disabled if the proposed scope fails any required route. Leave
  current-version import disabled if exact binding cannot be established for
  that path. Direct upload remains available. End the pilot by revoking its
  test grants or retaining them only under the test tenant owner's approval.

Provider qualification is separate from local fixture results. Record each
failed or unavailable live criterion and the disabled capability it affects.
See [Teams custom app policy](https://learn.microsoft.com/en-us/microsoftteams/teams-custom-app-policies-and-settings)
and [Selected permissions](https://learn.microsoft.com/en-us/graph/permissions-selected-overview).

## Gate 3 - Full synthetic signing acceptance

Run VIK-01 through VIK-12 from the [reviewed spec](spec.md). The matrix requires
autograph-only completion, two distinct certified success cases, signing-time
identity and authority, required co-signatures, an explicit mode change with
fresh approvals, private disclosure controls and tampering rejection.

Use the existing Feature 001 verification commands for baseline regression.
Run the Feature 002 acceptance cases through the governed runner once their
modes are registered. Run independent acceptance on the published implementation
commit, record which connector capabilities were proven, and report any
unavailable gate without treating it as passing. Use synthetic identities,
mail delivery, operating agreements, resolutions and signing keys throughout.
