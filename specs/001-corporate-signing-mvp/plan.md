# Implementation Plan: Corporate Signing MVP

**Branch**: `001-corporate-signing-mvp` | **Date**: 2026-11-08 (architect amendments A-001..A-004 the same day) | **Spec**: [spec.md](spec.md)

## Summary

Deliver one corporate signing flow with openAutograph human approval and an openCryptograph organizational PDF seal; then expose bounded Hermes tools and synthetic openChart/openPractice contracts. No production deployment or live anchoring is included. [Tasks](tasks.md) is the sole executable checklist.

## Technical Context

**Runtime (amendment A-001).** Node.js **24.22.2** LTS ("Krypton", released 2026-10-27), linux-x64 only. This replaces the earlier "Node 22" target: Node 22 reaches end of life on 2027-04-30 and its `node:sqlite` prints an ExperimentalWarning, while 24.x loads `node:sqlite` without a warning and has stable type stripping. 24.22.2 is the newest 24.x and follows the 2026-10-20 security release 24.22.1. The host binary `/usr/bin/node` is v24.21.0, which predates that security release. It is never the pinned runtime, and nothing is installed or downgraded globally.

- **Delivery.** `scripts/fetch-node.sh` (POSIX sh, idempotent, fail-closed) downloads `node-v24.22.2-linux-x64.tar.xz` and the clearsigned `SHASUMS256.txt.asc` from `https://nodejs.org/dist/v24.22.2/`. It then:
  1. verifies the signature with `gpg` in an ephemeral `GNUPGHOME` against the Node release-key fingerprints, which are embedded in the script and taken from the Node v24.22.2 README;
  2. requires the tarball's SHA-256 to equal both the signed SHASUMS line and the embedded constant `48cc0e21e048790e54c0cf5749c2725005313d652651c40b7a21feb9697aaf75`;
  3. extracts into the gitignored `tools/node-v24.22.2-linux-x64/`, points `tools/node` at it and writes `tools/node.receipt.json` (version, tarball digest, signer fingerprint, time).

  A missing `curl`, `gpg`, `sha256sum`, `tar` or `xz`, or any mismatch, exits non-zero before extraction. On a second run the script re-checks the receipt and `tools/node/bin/node --version`, and downloads nothing.
- **Measured on 2026-11-08.** `gpg --verify SHASUMS256.txt.asc` gave `Good signature from "Antoine du Hamel"` using key `5BE8A3F6C8A5C01D106C0AD820B1A390B168D356`, which appears in the Node v24.22.2 README release-key list. The `node-v24.22.2-linux-x64.tar.xz` line in the signed file is `48cc0e21…aaf75`.
- **Pins.** `.node-version` contains `24.22.2`. `package.json` sets `engines.node` to `24.22.2`. `.npmrc` sets `engine-strict=true`, `save-exact=true` and `ignore-scripts=true`. Installs use the npm bundled in the pinned tarball with `PATH="$PWD/tools/node/bin:$PATH"`. That npm's shebang is `env node`, so it must find the pinned binary first. CI (T023) runs the same `scripts/fetch-node.sh`; `actions/setup-node` is not used.
- **`.gitignore` (T001).** Adds `node_modules/`, `tools/`, `dist/`, `.verify-out/`, `var/`, `spool/`, `*.sqlite*`, `*.db`, `*.pem`, `*.key`, `*.p12`, `*.pfx` and `.env*`, and keeps the existing entries.

**Language and type checking.**
- TypeScript sources (`.ts`) run directly under Node 24 type stripping. Only erasable syntax is allowed: no `enum`, `namespace` or parameter properties.
- Tests are `.test.mjs` files that import `.ts` sources.
- Type checking uses the `typescript` package, pinned at **6.0.3**: Apache-2.0, no dependencies, measured with `npm install --ignore-scripts`. The command is `npm run typecheck`, which runs `tools/node/bin/node node_modules/typescript/bin/tsc --noEmit -p tsconfig.json`. Never use `npx tsc`: it resolves an unrelated `tsc` package.
- TypeScript 7.x is not used. It depends on native per-platform binary packages whose embedded contents were not measured.
- `@types/node` is pinned at **24.19.2** (MIT), with `undici-types` 7.24.6 (MIT); both measured.
- `tsconfig.json` sets: `target`/`lib` `es2024`, `module`/`moduleResolution` `nodenext`, `strict`, `noUncheckedIndexedAccess`, `noEmit`, `allowImportingTsExtensions`, `erasableSyntaxOnly`, `verbatimModuleSyntax`, `types: ["node"]`, `allowJs: true`, `checkJs: false`, and includes `src/`, `tests/` and `scripts/`. Including the scripts keeps the input set non-empty before any `.ts` source exists.
- Browser code gets its own build (T035). Dev-only tools contribute no bytes to any distributable.

**Testing.** `node:test` runs only through the frozen runner `scripts/verify.mjs` (contract below). Raw `node --test` is not an acceptance signal. Measured on v24.21.0, it exits 0 for a glob that matches nothing, an empty file, a skip-only file and a failing-todo file, and it does not search directory arguments. `npm test` is `verify -- all-unit`.

**How verify commands run on the pinned runtime.** Every task's verify is `npm run verify -- <mode>`. The orchestrator runs it from the code tree with `PATH="$PWD/tools/node/bin:$PATH"`, after `sh scripts/fetch-node.sh` and `npm ci`. The package script is `tools/node/bin/node scripts/verify.mjs`, so whichever npm launches it, the pinned binary runs the gate. The runner returns ENV_MISMATCH when `process.version` differs from `v` + `.node-version`, when the platform is not linux-x64, or when the installed `node_modules/.package-lock.json` disagrees with `package-lock.json`. Measured: npm 11.19.0 propagates a script's exit status unchanged (exit 2 and exit 4 came back as 2 and 4). Orchestrators still treat any non-zero exit as failure and read the runner's final status line.

**Dependencies (amendment A-002).** The PDF/CMS engine is original Apache-2.0 code on `node:crypto` and `node:zlib`, so the **shipped npm closure is empty**.
- **Measured and rejected:** pdf-lib, `@signpdf/*` (node-forge) and @libpdf/core 0.5.3, whose closure includes pako `(MIT AND Zlib)`, pkijs/asn1js/bytestreamjs BSD-3-Clause, lru-cache BlueOak-1.0.0 and tslib 0BSD.
- **Deferred:** pdfjs-dist 6.5.438. Its bundled fonts, cmaps and wasm carry BSD, OpenJPEG and Liberation licenses, and its worker embeds glue modules whose provenance is unproven.
- **Review rendering:** the browser's built-in PDF viewer, plus an original page-outline schematic and a link that downloads the exact frozen bytes.
- **Signature capture:** original pointer-to-vector-stroke code.
- **OIDC:** original JWT/JWKS validation.
- **Packaging:** an original store-method ZIP writer.
- **Dev-only packages:** typescript, `@types/node` and playwright are `non-shipped`.
- **Where packages land:** every third-party package lands, with exact pins, only in a task whose `files:` lists `package.json`/`package-lock.json`. That is T001 for typescript and `@types/node`, and T027 for playwright.
- **Evidence:** research.md § Engine decision.

**Storage.** Built-in `node:sqlite` (no npm driver, no native addon), single instance, with `BEGIN IMMEDIATE` transactions, a busy timeout and a transactional outbox. Blobs are immutable, tenant-namespaced and content-addressed on the filesystem behind an object-store interface. Development keys are ephemeral self-signed fixtures generated at test runtime outside the tree. Production managed key access is a separate rollout gate.

**Target.** A local/staged Linux service and browser client, not a provisioned AKS deployment. The UI supports keyboard and mobile and loads no CDN or external font.

**Scale.** One 10 MiB, 50-page PDF; one or two sequential recipients; ten concurrent envelopes. Status/review navigation p95 ≤ 2 s and finalization max ≤ 60 s for n=10 on declared hardware, excluding user and email latency. Finalization jobs keep bounded exponential retries and visible failure, never silent completion.

**Evidence.** Versioned evidence records the tree digest, the lockfile/SBOM digest, the environment and artifact digests. No build command is claimed available until its implementation task creates it.

## Pending owner decision: platform license scope

**Status: POLICY-PENDING.** Not resolved or reinterpreted here.

FR-011 says: "fail release on unknown/disallowed third-party licenses, including transitive assets and release-image inventory". That wording is kept unchanged. The open question is whether pre-existing platforms (OS, Node runtime, JVM, browser) are inventoried separately or fall under the literal MIT/Apache allowlist. The conflict, from the 2026-11-08 feasibility review (section E):

- **Inside the Node binary:** V8 (BSD-3-Clause); ICU (Unicode-3.0 plus older ICU/BSD texts); zlib (Zlib); zstd (BSD); ittapi and inspector_protocol (BSD); SipHash (CC0); HdrHistogram (BSD-2/CC0); minimatch (BlueOak-1.0.0); rimraf and node-heapdump (ISC).
- **In the binary but not covered by its `LICENSE`:** SQLite (public-domain blessing), which this plan uses as `node:sqlite`, and 116 root CA certificates (provenance unverified).
- **Elsewhere in the tarball:** npm (Artistic-2.0), and build-time notices (GYP, jinja2, markupsafe, cpplint, gtest; BSD).
- **Dynamically linked:** glibc (LGPL-2.1), and libstdc++/libgcc_s (GPL-3+ with the GCC Runtime Library Exception).
- **A typical base image:** ca-certificates (MPL-2.0/GPL-2+) and tzdata (public domain).
- **Test-time tools:** the Playwright Chromium build (BSD and others) and the OpenSSL CLI (Apache-2.0), plus the isolated Hermes runtime. These are not shipped, but they are named in the platform inventory too.

**Handling.**

- **Application gate (strict, must pass).** The application-dependency gate covers the shipped npm closure plus every bundled asset copied into a distributable. It is strict and must actually pass for everything the code ships (T002/T027).
- **Platform inventory.** A separate inventory records the pinned Node runtime, linked system libraries, browser and CLI tools with status `POLICY-PENDING`.
- **Release gate.** The release gate (`acceptance` mode, SC-007's release-image inventory, checklists/security-release.md item 1) stays failing/OPEN until the owner decides. No waiver is requested, and no task may mark it passed.
- **Dev/test tools.** npm `devDependencies` are inventoried with full license data, labeled `non-shipped`, and the release build proves they contribute no bytes to the distributable. TypeScript 6.0.3 itself carries third-party notices (DefinitelyTyped MIT, Unicode, W3C, CC-BY-4.0 in its `lib` files; measured in `ThirdPartyNoticeText.txt`). It is a build tool, not an application dependency; this is recorded for the owner, not waived.

## Verification runner contract (frozen by T001)

T001 writes `scripts/verify.mjs` (engine and CLI), `scripts/verify-modes.mjs` (registry) and `tests/tooling/verification-runner.test.mjs` (self-tests). After T001's commit these three files are **frozen**. No later task lists them, and before accepting any later task the orchestrator runs:

`git diff --quiet <T001-sha> HEAD -- scripts/verify.mjs scripts/verify-modes.mjs tests/tooling/verification-runner.test.mjs`

Only a recorded architect amendment can add a mode. It does so through a new `[O]` task that lists these files, re-runs the self-tests and re-freezes at that task's commit.

### Statuses

Each run prints exactly one final stdout line, `verify: <mode> <STATUS> exit=<n> files=<n> tests=<n> pass=<n> fail=<n> skip=<n> todo=<n> cases=<found>/<required>`, and writes `.verify-out/<mode>/result.json`, which is gitignored.

| Exit | Status | Meaning |
|---|---|---|
| 0 | PASS | Every listed file ran, the test count is at least `minTests`, every required case ID passed, there were no skip/todo/only, and the evidence is fresh |
| 1 | FAIL | Any failing test, cancellation, timeout, file-load or syntax error, typecheck error, or a known mock identity in acceptance evidence |
| 2 | USAGE | No mode, an unknown mode, an unknown flag, or any attempt to relax rules (for example `--allow-skip`). No relax flag or environment variable exists |
| 3 | CAPABILITY_ABSENT | A declared capability probe failed. No test runs. The orchestrator records "environment-blocked", never a pass and never a writer failure |
| 4 | VACUOUS | Zero files, zero tests, any skip, todo or `only`, a count below `minTests`, or a missing required case ID |
| 5 | EVIDENCE_MISSING | Declared evidence absent, not written by this run, missing a required label, or stale (tree digest mismatch) |
| 6 | ENV_MISMATCH | Runtime version or platform mismatch, installed tree differs from the lockfile, vendored contract missing or digest mismatch, or test-filter flags in `NODE_OPTIONS` |
| 7 | RUNNER_ERROR | The runner's built-in sentinel controls failed, or an internal error occurred |

### Registry fields (`scripts/verify-modes.mjs`)

Each mode is `{ task, kind, files, minTests, requiredCases, capabilities, evidence, typecheck, contract, components }`, where:

- `kind` is one of `tooling | unit | harness | contract | acceptance | aggregate | release`;
- `files` lists explicit globs that the runner expands itself. The runner never passes a directory or glob to `node --test`;
- `capabilities` takes probe names from: `openssl` (OpenSSL ≥ 3.0 with `cms`), `chromium` (the browser revision matching the pinned Playwright), `hermes-runtime`, `claude-sdk-transport`, `external-pdf-reader`, `declared-hardware` and `ci-run`. `openssl` and `chromium` probes are built in. Any other probe is delegated to `scripts/capabilities/<name>.mjs`, which exits 0 when the capability is present; an absent or failing probe file means CAPABILITY_ABSENT;
- `evidence` lists required evidence files and required labels;
- `typecheck` is a boolean;
- `contract: true` requires `contracts/http-api.json` plus `contracts/http-api.source.json` ({repo, commit, path, sha256}) with a matching digest;
- `components` lists the member modes of an aggregate.

### Runner rules

- **Sentinels.** Before any mode, the runner runs two built-in sentinels in a temp directory: one passing test must count 1, and one skip-only file must yield VACUOUS. If either misbehaves, the runner returns RUNNER_ERROR.
- **Typecheck.** When `typecheck` is set, the runner runs `npm run typecheck` first.
- **Execution.** Tests run through `node:test` `run()` with process isolation and an explicit file list. The runner counts from the event stream, never retries, strips test-filter flags, and enforces a per-file timeout.
- **Case IDs** are the leading `[ID]` of a passing test name.
- **Tree digest.** SHA-256 over sorted `path\0sha256(content)\n` lines for `git ls-files --cached --others --exclude-standard` within `src/`, `tests/`, `scripts/`, `fixtures/`, `contracts/`, `package.json`, `package-lock.json`, `tsconfig*.json` and `.node-version`. It excludes `docs/evidence/`.
- **Evidence** must be written during the run under `.verify-out/<mode>/` and carry the current tree digest and required labels. A label value naming a known mock (`hermes-stub`, `fake-verifier`, `jsdom`, `mock-browser`) fails acceptance modes.
- **Aggregates.**
  - Aggregates run their components in order.
  - A `harness` or `contract` component never counts as success-criterion evidence.
  - `acceptance-local` writes the receipt and requires the committed `docs/evidence/acceptance.json` to carry the same tree digest. It returns CAPABILITY_ABSENT when a local capability (`openssl`, `chromium`, `hermes-runtime`) is absent. It always lists the external gates as `OPEN` with `release_ready: false`.
  - `acceptance` is the release gate. It returns FAIL while any gate is OPEN or POLICY-PENDING, and it is no task's verify.
- **Registry completeness.** The runner and its self-test also check that every `tests/**/*.test.mjs` is claimed by exactly one mode, and that the registry's mode names equal the list below exactly.

### Self-tests (`tests/tooling/verification-runner.test.mjs`)

Fixture suites are generated in a temp directory at run time. Engine behavior is tested by importing the engine with synthetic registries, so the CLI needs no override path. An `after()` hook asserts that the executed test count equals a declared constant. The cases:

1. no mode → 2
2. unknown mode → 2
3. `--allow-skip` → 2
4. zero files → 4
5. empty file → 4
6. `skip` option → 4
7. runtime `t.skip()` → 4
8. passing todo and failing todo → 4
9. `only` → 4
10. failing test → 1
11. syntax/import error → 1
12. timeout → 1
13. below `minTests` → 4
14. missing required case → 4
15. absent capability → 3 with zero tests executed
16. missing evidence → 5, and stale tree digest → 5
17. runtime mismatch → 6
18. a harness component cannot satisfy a success-criterion gate
19. positive control → 0, with counts recorded
20. registry names equal the frozen list, and every test file is claimed exactly once
21. test-filter flags in `NODE_OPTIONS` → 6
22. the `after()` count guard
23. mock-identity label → 1
24. vendored contract digest mismatch → 6
25. aggregate stops at the first non-PASS and reports it
26. sentinel failure → 7

### Mode registry (43 modes; every mode named in tasks.md, pre-registered by T001)

Before a mode's own task lands, its file globs match nothing, so the mode returns VACUOUS. That is the correct result. `us3-real-model` is a supplementary, separately labeled attempt. It belongs to no aggregate, and its CAPABILITY_ABSENT never blocks `us3`; the scripted-model `us3` run stays the recorded SC-006 evidence.

| Mode | Task | Kind | Test files | minTests | Required case IDs | Capabilities | Evidence |
|---|---|---|---|---|---|---|---|
| `tooling` | T001 | tooling | `tests/tooling/verification-runner.test.mjs` | 26 | — | — | — |
| `no-secrets` | T026 | unit | `tests/no-secrets/**/*.test.mjs` | 8 | NS-01..NS-07 | — | — |
| `license-inventory` | T002 | unit | `tests/license-inventory/**/*.test.mjs` | 14 | LI-01..LI-10 | — | — |
| `qualification` | T027 | unit | `tests/qualification/**/*.test.mjs` | 6 | QL-01..QL-05 | — | `.verify-out/qualification/inventory.json` |
| `storage` | T003 | unit | `tests/storage/**/*.test.mjs` | 12 | — | — | — |
| `blobs` | T028 | unit | `tests/blobs/**/*.test.mjs` | 8 | BL-01..BL-04 | — | — |
| `audit-chain` | T029 | unit | `tests/audit-chain/**/*.test.mjs` | 8 | AC-01..AC-05 | — | — |
| `outbox-restore` | T030 | unit | `tests/outbox-restore/**/*.test.mjs` | 8 | OR-01..OR-04 | — | — |
| `dev` | T004 | unit | `tests/dev/**/*.test.mjs` | 5 | DV-01..DV-03 | — | — |
| `auth` | T005 | unit | `tests/auth/**/*.test.mjs` | 14 | AU-01..AU-10 | — | — |
| `server` | T031 | unit | `tests/server/**/*.test.mjs` | 12 | SEC-01..SEC-06 | — | — |
| `notifications` | T032 | unit | `tests/notifications/**/*.test.mjs` | 8 | NT-01..NT-05 | — | — |
| `parser-limits` | T033 | unit | `tests/pdf-parse/**/*.test.mjs` | 16 | PL-01..PL-12 | — | — |
| `envelope-contract` | T006 | harness | `tests/contract/envelopes/**/*.test.mjs` | 12 | — | — | — (contract: true) |
| `envelopes` | T007 | unit | `tests/domain/envelopes/**/*.test.mjs` | 16 | ST-01..ST-08 | — | — |
| `envelope-api` | T034 | unit | `tests/api/envelopes/**/*.test.mjs` | 16 | SC002-07, SC002-08, SC002-23, SC002-24, SC002-26 | — | — (contract: true) |
| `recipients` | T008 | unit | `tests/recipients/**/*.test.mjs` | 18 | SC002-10..SC002-16, SC002-25, CH-01..CH-03 | — | — (contract: true) |
| `approval` | T010 | unit | `tests/approval/**/*.test.mjs` | 16 | SC002-01..SC002-06, SC002-17, SC002-18 | — | — (contract: true) |
| `ui-build` | T035 | unit | `tests/ui-build/**/*.test.mjs` | 6 | UB-01..UB-04 | — | — |
| `autograph` | T009 | unit | `tests/browser/autograph/**/*.test.mjs` | 10 | AG-01..AG-06 | chromium | — |
| `us1` | T011 | acceptance | `tests/acceptance/us1/**/*.test.mjs` | 6 | SC004-01..SC004-04, US1-01, US1-02 | chromium | `.verify-out/us1/evidence.json` labels: browser_engine, browser_version, tree_digest |
| `integrity-contract` | T012 | harness | `tests/integrity/oracle/**/*.test.mjs` | 12 | OC-01..OC-08 | openssl | — |
| `cms` | T013 | unit | `tests/cms/**/*.test.mjs` | 10 | KEY-01, KEY-02 | openssl | — |
| `compose` | T036 | unit | `tests/compose/**/*.test.mjs` | 10 | CP-01..CP-06 | — | — |
| `cryptograph` | T037 | unit | `tests/cryptograph/**/*.test.mjs` | 8 | WK-01..WK-04 | — | — |
| `verification` | T014 | unit | `tests/integrity/product/**/*.test.mjs` | 14 | SC002-19..SC002-22, VB-01 | openssl | — |
| `completion` | T038 | unit | `tests/completion/**/*.test.mjs` | 8 | FEN-01..FEN-03 | — | — |
| `recovery` | T015 | unit | `tests/recovery/**/*.test.mjs` | 20 | SC003-01..SC003-20 | — | — |
| `zip` | T039 | unit | `tests/zip/**/*.test.mjs` | 6 | — | — | — |
| `artifacts` | T016 | unit | `tests/artifacts/**/*.test.mjs` | 10 | SC002-09, PK-01..PK-04 | — | — (contract: true) |
| `us2-local` | T017 | acceptance | `tests/acceptance/us2/**/*.test.mjs` | 12 | SC001-01..SC001-10, SC005-01, SC005-02 | openssl, chromium | `.verify-out/us2-local/evidence.json` labels: verifier_name, verifier_version, oracle_name, oracle_version, timing_scope=indicative-local, tree_digest |
| `hermes-contract` | T018 | contract | `tests/integrations/hermes/**/*.test.mjs` | 10 | HM-01..HM-06 | — | — (contract: true) |
| `webhooks` | T019 | unit | `tests/webhooks/**/*.test.mjs` | 14 | WH-01..WH-08 | — | — (contract: true) |
| `healthcare-contracts` | T020 | contract | `tests/integrations/healthcare/**/*.test.mjs` | 8 | HC-01..HC-04 | — | — |
| `anchoring-disabled` | T021 | unit | `tests/anchoring/**/*.test.mjs` | 6 | AN-01..AN-03 | — | — |
| `hermes-runtime` | T040 | unit | `tests/hermes-runtime/**/*.test.mjs` | 4 | HR-01..HR-03 | hermes-runtime (probe `scripts/capabilities/hermes-runtime.mjs`: docker daemon plus local image `docker.io/nousresearch/hermes-agent@sha256:adb7890916de63245523492be8f68f9d2095fecbec4e2a6dab7960381f0fc002`) | — |
| `us3` | T022 | acceptance | `tests/acceptance/us3/**/*.test.mjs` | 9 | SC006-01..SC006-09 | hermes-runtime, chromium | `.verify-out/us3/evidence.json` labels: hermes_runtime=isolated-local, hermes_upstream (image digest), hermes_version=v0.23.0, network=none, provider=scripted-deterministic, tool_search=on\|off, production_hermes=not-exercised, notification=local-spool, tree_digest |
| `us3-real-model` | T041 | acceptance | `tests/acceptance/us3-real-model/**/*.test.mjs` | 4 | RM-01..RM-04 | hermes-runtime, claude-sdk-transport (probe `scripts/capabilities/claude-sdk-transport.mjs`: host-side OpenAI-compatible adapter reachable from the container and an authorized max001 transport; absent → CAPABILITY_ABSENT) | `.verify-out/us3-real-model/evidence.json` labels: provider=claude-sdk-max001-local, credentials=host-only, hermes_upstream, hermes_version=v0.23.0, production_hermes=not-exercised, tree_digest. The evidence and the Hermes config must contain no credential value |
| `release-gates` | T023 | unit | `tests/release-gates/**/*.test.mjs` | 10 | RG-01..RG-06 | — | — |
| `operations` | T024 | unit | `tests/operations/**/*.test.mjs` | 6 | OP-01..OP-03 | — | — |
| `all-unit` | (npm test) | aggregate | components: every `unit`, `harness` and `contract` mode without capabilities | — | — | — | — |
| `acceptance-local` | T025 | aggregate | components: every mode above except `tooling` and `us3-real-model`, in task order | — | union must cover SC001-*, SC002-01..SC002-26, SC003-*, SC004-*, SC005-*, SC006-* | openssl, chromium, hermes-runtime | `.verify-out/acceptance-local/receipt.json` with `release_ready: false` and external gates OPEN; committed `docs/evidence/acceptance.json` with the same tree digest |
| `acceptance` | (release gate) | release | components: `acceptance-local` plus external gates `external-pdf-reader`, `declared-hardware`, `ci-run`, platform inventory (POLICY-PENDING), trusted certificate, real OIDC/SMTP | — | — | external-pdf-reader, declared-hardware, ci-run | receipt with every gate closed; FAIL while any is OPEN |

`typecheck: true` applies to every non-aggregate mode, `tooling` included, which proves the TypeScript pin and tsconfig work. Each aggregate typechecks once.

### Case catalogs (test names start with `[ID]`)

**SC-002 (all defined negative tests).**
- SC002-01: second recipient before first → 409, no approval
- SC002-02: recipient session of envelope A used on B → 404
- SC002-03: stale `expected_version` → 409
- SC002-04: `document_digest` mismatch → 409
- SC002-05: `manifest_digest` mismatch → 409
- SC002-06: wrong `prior_approval_digest` → 409
- SC002-07: cross-tenant read → uniform 404
- SC002-08: cross-tenant mutation → uniform 404
- SC002-09: cross-tenant artifact or verification read → uniform 404
- SC002-10: expired challenge → rejected
- SC002-11: expired invitation → rejected
- SC002-12: idle-expired session → 401
- SC002-13: consumed challenge replay → rejected
- SC002-14: session revoked by reissue → 401
- SC002-15: sixth wrong code → 429, locked
- SC002-16: approval without valid CSRF token → 403
- SC002-17: service (Hermes) credential calls approve → 401 (recipient scheme only)
- SC002-18: sender operator bearer calls approve → 401
- SC002-19: byte change inside the signed range → integrity invalid
- SC002-20: appended revision → revision_coverage incomplete, not accepted as the original
- SC002-21: `/Contents` padding change → exact-artifact digest mismatch
- SC002-22: self-signed certificate → integrity valid, trust untrusted
- SC002-23: same `Idempotency-Key`, changed payload → 409
- SC002-24: service-originated send without human-operator confirmation → 409 `confirmation_required`
- SC002-25: GET, HEAD or prefetch of the invitation link → no state change
- SC002-26: the cross-tenant 404 matrix, generated from contract paths, has a case for every operator route

**SC-003.** SC003-01..SC003-20 are finalization fault and race cases. Each injects a SIGKILL or a barrier at a named phase in a separate OS process (reserve → compose → seal → verify → store PDF → store evidence → publish → outbox), plus void, decline, expiry, operator-retry-while-running and stale-lease races. After every case the invariants hold: one artifact, one event, never both completed and voided, bounded attempts.

**SC-004.**
- SC004-01: keyboard-only completion with zero pointer events
- SC004-02: mobile emulation with no horizontal overflow
- SC004-03: consent and organization-seal wording visible
- SC004-04: opening, reloading or prefetching the link causes no approval or version change

**SC-001.** SC001-01..SC001-10 are ten consecutive one- and two-recipient flows. In each, the approval's `document_digest` equals the SHA-256 of the PDF bytes the browser received.

**SC-005.**
- SC005-01: navigation p95 ≤ 2 s
- SC005-02: finalization max ≤ 60 s, n=10 concurrent

Both are labeled `indicative-local`. The declared pilot hardware stays an OPEN gate.

**SC-006.**
- SC006-01..SC006-04: Hermes dispatch of prepare, request, status and retrieve
- SC006-05: Hermes credential calling approve is rejected
- SC006-06: openChart harness
- SC006-07: openPractice harness
- SC006-08: anchoring disabled
- SC006-09: fake anchoring failure

## Constitution Check

Planning passes the owner constraints: Apache-2.0 originals; human approval separation; verified completion; synthetic fixtures; Triad placement; no implicit deployment or merge.

- **Application gate (A-001).** MIT/Apache-only shipped materials are enforced by the strict application-dependency gate, which must pass for what ships.
- **Platform scope.** Platform/image eligibility is **POLICY-PENDING** (see above), so release stays blocked.
- **Planning basis.** The root's planning constitution reflects existing constraints, not newly ratified policy.
- **When to recheck.** After T027 qualification and before release.
- **Baseline.** Recorded in this feature so it travels with the spec leg.

## Research and Design

See [research](research.md), [data model](data-model.md) and [contracts](contracts/http-api.yaml).

- **Separate assurance domains.** Operator OIDC and the recipient mailbox challenge are separate assurance domains. Challenge state and invitation binding stop a forwarded link from constituting approval on its own.
- **Seal model.** A single seal over all approval values is the MVP. Individual certificate signatures and parallel composition remain out of scope.

Finalization is a job/outbox transaction:

1. reserve the expected envelope version;
2. compose into immutable temporary content-addressed storage;
3. seal;
4. independently verify;
5. durably store the PDF and evidence;
6. atomically publish one artifact and one completion event.

Void can win only before the finalizing reservation. Later cancellation returns a conflict and cannot claim the PDF was revoked. Failures keep the envelope in finalizing, with retryable/failed job status. Operator retry never creates new approvals. A compare-and-set on state/version, with a job lease and fencing token, plus uniqueness constraints prevent duplicate completion.

## Project Structure

The spec feature owns spec.md, plan.md, research.md, data-model.md, contracts/, quickstart.md, tasks.md and checklists/. governance-baseline.md is a byte-identical snapshot of the planning constitution (sha256 `eec589f3…`), kept so the baseline travels with the spec leg. The OpenSpec proposal/delta remains in the spec leg and hands off here. Code implementation is in the paired code worktree:

```text
src/server/              HTTP entrypoint, router, input limits, security headers, log redaction (T031)
src/api/auth/            operator OIDC/JWKS, role-by-operation matrix, service vs human operator (T005)
src/api/envelopes/       operator envelope handlers incl. send confirmation, void, reissue, retry (T034, T010, T038)
src/api/recipients/      recipient invitation/challenge/session/document/approve/decline handlers (T008, T010)
src/api/artifacts/       package, verification and receipt handlers (T016)
src/api/webhooks/        webhook registration and HMAC key rotation handlers (T019)
src/domain/              envelope/approval/version/state rules, consent text, finalization reservation (T007, T010, T038, T015)
src/storage/             node:sqlite schema/migrations/transactions, blobs, outbox, audit chain, restore (T003, T028-T030)
src/jobs/                expiry sweeper, finalization job runner with lease/fencing, outbox dispatcher (T010, T015, T038)
src/notifications/       mail adapter: dev file spool, guarded SMTP, redaction, rate limits (T032)
src/pdf/                 bounded PDF parser and the isolated parse service (T033)
src/cryptograph/         compose (incremental update), cms (DER/SignedData), keys, worker (T013, T036, T037)
src/verifier/            shipped product verifier; imports only node:* and src/verifier/** (T014)
src/artifacts/           deterministic ZIP writer and package assembly (T039, T016)
src/autograph/           browser review/consent/signature UI sources (T009); built to dist/ by T035
src/integrations/hermes/ stdio MCP server and adapter: four tools, no approve tool (T018)
src/integrations/webhooks/ outbox delivery, signed replay-safe callbacks, SSRF-safe connector (T019)
src/integrations/healthcare/ synthetic openChart/openPractice harness consumers (T020)
src/anchoring/           disabled and fake adapters only (T021)
scripts/                 fetch-node.sh, frozen verify runner/registry, inventory, no-secrets, dev, build-ui, release evidence
scripts/capabilities/    delegated capability probes (hermes-runtime, claude-sdk-transport)
tests/                   one directory per verify mode (see registry); tests/integrity/oracle/ is independent of src/
fixtures/synthetic/      generated documents and identities only (no keys, no signature images)
fixtures/hermes/         ephemeral isolated-Hermes config template and scripted endpoint (T040)
contracts/               vendored copy of the spec-leg http-api contract plus source record (T006)
docs/                    qualification, platform inventory, operations, threat model, evidence summaries
tools/                   gitignored pinned runtime (never committed)
dist/, var/, .verify-out/ gitignored build output, local data and run evidence
```

## Governed invariants

- `src/domain/` — approval/version/state transitions and tenant isolation.
- `src/api/auth/` — operator/recipient/service authority.
- `src/cryptograph/` — key custody, verification and completion integrity.
- `src/storage/` — durable evidence and exactly-once publication.
- `src/integrations/` — domain authority and notification permissions.
- `src/anchoring/` — privacy and no real ledger activity.
- `package-lock.json` — pinned dependency inventory.
- `package.json` — dependency set, engines pin and the npm script surface the gates use.
- `.node-version` — pinned runtime version.
- `scripts/fetch-node.sh` — runtime provenance: embedded tarball digest and release-key fingerprints.
- `scripts/verify*.mjs` — the frozen gate engine and mode registry (frozen after T001).
- `tests/tooling/verification-runner.test.mjs` — the gate's own self-test (frozen after T001).

## Delivery and Landing

T001 establishes the pinned runtime, the type check and the frozen runner. T026 (no-secrets) and T002/T027 (inventory and qualification) must pass before anything builds on dependencies. US1 delivers human approval visibly short of sealed completion. US2 completes the signing MVP. US3 proves Hermes reuse and the healthcare contracts. Run synthesis/security/recovery acceptance before claiming the whole feature complete. Keep every implementation task unchecked until it is executed.

The feature branches depend on foundation PRs openSig#1, openSig-spec#1 and openSig-code#1. A stacked spec PR targets the spec foundation branch until it lands. The code worktree exists but contains no new implementation. Do not create an empty implementation PR. After actual implementation, review and land both legs, advance the matching assembly pins on the feature branch, and only then archive. No assembly feature pin is advanced by this planning work.
