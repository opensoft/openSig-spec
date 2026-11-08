# Tasks: 001 Corporate Signing MVP

Status: planned; all implementation tasks intentionally unchecked. This is the only executable build checklist. OpenSpec tasks contain governance/handoff only. Paths in files are relative to the paired code worktree. Commands are planned and become available through T001; missing/skipped checks must fail, not count as success. Tier tags follow the shared rubric; no H tasks qualify in a new security-sensitive project.

## Setup

- [ ] T001 [O] Pin Node 24.22.2 project-locally (verified tarball under gitignored `tools/`), configure TypeScript type checking (typescript 6.0.3, @types/node 24.19.2), and build the frozen fail-closed verification runner with the full mode registry and self-tests exactly as plan.md § Technical Context and § Verification runner contract specify; extend `.gitignore` as planned; replace the code-leg AGENTS.md note "No signing runtime/test commands exist yet" with the real commands and the freeze rule — files: `.gitignore`, `.node-version`, `.npmrc`, `package.json`, `package-lock.json`, `tsconfig.json`, `AGENTS.md`, `scripts/fetch-node.sh`, `scripts/verify.mjs`, `scripts/verify-modes.mjs`, `tests/tooling/verification-runner.test.mjs` — verify: `sh -c 'set -e; sh scripts/fetch-node.sh; PATH="$PWD/tools/node/bin:$PATH"; export PATH; npm ci --no-audit --no-fund; node --test tests/tooling/verification-runner.test.mjs; rc=0; npm run --silent verify -- __no_such_mode__ >/dev/null 2>&1 || rc=$?; test "$rc" -eq 2; npm run --silent verify -- tooling'`
- [ ] T002 [O] Qualify PDF/signature/auth/storage dependencies and distribution inventory; block design on disallowed licenses or failed independent verification — files: `scripts/qualification.mjs`, `tests/qualification/`, `docs/qualification.md`, `package.json`, `package-lock.json` — verify: `npm run verify -- qualification`

## Foundation

- [ ] T003 [O] Implement tenant-bound persistence, migrations, immutable blobs and transactional outbox with restore checks — files: `src/storage/`, `tests/storage/`, `scripts/verify.mjs` — verify: `npm run verify -- storage`
- [ ] T004 [O] Create bounded local development composition and synthetic fixtures with no real secrets — files: `compose.yaml`, `fixtures/synthetic/`, `scripts/dev.mjs`, `tests/tooling/dev.test.mjs`, `README.md` — verify: `npm run verify -- dev`
- [ ] T005 [O] Implement operator OIDC issuer/audience/tenant-role validation and isolate service authority — files: `src/api/auth/`, `tests/auth/`, `scripts/verify.mjs` — verify: `npm run verify -- auth`

## US1

- [ ] T006 [US1] [O] Create envelope/version/upload-limit and unsupported-PDF fixture contracts with a self-checking test harness — files: `tests/contract/envelopes.test.mjs`, `fixtures/synthetic/` — verify: `npm run verify -- envelope-contract`
- [ ] T007 [US1] [O] Implement envelope upload/field validation/freeze/state and idempotent operators — files: `src/domain/`, `src/api/envelopes/`, `tests/domain/`, `scripts/verify.mjs` — verify: `npm run verify -- envelopes`
- [ ] T008 [US1] [O] Implement invitation/challenge/session binding, TTL/attempt limits, CSRF and safe link viewing — files: `src/api/recipients/`, `src/api/auth/recipient-session.ts`, `tests/recipients/`, `scripts/verify.mjs` — verify: `npm run verify -- recipients`
- [ ] T009 [US1] [O] Implement review and drawn/typed signature/date UI with exact-version consent and accessible navigation — files: `src/autograph/`, `tests/browser/review.test.mjs`, `scripts/verify.mjs` — verify: `npm run verify -- autograph`
- [ ] T010 [US1] [O] Implement sequential approval digests, decline/expiry/void and atomic finalization reservation — files: `src/domain/`, `src/api/recipients/`, `tests/domain/approval.test.mjs`, `tests/integration/races.test.mjs` — verify: `npm run verify -- approval`
- [ ] T011 [US1] [S] Demonstrate US1 synthetic two-recipient flow and record keyboard/mobile observations — files: `tests/browser/approval.test.mjs`, `docs/evidence/us1.md`, `scripts/verify.mjs` — verify: `npm run verify -- us1`

## US2

- [ ] T012 [US2] [O] Build independently checkable tamper/trust/revision fixtures and verifier harness before engine integration — files: `tests/integrity/`, `fixtures/synthetic/`, `scripts/verify.mjs` — verify: `npm run verify -- integrity-contract`
- [ ] T013 [US2] [O] Implement isolated composition/sealing worker and key-adapter boundary using qualified engine — files: `src/cryptograph/`, `tests/cryptograph/`, `scripts/verify.mjs` — verify: `npm run verify -- cryptograph`
- [ ] T014 [US2] [O] Implement granular independent verification and verified durable package completion — files: `src/cryptograph/`, `src/storage/`, `src/domain/`, `tests/integrity/`, `scripts/verify.mjs` — verify: `npm run verify -- verification`
- [ ] T015 [US2] [O] Implement bounded retry/crash recovery, unique artifact publication and concurrent void/finalize behavior — files: `src/domain/`, `src/storage/`, `tests/recovery/`, `scripts/verify.mjs` — verify: `npm run verify -- recovery`
- [ ] T016 [US2] [O] Implement authorized package download, sanitized receipts and redaction/access audit — files: `src/api/artifacts/`, `src/storage/`, `tests/artifacts/`, `scripts/verify.mjs` — verify: `npm run verify -- artifacts`
- [ ] T017 [US2] [S] Record ten-flow external-verifier and performance evidence with untrusted certificate labeling — files: `tests/acceptance/sealed-package.test.mjs`, `docs/evidence/us2.md`, `scripts/verify.mjs` — verify: `npm run verify -- us2`

## US3

- [ ] T018 [US3] [O] Implement scoped Hermes prepare/request/status/retrieve tool adapter with authorization preview — files: `src/integrations/hermes/`, `tests/integrations/hermes.test.mjs`, `scripts/verify.mjs` — verify: `npm run verify -- hermes-contract`
- [ ] T019 [US3] [O] Implement outbox delivery with signed replay-safe callbacks and SSRF-safe registration — files: `src/integrations/webhooks/`, `src/storage/`, `tests/webhooks/`, `scripts/verify.mjs` — verify: `npm run verify -- webhooks`
- [ ] T020 [US3] [O] Implement synthetic source-version healthcare consumers without clinical/financial mutation authority — files: `src/integrations/healthcare/`, `tests/integrations/healthcare.test.mjs`, `scripts/verify.mjs` — verify: `npm run verify -- healthcare-contracts`
- [ ] T021 [US3] [O] Implement disabled/fake anchoring adapters and prove no real network/wallet activity — files: `src/anchoring/`, `tests/anchoring/`, `scripts/verify.mjs` — verify: `npm run verify -- anchoring-disabled`
- [ ] T022 [US3] [O] Demonstrate actual authorized Hermes tool round trip with human approvals and healthcare harnesses — files: `tests/acceptance/consumers.test.mjs`, `docs/evidence/us3.md`, `scripts/verify.mjs` — verify: `npm run verify -- us3`

## Release

- [ ] T023 [O] Wire build, contract, integrity, security, recovery and license checks into CI without fake pass modes — files: `.github/workflows/validate.yml`, `scripts/release-evidence.mjs`, `tests/tooling/release-gates.test.mjs`, `scripts/verify.mjs` — verify: `npm run verify -- release-gates`
- [ ] T024 [O] Document managed key, retention, backup/restore, threat model and rollback pilot prerequisites; no deployment — files: `docs/operations.md`, `docs/threat-model.md`, `tests/operations/`, `scripts/verify.mjs` — verify: `npm run verify -- operations`
- [ ] T025 [O] Execute full synthetic acceptance and emit source-bound release receipt; leave unavailable runtime checks failing — files: `tests/acceptance/`, `docs/evidence/acceptance.json`, `scripts/verify.mjs` — verify: `npm run verify -- acceptance`

## Dependencies and independent acceptance

T001 -> T002 -> T003..T005 -> US1 T006..T011 -> US2 T012..T017 -> US3 T018..T022 -> release T023..T025. T006 and T012 qualify fixture/harness behavior independently; they do not claim the missing API or signing engine works. Full handler/engine acceptance remains in T007 and T013..T017. A passing fixture self-check cannot satisfy runtime acceptance. US1 is independently demonstrable human approval but must not claim sealed completion; the releasable signing MVP requires US1 and US2. Feature acceptance also requires US3 and cross-cutting gates.

After T005 and stable contracts, independent work may prepare US1 browser fixtures and US2 tamper fixtures. After T017, Hermes adapter, healthcare harness and disabled anchoring work can proceed in separate files, but do not concurrently edit the shared verifier dispatcher. No P markers authorize overlapping writes. The default execution is sequential.

## Traceability

FR-001: T003,T005,T007,T008,T016; FR-002: T002,T006,T007; FR-003: T007,T009,T010; FR-004: T005,T008,T010; FR-005: T012,T013,T014,T016; FR-006: T012,T014,T017; FR-007: T003,T007,T010,T015,T019; FR-008: T016,T024; FR-009: T018,T019,T020,T022; FR-010: T021,T022; FR-011: T001,T002,T023; FR-012: T011,T012,T015,T017,T022,T024,T025.

SC-001: T017; SC-002: T006,T008,T012,T014; SC-003: T015; SC-004: T009,T011; SC-005: T017,T025; SC-006: T022; SC-007: T002,T023,T025.

## Landing obligation

After implementation, reconcile these spec artifacts against tested behavior, obtain leg reviews and land matching assembly pins. Do not archive from documentation completion. Any accepted bounded amendment updates the working spec/plan/tasks immediately and uses the shared reconciliation issue protocol. Wider identity, licensing, live ledger or healthcare deployment changes require full governance first.
