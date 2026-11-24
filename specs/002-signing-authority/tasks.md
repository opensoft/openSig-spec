# Implementation Tasks for Feature 002 Signing Authority

**Branch** `002-signing-authority`

**Planning baseline** `cd553f0baa43850c82ab0ed9528ef9919a05a037`

**Inputs** [Specification and Viktory scenarios](spec.md) · [Plan](plan.md) ·
[Research](research.md) · [Data model](data-model.md) ·
[Intake contract](contracts/intake.md) · [Certification contract](contracts/certification.md) ·
[Acceptance quickstart](quickstart.md)

Code paths are relative to openSig-code. Planning and acceptance receipts
named under `specs/002-signing-authority/` belong in openSig-spec. New code paths
below identify the intended implementation seams. Use the integrated code
head to allocate migration numbers and confirm the exact entry points. Keep
the existing Feature 001 runner modes intact and use the governed amendment
process for new mode registration.

## Phase 1 - Setup

- [ ] T001 Record the intended integrated Feature 001 code base SHA, current migration boundary, any overlap with US3 and the pinned runtime from `package.json` in `specs/002-signing-authority/receipts/implementation-baseline.md`. Keep Feature 001 worktrees and unpublished changes separate.
- [ ] T002 Record baseline typecheck and regression results, the Feature 002 test entry points, and the runner registration approach in `specs/002-signing-authority/receipts/implementation-baseline.md`. Use `scripts/verify.mjs` for baseline checks and record any required runner amendment. Depends on T001.

## Phase 2 - Shared Foundations

- [ ] T003 [P] Create reusable synthetic Viktory PDF, governing document versions, appointments, resolution variants, registry responses and signer identities in `tests/signing-authority/fixtures/viktory.ts`. Keep fixture signing and delivery isolated. Depends on T002.
- [ ] T004 [P] Define shared entity and evidence identifiers, review revisions, and sanitized pending or denial results in `src/domain/authority/types.ts` and `src/domain/authority/errors.ts`. Reuse the existing server error and audit conventions. Depends on T002.

## Phase 3 - US2 Corporate Records Intake and Review

**Goal** Give authorized staff one intake and review workflow. Prove direct
upload and review first. Then use the same pipeline for Teams and SharePoint.

### First increment - Direct upload and review

**Independent test** Use synthetic Viktory records through the standalone UI.
Verify entity scoping, exact retained bytes, readable receipt and status,
provenance review, request for more evidence, supersession, and recovery.

- [ ] T005 [P] [US2] Write direct intake contract tests in `tests/signing-authority/intake-contract.test.ts` covering tenant and entity isolation, human-only roles, bounded PDF/DOCX, retry conflicts, immutable receipts and stale review decisions. Depends on T003 and T004.
- [ ] T006 [P] [US2] Add Viktory browser and restart or restore scenarios for direct upload and review in `tests/acceptance/signing-authority/direct-intake.spec.mjs` and `tests/signing-authority/evidence-recovery.test.ts`. Cover the intake portions of VIK-02, VIK-06 and VIK-12. Depends on T003 and T004.
- [ ] T007 [US2] Add the entity and registry-observation schema in the next available migration, register it in `src/storage/schema/migrations.ts`, and persist standing, registration, source and timestamp in `src/storage/repositories/authority-entities.ts` and `src/domain/authority/registry.ts`. Wire the dated registry observation through the service boundary with a synthetic provider for QA. Depends on T005 and T006.
- [ ] T008 [US2] Add human submitter and reviewer capabilities with independent tenant and entity checks in `src/api/auth/authority.ts` and `src/api/auth/matrix.ts`. Protect private evidence reads and reject recipient sessions and Hermes or other service credentials. Depends on T005 and T006.
- [ ] T009 [US2] Add the evidence submission, review revision and provenance records in the next available migration, register it in `src/storage/schema/migrations.ts`, and implement the repository in `src/storage/repositories/authority-evidence.ts`. Preserve exact versions, historical decisions, and private blob references. Depends on T007.
- [ ] T010 [US2] Implement bounded upload staging, PDF/DOCX safety checks, digest calculation, atomic promotion, actor-scoped idempotency and sanitized audit in `src/domain/authority/intake.ts`. Use the private blob store. Failed, oversized or interrupted transfers must create no reviewable record. Depends on T008 and T009.
- [ ] T011 [US2] Implement reviewer corroboration, effective dates, requests for evidence, rejection and supersession with an expected review revision in `src/domain/authority/review.ts`. Require independently established provenance before marking a record verified. Depends on T008 and T009.
- [ ] T012 [US2] Protect committed corporate evidence through backup, restore, retention and orphan recovery in `src/storage/backup/backup.ts`, `src/storage/backup/restore.ts` and `src/storage/retention/service.ts`. Retain evidence referenced by historical signing decisions. Depends on T010 and T011.
- [ ] T013 [US2] Add scoped direct upload, status, review, and authorized private read routes in `src/api/authority/intake.ts` and `src/api/routes.ts`. Wire the corporate records screen in `src/authority-intake/index.html` and `src/authority-intake/main.ts` for the submitter and reviewer workflows. Depends on T010 and T011.
- [ ] T014 [US2] Run and independently verify direct upload, review, negative access and durable recovery on the same candidate. Record exact versions, bytes, observed statuses, and the covered parts of VIK-02, VIK-06 and VIK-12 in `specs/002-signing-authority/receipts/direct-intake.md`. Depends on T012 and T013.

### Second increment - Teams and SharePoint

**Independent test** Import the same Viktory synthetic records from one approved
library. Compare the review decision with direct upload and verify the exact
source version, denial behavior, and no writes to SharePoint. Test local
connector behavior separately from live Microsoft 365 qualification.

- [ ] T015 [P] [US2] Write delegated-adapter tests for approved-root browsing, denied metadata, grant revocation, changed selection, version races, bounded transfers, read-only provider calls and redirect token handling in `tests/signing-authority/sharepoint-contract.test.ts`. Depends on T014.
- [ ] T016 [P] [US2] Write a Teams and SharePoint browser scenario using a stub session and deterministic library in `tests/acceptance/signing-authority/sharepoint-intake.spec.mjs`. Cover VIK-03 origin parity, denied access and source change. Depends on T014.
- [ ] T017 [US2] Add the approved tenant, entity and site/list/drive/root binding and an off-by-default connector setting in `src/integrations/sharepoint/binding.ts` and `src/server/config.ts`. Keep grant and permission changes outside runtime credentials. Depends on T015 and T016.
- [ ] T018 [US2] Qualify any exact shipped Teams or Entra dependencies and record license, notices and SBOM results in `specs/002-signing-authority/receipts/connector-dependencies.md`. Preserve Feature 001's platform and shipped-dependency boundary. Depends on T014.
- [ ] T019 [US2] Add the personal tab manifest and shared UI entry in `src/integrations/teams/manifest.json` and `src/authority-intake/teams.ts`. Validate the Teams token, map to the configured operator role and exchange for delegated Graph server-side in `src/api/auth/teams.ts`. Qualify signing-key rotation and handle interactive consent without exposing Graph tokens. Depends on T017 and T018.
- [ ] T020 [US2] Implement selected-library metadata and content reads in `src/integrations/sharepoint/graph.ts` and `src/integrations/sharepoint/version.ts`. Use delegated `Lists.SelectedOperations.Selected` with an explicit read grant, enforce the configured root and user permission, keep download redirects transient, and avoid forwarding the Graph bearer to the download host. Require an exact immutable version or the separately qualified current-version read. Depends on T019.
- [ ] T021 [US2] Add restricted browse and selected-version import to `src/api/authority/sharepoint.ts` and `src/authority-intake/sharepoint.ts`. Send accepted bytes through the direct submission pipeline with their source receipt. Denied, stale, or unverifiable selections must disclose no restricted details and create no reviewable submission. Depends on T020.
- [ ] T022 [US2] Run the synthetic connector and browser tests, verify direct upload still works with the connector disabled, and document version and authorization results in `specs/002-signing-authority/receipts/sharepoint-synthetic.md`. Depends on T021.
- [ ] T023 [US2] With the approved test tenant and dedicated synthetic library, qualify Teams desktop/web SSO, the selected grant, required Graph operations, revocation and concurrent edits against known version bytes. Record per-capability evidence and grant cleanup in `specs/002-signing-authority/receipts/sharepoint-test-tenant.md`. Keep import disabled when the required scoped operations fail and current-version import disabled when exact version binding is unproven. Depends on T022 and test-tenant authorization.

**Checkpoint** T014 is the first deliverable and the prerequisite for any
Teams or SharePoint work. T022 verifies connector behavior against local
fixtures. T023 controls enablement in the test tenant and is recorded
separately from synthetic results.

## Phase 4 - US1 Autograph-Only Signing

**Goal** Preserve ordinary signing and label completed autograph-only results
consistently, including legacy requests.

**Independent test** Complete and verify the mock Viktory consent with no
governance records. Verify legacy package compatibility and zero authority
badges even when the organization seal passes.

- [ ] T024 [US1] Add the VIK-01 legacy and new autograph-only cases in `tests/acceptance/signing-authority/autograph-only.spec.mjs` and `tests/signing-authority/legacy-package.test.ts`. Test received artifacts and verification views. Depends on T022.
- [ ] T025 [US1] Add the backward-compatible mode default and revision-bound mode in `src/domain/envelopes/service.ts` and `src/domain/envelopes/manifest.ts`. Continue to admit ordinary signing without corporate evidence. Depends on T024.
- [ ] T026 [US1] Show the selected mode and **Autographed** outcome through `src/domain/envelopes/views.ts`, `src/autograph/main.ts` and `src/verifier/report.ts`. Keep the existing organization seal and legacy package behavior. Depends on T025.
- [ ] T027 [US1] Run VIK-01 and legacy signing and package regression on the candidate. Record compatibility and absent authority badges in `specs/002-signing-authority/receipts/autograph-only.md`. Depends on T026.

## Phase 5 - US3 Fully Certified Signing

**Goal** Certify an exact revision only after valid entity and transaction
authority, identity at signing, and every required signature are verified.

**Independent test** Use standing-authority and resolution-based versions of
the mock Viktory consent. Verify both earn the badge only after the checks
pass. Repeat with missing or conflicting evidence, wrong or unauthorized
signer, missing co-signature, mode change, and modified package bytes.

- [ ] T028 [P] [US3] Add the certification decision and publication contract tests for VIK-04 through VIK-09 and VIK-11 in `tests/signing-authority/certification-contract.test.ts`. Cover both positive authority sources and negative cases for scope, adoption, standing, revocation, proof timing, co-signers, and package binding. Depends on T027.
- [ ] T029 [P] [US3] Add the browser consent, mode-change, privacy and tampering acceptance checks in `tests/acceptance/signing-authority/certified-signing.spec.mjs`. Include the signing part of VIK-02 and VIK-10 through VIK-12. Depends on T027.
- [ ] T030 [US3] Implement revision-scoped authority decisions, standing authority, reviewable resolutions and corroborated appointments in `src/domain/authority/decision.ts` and `src/storage/repositories/authority-decisions.ts`. Enforce governing limits, competent-body adoption, effective dates, and required co-signers. Depends on T028 and T029.
- [ ] T031 [US3] Implement explicit certification opt-in and mode or revision replacement in `src/api/envelopes/handlers.ts` and `src/domain/envelopes/send.ts`. Preserve earlier history, retire obsolete invitations and require fresh human consent. Depends on T030.
- [ ] T032 [US3] Verify the recipient's identity and authority when they sign in `src/api/recipients/approval.ts` and `src/domain/authority/signing-proof.ts`. Bind explicit intent to capacity and exact revision, and exclude operator confirmation, Teams identity and service credentials as signer proof. Depends on T030 and T031.
- [ ] T033 [US3] Gate certified completion and publication on the exact decision, registry and governing evidence, and all qualified signatures in `src/domain/finalization/verify.ts` and `src/domain/finalization/publish.ts`. Fence stale attempts and expose a safe pending reason. Depends on T032.
- [ ] T034 [US3] Bind the recipient-safe authority summary to a versioned sealed evidence package in `src/domain/finalization/manifest.ts`, `src/artifacts/package.ts` and `src/verifier/report.ts`. Protect private attachments and identity evidence, reject altered packages and preserve the legacy package reader. Depends on T033.
- [ ] T035 [US3] Show pending certification and the readable **Signing authority verified** blue checkmark through `src/domain/envelopes/views.ts` and `src/autograph/main.ts`. Require verified evidence on the exact completed revision before displaying the badge. Depends on T034.

## Phase 6 - Regression and Independent Acceptance

- [ ] T036 Run the exact candidate through typecheck, the Feature 001 regression and the governed Feature 002 modes or approved test entry points. Record runner coverage and any unavailable prerequisite in `specs/002-signing-authority/receipts/regression.md`. Depends on T035 and a recorded T023 outcome, including an explicitly unavailable live gate. Connector capability remains disabled wherever the live gate is unavailable.
- [ ] T037 Run independent VIK-01 through VIK-12 acceptance on the published implementation SHA and record artifact evidence, public/private disclosure, mode-specific status and exact connector capabilities in `specs/002-signing-authority/receipts/independent-acceptance.md`. Follow `quickstart.md` for synthetic and test-tenant boundaries. Depends on T036.

## Dependencies and Execution Order

```text
T001-T004 → T005-T014 Direct upload and review → T015-T022 Teams and SharePoint
                                                    ├→ T023 Test-tenant qualification
                                                    └→ T024-T027 Autograph-only → T028-T035 Certified signing
T035 and T023 outcome recorded → T036 Regression → T037 Independent acceptance
```

Synthetic signing can continue when the authorized test tenant is unavailable.
Record the blocker and leave unqualified connector capabilities disabled.
No feature release can claim live SharePoint or Teams acceptance from the
synthetic run. Use the first [Viktory acceptance cases](spec.md#first-qa-case---mock-viktory-registered-agent-consent)
for evidence and keep production signing, production mail and filings inactive.

### Parallel work after dependencies pass

- Foundation can split the synthetic fixtures (T003) and shared types (T004).
- US2 can split service and browser/durability tests (T005 and T006). After
  T014 passes, split the connector contract tests (T015) from the Teams-host
  browser scenario (T016).
- US1 keeps the legacy package and autograph UI changes together because they
  share mode and verification semantics. The VIK-01 browser and package
  assertions under T024 can be authored independently in separate test files.
- US3 can split the decision and publication contract tests (T028) from the
  browser ceremony tests (T029). Integrate the shared mode and verification
  changes in dependency order.

## Requirement Coverage

| Requirements | Implementation and acceptance tasks |
| --- | --- |
| FR-001, FR-002 | T024-T027, T031 |
| FR-003 | T007, T014, T028, T033 |
| FR-004, FR-005 | T013-T023 |
| FR-006, FR-007 | T009-T014, T021-T023 |
| FR-008, FR-009 | T028, T030, T033 |
| FR-010, FR-011 | T028-T029, T032-T035 |
| FR-012 | T029, T031 |
| FR-013, FR-014 | T024-T027, T028-T029, T033-T035 |
| FR-015 | T003, T014, T022-T023, T037 |

## Implementation Strategy

The first independently testable increment is **direct upload and review**.
Complete T001 through T014 and record the receipt. Then implement the Teams
and SharePoint adapter, starting with synthetic tests and graduating to
an authorized test tenant. Keep autograph-only as the ordinary completion
path, then add opt-in certification and the full synthetic signing matrix.
Use the published implementation commit for independent acceptance.
