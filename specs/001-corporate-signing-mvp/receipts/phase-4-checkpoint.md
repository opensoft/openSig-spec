# Phase 4 Checkpoint Receipt for the Corporate Signing MVP

**Open.** The independent checkpoint at published code head `b35c18ced07965251cab343a4fa60fa070e1dcba` failed. SC001-03 did not complete, and two dependent checks reported the missing flow.

## Table 1. Identity and runtime

| Item | Recorded value |
| --- | --- |
| Published code head | `b35c18ced07965251cab343a4fa60fa070e1dcba` |
| Code Git tree | `4aa4a6aa410b6e382f8e4be80e2237f8b494d6fd` |
| Source digest | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` (499 in-scope Git paths) |
| Governing spec head | `c29102997fbc1653d629c1e9ef0014a3e1d445f3` |
| Task and plan hashes | `192b952c3f3ad33ac3abd5ffcd56283e2835048e44c3dc5050558e91310111e6`, `61415e2cee47086823c14f1f6f0a2f841bf49997be00716a943fda6f057ceecc` |
| Remote readback | 2026-11-23T02:29:22.817353+00:00 and 2026-11-23T02:52:09.994721+00:00 |
| Fresh runner | `/root/us2_phase4` had no authorship, acceptance or recheck role on US2 |
| Native assignment | gpt-6.2-sol with high reasoning effort, inherited by full-history fork. Separate child/backend trace was unavailable. |
| Run window | 2026-11-23T02:29:11.841414+00:00 to 2026-11-23T02:52:19.775791+00:00 |
| Runtime | v24.22.2 with pinned Node binary SHA-256 `904a6d3484241d466989f9df9ba3aab822b1496191bdb28230eb491a55c32a52` and OpenSSL 3.0.13 30 Jan 2024 (Library: OpenSSL 3.0.13 30 Jan 2024) |

## Table 2. Serial verification results

| # | Mode | Owner | Verbatim decisive line | After digest |
| --- | --- | --- | --- | --- |
| 1 | `us2-local` | T017 | `verify: us2-local FAIL exit=1 files=4 tests=16 pass=13 fail=3 skip=0 todo=0 cases=10/12` | `null` |
| 2 | `tooling` | T001 | `verify: tooling PASS exit=0 files=1 tests=38 pass=38 fail=0 skip=0 todo=0 cases=0/0` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 3 | `no-secrets` | T026 | `verify: no-secrets PASS exit=0 files=1 tests=29 pass=29 fail=0 skip=0 todo=0 cases=7/7` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 4 | `license-inventory` | T002 | `verify: license-inventory PASS exit=0 files=1 tests=23 pass=23 fail=0 skip=0 todo=0 cases=10/10` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 5 | `qualification` | T027 | `verify: qualification PASS exit=0 files=1 tests=10 pass=10 fail=0 skip=0 todo=0 cases=5/5` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 6 | `storage` | T003 | `verify: storage PASS exit=0 files=6 tests=29 pass=29 fail=0 skip=0 todo=0 cases=0/0` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 7 | `blobs` | T028 | `verify: blobs PASS exit=0 files=1 tests=12 pass=12 fail=0 skip=0 todo=0 cases=4/4` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 8 | `audit-chain` | T029 | `verify: audit-chain PASS exit=0 files=4 tests=26 pass=26 fail=0 skip=0 todo=0 cases=5/5` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 9 | `outbox-restore` | T030 | `verify: outbox-restore PASS exit=0 files=3 tests=41 pass=41 fail=0 skip=0 todo=0 cases=4/4` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 10 | `dev` | T004 | `verify: dev PASS exit=0 files=5 tests=18 pass=18 fail=0 skip=0 todo=0 cases=3/3` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 11 | `auth` | T005 | `verify: auth PASS exit=0 files=4 tests=20 pass=20 fail=0 skip=0 todo=0 cases=10/10` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 12 | `server` | T031 | `verify: server PASS exit=0 files=13 tests=54 pass=54 fail=0 skip=0 todo=0 cases=6/6` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 13 | `notifications` | T032 | `verify: notifications PASS exit=0 files=4 tests=14 pass=14 fail=0 skip=0 todo=0 cases=5/5` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 14 | `parser-limits` | T033 | `verify: parser-limits FAIL exit=1 files=11 tests=46 pass=45 fail=1 skip=0 todo=0 cases=12/12` | `null` |
| 15 | `envelope-contract` | T006 | `verify: envelope-contract PASS exit=0 files=6 tests=93 pass=93 fail=0 skip=0 todo=0 cases=0/0` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 16 | `envelopes` | T007 | `verify: envelopes PASS exit=0 files=10 tests=48 pass=48 fail=0 skip=0 todo=0 cases=8/8` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 17 | `envelope-api` | T034 | `verify: envelope-api PASS exit=0 files=10 tests=42 pass=42 fail=0 skip=0 todo=0 cases=5/5` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 18 | `recipients` | T008 | `verify: recipients PASS exit=0 files=10 tests=44 pass=44 fail=0 skip=0 todo=0 cases=11/11` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 19 | `approval` | T010 | `verify: approval PASS exit=0 files=8 tests=25 pass=25 fail=0 skip=0 todo=0 cases=8/8` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 20 | `ui-build` | T035 | `verify: ui-build PASS exit=0 files=6 tests=17 pass=17 fail=0 skip=0 todo=0 cases=4/4` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 21 | `autograph` | T009 | `verify: autograph PASS exit=0 files=6 tests=21 pass=21 fail=0 skip=0 todo=0 cases=6/6` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 22 | `us1` | T011 | `verify: us1 PASS exit=0 files=7 tests=9 pass=9 fail=0 skip=0 todo=0 cases=6/6` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 23 | `integrity-contract` | T012 | `verify: integrity-contract PASS exit=0 files=2 tests=50 pass=50 fail=0 skip=0 todo=0 cases=8/8` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 24 | `cms` | T013 | `verify: cms PASS exit=0 files=3 tests=18 pass=18 fail=0 skip=0 todo=0 cases=2/2` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 25 | `compose` | T036 | `verify: compose PASS exit=0 files=6 tests=38 pass=38 fail=0 skip=0 todo=0 cases=6/6` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 26 | `cryptograph` | T037 | `verify: cryptograph PASS exit=0 files=4 tests=19 pass=19 fail=0 skip=0 todo=0 cases=4/4` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 27 | `verification` | T014 | `verify: verification PASS exit=0 files=16 tests=163 pass=163 fail=0 skip=0 todo=0 cases=5/5` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 28 | `completion` | T038 | `verify: completion PASS exit=0 files=10 tests=44 pass=44 fail=0 skip=0 todo=0 cases=3/3` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 29 | `recovery` | T015 | `verify: recovery PASS exit=0 files=5 tests=23 pass=23 fail=0 skip=0 todo=0 cases=20/20` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 30 | `zip` | T039 | `verify: zip PASS exit=0 files=2 tests=19 pass=19 fail=0 skip=0 todo=0 cases=0/0` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |
| 31 | `artifacts` | T016 | `verify: artifacts PASS exit=0 files=3 tests=19 pass=19 fail=0 skip=0 todo=0 cases=5/5` | `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` |

Typecheck exited 0.

## Failed US2 cases and evidence

- **SC001-03.** On the one-recipient cross-reference-stream fixture, Chromium resolved the “Draw my signature” radio and completed the normal pointer action. Playwright reported `locator.check: Clicking the checkbox did not change its state`. The original action mechanism and browser event sequence remain unconfirmed.
- **SC005-01.** The navigation timing check reported `all ten flows completed before the navigation figures are taken`. The run had nine completed flows, so the combined navigation measurement was unavailable.
- **US2-RECORD.** The ten-flow record check reported `flows.json holds the ten flows`. It enumerated the same nine flow IDs.

The failed mode retained before digest `763fb6eb79f3c0df706537e0c2417d58ee0c2ec284f0f432e7334e4b9bcff578` and a `null` after digest. SC005-02, US2-TAMPER and the separate trust-anchor control passed in that mode. The first failure and these passing cases have separate recorded case IDs.

## Parser cleanup result

The same batch reported a PL-26 failure in `parser-limits`. The captured bwrap init did not become reaped within the test’s 20,000 ms bound, leaving the elapsed time as `NaN`. The wrapper ran as a child subreaper and waited to reap adopted children until the mode exited. That ordering can keep an orphan visible to PL-26 during the assertion, and the specific PID’s reaping path remains unconfirmed. The raw `parser-limits-result.json` and its null after digest are preserved in the batch manifest.

The later normal-launcher verification at the same published code head passed PL-26 and all 46 tests in `parser-limits`. It used `setsid --wait` and ordinary orphan reaping. The before and after digests matched. The separate `normal-run.json` SHA-256 is `742d257e458395943905d3238a9a19ebd4e96e80dc98c2965c440433b74d4319`, and its raw result SHA-256 is `72226f7abad988d010a6546d05c43851a692bca71fb402ee25ab076709f8c761`.

## Evidence integrity and first wrapper attempt

The replacement batch manifest `SHA256SUMS` has 104 verified entries and SHA-256 `7bb7b21dc21c985fcd5458e6a79250c45a882c79740d733da2b9111d06624153`. `batch-summary.json` has SHA-256 `e9a59f69d7239de9b18cd7433c1a973b13dc7149f6f2df0ffa89d3d2521260eb`. The exact raw `us2-local-result.json` has SHA-256 `d18c7fbdce7d00bfe8821cf5e4770006d6f1829ab4cfc61478887506b157651c`.
The first wrapper attempt stopped after a passing US2 mode when its guard reported `owned processes remain; next modes stopped`. On follow-up all 49 recorded child IDs were absent. Their states at the original guard were unobserved. Its summary SHA-256 is `7f1ca4c73025f308c5db8481108251bdfa4dc888e6f101eb9517bcfa11c6a663` and manifest SHA-256 `f151919d3da5defb835a8139c4e2eb12bd283a49192a68bc99a472cfb76c381e`.
The replacement run used the published code after the wrapper was corrected to reap exited adopted children before checking for live survivors. The wrapper patch SHA-256 is `6fcd2fd08c98e58ba38c5de9d3613751e69c3900aaefe0c4bfb528308540bd47`.

## Follow-up diagnostics

The bounded browser diagnostics left the SC001-03 failure mechanism unresolved. A held-response control reproduced the same radio error after the matching iframe had loaded, so the readiness-only patch failed its admission check. The pass in the event-traced comparison and all six ordinary checks in the alternating batch reached the same connected radio through trusted, uncancelled events. Those runs stopped before approval and recorded healthy comparisons.

The six-context result is `alternating-radio-trace.json` with SHA-256 `fe7a87c4d451766826774652911a40c9198f4f36bae1f8b1d1dd110290898625`. Its wrapper evidence is `alternating-radio-trace-run.json` with SHA-256 `71cfec11a04afafd7a4abbcf61f97f734e3b95324a535962e0d68890ee056af1`. The failed Phase 4 result above governs the checkpoint.

## Determination

Phase 4 stays open until the owner addresses the failed interaction and a fresh independent full checkpoint passes on a new published code head.

The exact-head hosted review also raised a certificate-chain path-length finding in the verifier. [Codex review](https://github.com/opensoft/openSig-code/pull/2#pullrequestreview-6015906452) · [Path-length finding](https://github.com/opensoft/openSig-code/pull/2#discussion_r4661257964)
