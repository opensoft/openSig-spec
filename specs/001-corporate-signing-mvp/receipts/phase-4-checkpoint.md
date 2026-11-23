# Phase 4 Checkpoint Receipt for the Corporate Signing MVP

**Closed.** A fresh independent agent completed all 31 Phase 1–4 modes and typecheck at published code head `ccd6ff10a32f173272826b47c5b06611ad97df73`. All 1,068 tests passed. The evidence applies to the synthetic US2 workflow defined in [the task ledger](../tasks.md).

## Table 1. Identity and runtime

| Item | Recorded value |
| --- | --- |
| Published code head | `ccd6ff10a32f173272826b47c5b06611ad97df73` |
| Code Git tree | `f754fa21cb5f4a2e4402fde2aa2803c7807c2fa9` |
| Independent source digest | `d5ce1267fb221f607b63546ddfa5e87ae903bde2a9c361624a5d50d37ba6b0d8` across 499 in-scope Git paths |
| Governing spec head | `c31d920a1385c8e7b560f07b718fa3408cf89bb5` |
| Spec source hashes | `tasks.md` `c5a7443b861e62119450ad200e0490a13f0f38f45e5f235c43fd59b1ffa4dfc9`, `plan.md` `61415e2cee47086823c14f1f6f0a2f841bf49997be00716a943fda6f057ceecc` |
| Remote identity | `refs/heads/001-corporate-signing-mvp` returned the code head at 2026-11-23T04:36:32.443987Z before the suite and 2026-11-23T04:57:36.137317Z after it |
| Runtime | v24.22.2 with binary SHA-256 `904a6d3484241d466989f9df9ba3aab822b1496191bdb28230eb491a55c32a52` matching the pinned receipt `e2094387c4eb7fe284e5a714f595eacdff944b2a885bcbba6b88972244350522` |
| Chromium | chromium 149.0.7827.55, revision 1228 |
| OpenSSL and sandbox tools | OpenSSL 3.0.13 30 Jan 2024 (Library: OpenSSL 3.0.13 30 Jan 2024) and bubblewrap 0.9.0 |
| Fresh runner | `/root/us2_phase4_next` had no authorship, acceptance or recheck role on US2 |
| Native assignment | gpt-6.2-sol with high reasoning effort, inherited by a full-history fork with no override. Separate child/backend trace was unavailable. |
| Assignment source | `01a2060a-e7a1-7812-b0df-3a436bf83a47` parent turn 2026-11-23T03:18:11.187Z, context SHA-256 `d7f90e31bd9aa3f749974720f92cdabd66997330ddaf2111bef579e3adaaf04f` |
| Run window | 2026-11-23T04:36:38.115702Z to 2026-11-23T04:57:36.128069Z |

The checkout was detached and clean before and after the run. The three frozen files matched `ce36f6e` byte for byte.

| Frozen file | SHA-256 |
| --- | --- |
| `scripts/verify.mjs` | `2f2bbe009e69b3aca00b349c2d21f9951233b9c07931719b82027c42c5ee873a` |
| `scripts/verify-modes.mjs` | `67b2ca1d53c5faa95adaedda2393f9e0d517b6f6825394cdfef2186a7ce19cc3` |
| `tests/tooling/verification-runner.test.mjs` | `7e921b862b2a6bb1d67a99e05b016f4f49441366917b8d8d0b75801e4cac891d` |

## Table 2. Serial verification results

| # | Mode | Owner | Verbatim decisive line | UTC window | 1-minute load |
| --- | --- | --- | --- | --- | --- |
| 1 | `us2-local` | T017 | `verify: us2-local PASS exit=0 files=4 tests=16 pass=16 fail=0 skip=0 todo=0 cases=12/12` | 04:36:38Z–04:40:22Z | 6.10 → 10.58 |
| 2 | `tooling` | T001 | `verify: tooling PASS exit=0 files=1 tests=38 pass=38 fail=0 skip=0 todo=0 cases=0/0` | 04:40:22Z–04:41:03Z | 10.58 → 7.79 |
| 3 | `no-secrets` | T026 | `verify: no-secrets PASS exit=0 files=1 tests=29 pass=29 fail=0 skip=0 todo=0 cases=7/7` | 04:41:03Z–04:41:13Z | 7.79 → 7.12 |
| 4 | `license-inventory` | T002 | `verify: license-inventory PASS exit=0 files=1 tests=23 pass=23 fail=0 skip=0 todo=0 cases=10/10` | 04:41:13Z–04:41:31Z | 7.12 → 5.96 |
| 5 | `qualification` | T027 | `verify: qualification PASS exit=0 files=1 tests=10 pass=10 fail=0 skip=0 todo=0 cases=5/5` | 04:41:31Z–04:41:41Z | 5.96 → 6.15 |
| 6 | `storage` | T003 | `verify: storage PASS exit=0 files=6 tests=29 pass=29 fail=0 skip=0 todo=0 cases=0/0` | 04:41:41Z–04:41:54Z | 6.15 → 5.36 |
| 7 | `blobs` | T028 | `verify: blobs PASS exit=0 files=1 tests=12 pass=12 fail=0 skip=0 todo=0 cases=4/4` | 04:41:54Z–04:42:04Z | 5.36 → 5.64 |
| 8 | `audit-chain` | T029 | `verify: audit-chain PASS exit=0 files=4 tests=26 pass=26 fail=0 skip=0 todo=0 cases=5/5` | 04:42:04Z–04:42:34Z | 5.64 → 6.70 |
| 9 | `outbox-restore` | T030 | `verify: outbox-restore PASS exit=0 files=3 tests=41 pass=41 fail=0 skip=0 todo=0 cases=4/4` | 04:42:34Z–04:45:13Z | 6.70 → 10.25 |
| 10 | `dev` | T004 | `verify: dev PASS exit=0 files=5 tests=18 pass=18 fail=0 skip=0 todo=0 cases=3/3` | 04:45:13Z–04:45:37Z | 10.25 → 10.50 |
| 11 | `auth` | T005 | `verify: auth PASS exit=0 files=4 tests=20 pass=20 fail=0 skip=0 todo=0 cases=10/10` | 04:45:37Z–04:45:49Z | 10.50 → 11.18 |
| 12 | `server` | T031 | `verify: server PASS exit=0 files=13 tests=54 pass=54 fail=0 skip=0 todo=0 cases=6/6` | 04:45:49Z–04:47:12Z | 11.18 → 9.53 |
| 13 | `notifications` | T032 | `verify: notifications PASS exit=0 files=4 tests=14 pass=14 fail=0 skip=0 todo=0 cases=5/5` | 04:47:12Z–04:47:23Z | 9.53 → 8.82 |
| 14 | `parser-limits` | T033 | `verify: parser-limits PASS exit=0 files=11 tests=46 pass=46 fail=0 skip=0 todo=0 cases=12/12` | 04:47:23Z–04:48:23Z | 8.82 → 6.44 |
| 15 | `envelope-contract` | T006 | `verify: envelope-contract PASS exit=0 files=6 tests=93 pass=93 fail=0 skip=0 todo=0 cases=0/0` | 04:48:23Z–04:48:31Z | 6.44 → 6.09 |
| 16 | `envelopes` | T007 | `verify: envelopes PASS exit=0 files=10 tests=48 pass=48 fail=0 skip=0 todo=0 cases=8/8` | 04:48:31Z–04:48:54Z | 6.09 → 5.03 |
| 17 | `envelope-api` | T034 | `verify: envelope-api PASS exit=0 files=10 tests=42 pass=42 fail=0 skip=0 todo=0 cases=5/5` | 04:48:54Z–04:49:44Z | 5.03 → 3.93 |
| 18 | `recipients` | T008 | `verify: recipients PASS exit=0 files=10 tests=44 pass=44 fail=0 skip=0 todo=0 cases=11/11` | 04:49:44Z–04:50:20Z | 3.93 → 3.55 |
| 19 | `approval` | T010 | `verify: approval PASS exit=0 files=8 tests=25 pass=25 fail=0 skip=0 todo=0 cases=8/8` | 04:50:20Z–04:50:56Z | 3.55 → 4.26 |
| 20 | `ui-build` | T035 | `verify: ui-build PASS exit=0 files=6 tests=17 pass=17 fail=0 skip=0 todo=0 cases=4/4` | 04:50:56Z–04:51:16Z | 4.26 → 3.76 |
| 21 | `autograph` | T009 | `verify: autograph PASS exit=0 files=6 tests=21 pass=21 fail=0 skip=0 todo=0 cases=6/6` | 04:51:16Z–04:52:39Z | 3.76 → 4.02 |
| 22 | `us1` | T011 | `verify: us1 PASS exit=0 files=7 tests=9 pass=9 fail=0 skip=0 todo=0 cases=6/6` | 04:52:39Z–04:53:34Z | 4.02 → 3.81 |
| 23 | `integrity-contract` | T012 | `verify: integrity-contract PASS exit=0 files=2 tests=50 pass=50 fail=0 skip=0 todo=0 cases=8/8` | 04:53:34Z–04:53:43Z | 3.81 → 4.15 |
| 24 | `cms` | T013 | `verify: cms PASS exit=0 files=3 tests=18 pass=18 fail=0 skip=0 todo=0 cases=2/2` | 04:53:43Z–04:53:54Z | 4.15 → 3.74 |
| 25 | `compose` | T036 | `verify: compose PASS exit=0 files=6 tests=38 pass=38 fail=0 skip=0 todo=0 cases=6/6` | 04:53:54Z–04:54:50Z | 3.74 → 5.90 |
| 26 | `cryptograph` | T037 | `verify: cryptograph PASS exit=0 files=4 tests=19 pass=19 fail=0 skip=0 todo=0 cases=4/4` | 04:54:50Z–04:55:07Z | 5.90 → 5.67 |
| 27 | `verification` | T014 | `verify: verification PASS exit=0 files=16 tests=163 pass=163 fail=0 skip=0 todo=0 cases=5/5` | 04:55:07Z–04:55:57Z | 5.67 → 6.85 |
| 28 | `completion` | T038 | `verify: completion PASS exit=0 files=10 tests=44 pass=44 fail=0 skip=0 todo=0 cases=3/3` | 04:55:57Z–04:56:29Z | 6.85 → 7.21 |
| 29 | `recovery` | T015 | `verify: recovery PASS exit=0 files=5 tests=23 pass=23 fail=0 skip=0 todo=0 cases=20/20` | 04:56:29Z–04:57:07Z | 7.21 → 6.92 |
| 30 | `zip` | T039 | `verify: zip PASS exit=0 files=2 tests=19 pass=19 fail=0 skip=0 todo=0 cases=0/0` | 04:57:07Z–04:57:14Z | 6.92 → 6.61 |
| 31 | `artifacts` | T016 | `verify: artifacts PASS exit=0 files=3 tests=19 pass=19 fail=0 skip=0 todo=0 cases=5/5` | 04:57:14Z–04:57:28Z | 6.61 → 6.59 |

Each result reported equal, non-null before and after digests of `d5ce1267fb221f607b63546ddfa5e87ae903bde2a9c361624a5d50d37ba6b0d8`. Fail, skip, todo, cancelled and only counts were zero throughout. Typecheck exited 0 at 2026-11-23T04:57:36.128069Z. The silent command produced no output.

## US2 workflow evidence

| Label | Value |
| --- | --- |
| `verifier_name` | `opensig-product-verifier` |
| `verifier_version` | `0.1.0` |
| `oracle_name` | `opensig-integrity-oracle` |
| `oracle_version` | `0.1.0` |
| `timing_scope` | `indicative-local` |
| `tree_digest` | `d5ce1267fb221f607b63546ddfa5e87ae903bde2a9c361624a5d50d37ba6b0d8` |

Passing required cases were `SC001-01`, `SC001-02`, `SC001-03`, `SC001-04`, `SC001-05`, `SC001-06`, `SC001-07`, `SC001-08`, `SC001-09`, `SC001-10`, `SC005-01`, `SC005-02`. The runner captured these IDs from passing test names. Verification cases `SC002-19`, `SC002-20`, `SC002-21`, `SC002-22`, `VB-01`, `VB-05`, `VB-06` also passed, including the composed sandbox tests VB-05 and VB-06.

Product and oracle columns show integrity, revision coverage, trust and the approval-evidence result in that order.

| Flow | Recipients | Document digest matched | Product | Independent oracle | Package SHA-256 |
| --- | --- | --- | --- | --- | --- |
| SC001-01 | 1 | yes | valid / complete / untrusted / pass | valid / complete / untrusted / match | `9b038927d55140f1f22a0f1f0ff9b1cd1f0132b423912ff563473dce180225e6` |
| SC001-02 | 2 | yes | valid / complete / untrusted / pass | valid / complete / untrusted / match | `0d02983b179870483c45b36808088b68d91d2393f8a4a4b6fc712d56d74e1812` |
| SC001-03 | 1 | yes | valid / complete / untrusted / pass | valid / complete / untrusted / match | `db733fdffa20dfc26385fbb1c415d73430a519c12d1f0fbdf782ae8919b53c14` |
| SC001-04 | 2 | yes | valid / complete / untrusted / pass | valid / complete / untrusted / match | `de27076fe56e15aa7a71e473e20ed8151d21406f4780f24a42881dc12aa943b0` |
| SC001-05 | 1 | yes | valid / complete / untrusted / pass | valid / complete / untrusted / match | `65c1635bc38c970582810706d33e605252e12c6ff9c0d37246169fd817f01b1a` |
| SC001-06 | 2 | yes | valid / complete / untrusted / pass | valid / complete / untrusted / match | `84ee5ea443396998a8aa4dedc742319105f60ef13132e3d801c8e51be965ba11` |
| SC001-07 | 1 | yes | valid / complete / untrusted / pass | valid / complete / untrusted / match | `1152774cf7d3dc02a042216115cd67d6d8de3ef4555a87bf1a53b29cdeca84ab` |
| SC001-08 | 2 | yes | valid / complete / untrusted / pass | valid / complete / untrusted / match | `0cc6fe9542fbe21e99affd1b7c83681245cdfe3491571d685ac9619e88d9dc6a` |
| SC001-09 | 1 | yes | valid / complete / untrusted / pass | valid / complete / untrusted / match | `9ac2734dfb1eb904798ada58440175c59604d3117baa28bff64a98574518584a` |
| SC001-10 | 2 | yes | valid / complete / untrusted / pass | valid / complete / untrusted / match | `d2bb6887b5cd13194141249148df43800e9eeffa06b7041ef3ec6f5ab34f5631` |

All ten flows reported valid integrity and `untrusted` for the development certificate. SC002-22 confirmed the same trust result.

`[US2-TAMPER]` used the completed `SC001-01` flow. Both checkers reported invalid integrity when a byte changed inside either signed range and incomplete revision coverage after an appended revision. Both reported trusted when the sealing certificate was the sole anchor. SC002-19 through SC002-21 also passed.

The separate composed `[US2-ANCHOR-CONTROL]` stored `trusted` with `pass/anchor-direct`. Its served report matched the packaged report, and teardown ended with zero running or waiting parser jobs.

SC005-01 recorded combined navigation p95 of 221.4 ms over 65 operations. SC005-02 recorded ten final approvals in one burst, server p95 17409 ms, server maximum 17409 ms and client maximum 17548.9 ms. The observed peaks were 10 finalizing envelopes and 1 signing attempt(s) in progress. The figures are `indicative-local`.

US2 file order was `tests/acceptance/us2/concurrent.test.mjs` with 1 tests in 22016 ms, `tests/acceptance/us2/flows.test.mjs` with 13 tests in 177101 ms, `tests/acceptance/us2/static.test.mjs` with 1 tests in 112 ms, `tests/acceptance/us2/trust-anchor-control.test.mjs` with 1 tests in 16449 ms.

## US1 workflow evidence

The browser labels were `browser_engine=chromium` and `browser_version=149.0.7827.55`. Passing required cases were `SC004-01`, `SC004-02`, `SC004-03`, `SC004-04`, `US1-01`, `US1-02`.

## Load, cleanup and evidence integrity

The load gate ran from 2026-11-23T04:36:38.115004Z to 2026-11-23T04:36:38.115150Z. Its last one-minute load was 6.10. No recorded owned process or child group remained. The count of matching temporary directories was 109 before and 109 after.

Each mode ran in its own ordinary process session with inherited orphan reaping. PL-26 passed in the independent parser-limits mode. The separately run normal-launcher parser-limits check also passed all 46 tests at the preceding published head.

| Evidence file | SHA-256 |
| --- | --- |
| `batch-summary.json` | `c407c12c93c0faa92139b4b402936a80b64665d71c518e548174d87239f22ca9` |
| `spec-binding.json` | `3347eef79b9b28e025aeaebf78f1e2d1cf6977ea7c61c9ff22dec679cca88c90` |
| `tree-digest.json` | `69bedf1ebfb3e893cd5ca5c701de1c87913cbbb2056b760fbd68a11f2225ea0e` |
| `phase4-runner.py` | `4e7a46f2d55e788a936d622f7a0a6e22670fd065537317ecc6a6b291000448ee` |
| `load-gate.json` | `0b02cccc7847412cc2a78575c3b04a61f231984d3ceed5ba300ae46e229a8303` |
| `us2-local-result.json` | `bcc8343f33a1f470c2683bbf0a2e5eb57e6d1463c1882dd3d975159ccd7eb77d` |
| `us2-local-evidence.json` | `5780307b24f742d4525eec157ee5ed54f310d104fbe2f58b05c61bc526c49e53` |
| `us2-local-flows.json` | `811f35a53768e8ec78367beb0822454d8c8d6705219a9d5c7b35f1e2f02e7e26` |
| `us2-local-anchor-control.json` | `fbc5f0c704ffb4f8ffdc40557c174ac519b85d9194732906246de235c000cd37` |
| `us1-result.json` | `a55bd76c594dc80146481d1d748c8dbab98ed522e2a4c44d22cc1e34424b7768` |
| `us1-evidence.json` | `59bc2629517370c16af8c201cad15b89835b9928102de8de5718d78804d6e90c` |
| `verification-result.json` | `763ef8b978b3217533e4b0260c5afc05f66a7467717f85d4487b5c4c54f9eada` |
| `typecheck.log` | `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |

The complete checkpoint manifest `SHA256SUMS` has 104 verified entries and SHA-256 `9be1d7226b5b47bcf267b78805501729d0dd0af25e0c56d1bc3778e82791d7ef`. It binds the raw per-mode results and logs as well as the files above.
The earlier failed run and its preserved results are in the [prior checkpoint receipt](https://github.com/opensoft/openSig-spec/blob/8bad1a8b0ab03958158f8cb3a0f829ac00b62ca1/specs/001-corporate-signing-mvp/receipts/phase-4-checkpoint.md).

## Determination

Phase 4 closed at `ccd6ff10a32f173272826b47c5b06611ad97df73`. Completing the scroll before restoring radio focus allowed the same ordinary click to select the radio. The accepted patch is scoped to the drawn-signature path in the browser test. The published head passed the independent US2 checkpoint.
