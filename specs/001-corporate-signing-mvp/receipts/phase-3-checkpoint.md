# Phase 3 Checkpoint Receipt: 001-corporate-signing-mvp

**The Phase 3 checkpoint, the US1 independent test, is CLOSED at `code_head` `79f1863570c79530809ba2bf03c084a400c04081`.** Every item of tasks.md § "Phase 3 checkpoint, the US1 independent test" holds; see the determination at the end. US2's commits may start. The MVP is not ready: US2, US3 and the release tasks remain, and the 7 external gates stay OPEN.

The format follows the recording rules of tasks.md § "Phase 2 close receipts (A-010)", and table 1 follows table 1 of `phase-2-close.md`.

**Sources.**
- The run and its evidence are the checkpoint agent's: agent `a7d40472466dc4f48`, a fresh agent (Claude Opus 5.5, xhigh reasoning effort). Its transcript model check is MATCH: the assistant-message models are `claude-opus-5-5` in 107 of 107. It wrote none of the US1 code or tests (T006–T011, T034, T035 and their follow-ups) and accepted none of them.
- The evidence set is the checkpoint agent's evidence set (orchestrator workspace). Its files are named below by name and sha256, relative to that set.
- The orchestrator verified it at about 13:37Z: the model check, all 21 result files (PASS, zero fail, skip or todo, equal digests, 608 tests), the independent-digest file, and the 22 summary rows (21 modes plus typecheck).
- The Architect re-read the evidence set before writing this receipt. That covered every result file, each mode's decisive line verbatim in its log, the `us1` evidence file, and the six case IDs in the test files at `code_head`. The Architect computed the `code_tree` and `runner` rows of table 1 from git objects at `code_head`.

No host-absolute path appears. `docs/evidence/us1.md` stays T011's evidence for its own tree (0035817, digest 9ea3e8ee…3fbe) and is not cited here.

## Table 1. Header

| Key | Value |
|---|---|
| `code_head` | 79f1863570c79530809ba2bf03c084a400c04081 (the published head of the openSig-code branch `001-corporate-signing-mvp`; parent 0035817ce86cb89e11a91fc494e3a623fca22466). `git ls-remote origin refs/heads/001-corporate-signing-mvp` returned it at 13:17:30Z and again at 13:35:26Z, after the run |
| `code_tree` | f512f4ea196bcdd48a8bc4b92bc6a8f35a567a0e |
| `tree_digest` | 3ef7edefc6be72762b00e8845d841ea052d98bbea62dc59422ab7710a1cf9ac4 (the runner's tree digest at `code_head`; all 21 mode `result.json` files record the same value in `tree_digest` and `tree_digest_after`, none null; equal to the independent recomputation in table 4) |
| `spec_head` | 15c4269c763e516fbddac42a1b943028ee0ce4a5 (the spec commit whose checkpoint definition was applied) |
| `tasks_sha256` | f41eaf800a5c1d7c3740cd1f88fffaca30cc00772a7418645ed1fd84b3135b23 (`specs/001-corporate-signing-mvp/tasks.md` at `spec_head`) |
| `runtime` | `tools/node/bin/node --version` = `v24.22.2`; binary sha256 = 904a6d3484241d466989f9df9ba3aab822b1496191bdb28230eb491a55c32a52 (equal to `node_sha256` in `tools/node.receipt.json`) |
| `runner` | `scripts/verify.mjs` sha256 = 2f2bbe009e69b3aca00b349c2d21f9951233b9c07931719b82027c42c5ee873a; `scripts/verify-modes.mjs` sha256 = 67b2ca1d53c5faa95adaedda2393f9e0d517b6f6825394cdfef2186a7ce19cc3; `tests/tooling/verification-runner.test.mjs` sha256 = 7e921b862b2a6bb1d67a99e05b016f4f49441366917b8d8d0b75801e4cac891d. Each equals the sha256 of `git show ce36f6e:<path>` (ce36f6e is an ancestor of `code_head`). |
| `browser` | Chromium revision 1228, version 149.0.7827.55, Playwright 1.61.1, headless, no launch arguments, located through `PLAYWRIGHT_BROWSERS_PATH` (set) |
| `recorded_at` | 2026-11-18T13:41:50Z |
| `recorded_by` | run and evidence: the checkpoint agent, Claude Opus 5.5 (`claude-opus-5-5`), xhigh reasoning effort; receipt: the Architect (sole spec writer), Claude Opus 5.5 (`claude-opus-5-5`) |

## Table 2. Modes

Run serially by the agent as the sole runner, `us1` first, then the other 20 modes the T011 acceptance ran. Each step ran `npm run --silent verify -- <mode>` under `setsid --wait`, which only scopes the own-process check; the commands were unchanged. The `tree_digest` and `tree_digest_after` of every row equal table 1's `tree_digest`.

| # | Mode | Owner | Decisive line (verbatim) | Exit | Window (UTC) | result.json sha256 | Log sha256 |
|---|---|---|---|---|---|---|---|
| 1 | `us1` | T011 | `verify: us1 PASS exit=0 files=6 tests=7 pass=7 fail=0 skip=0 todo=0 cases=6/6` | 0 | 13:23:18Z–13:24:27Z | 8d5b6ea0ef16c2dc691a88d4048ed47f91695e8c227185d62ce9e71d031acad0 | 4904dc6c110bc1af2da894b01c97adeb3214bbfee210ec075ed61133faa92d04 |
| 2 | `tooling` | T001 | `verify: tooling PASS exit=0 files=1 tests=38 pass=38 fail=0 skip=0 todo=0 cases=0/0` | 0 | 13:24:27Z–13:25:14Z | 1dcd874779f29973a775d27694d56f1f02c3d96489bc4272ba36a6c31b476920 | 49a0f008a6eccf88aa98ea6c0f05f4929c3c24084c9285675265adcf6be41f84 |
| 3 | `license-inventory` | T002 | `verify: license-inventory PASS exit=0 files=1 tests=23 pass=23 fail=0 skip=0 todo=0 cases=10/10` | 0 | 13:25:14Z–13:25:41Z | 08bb7e11f8675d1f395ade5ee625dc46bf7fcf4c756b0e0604aa48e4a38aa0c3 | 4e05683b0a2ca3318899cf9e0214690e4f889687cfd0a13417564941173cf76b |
| 4 | `qualification` | T027 | `verify: qualification PASS exit=0 files=1 tests=10 pass=10 fail=0 skip=0 todo=0 cases=5/5` | 0 | 13:25:41Z–13:25:56Z | 5f8aa2efefcb8e02a752c17747d20ca831ddb272acbf6cf0e3dba4cd9f41e2e5 | 317eabd99e206aea0d2118c0cb2716300cfd1bfefce6c366bc94abd62907d450 |
| 5 | `storage` | T003 | `verify: storage PASS exit=0 files=6 tests=29 pass=29 fail=0 skip=0 todo=0 cases=0/0` | 0 | 13:25:56Z–13:26:11Z | b76e8569dcb2c2f62dad6255705044d4346ed13ea7653a69b0f76e33bcc6a464 | 0cb4379bd93c588fbdac989c9518b5a9fb55bd28888d0c754fca24ef00edd711 |
| 6 | `blobs` | T028 | `verify: blobs PASS exit=0 files=1 tests=12 pass=12 fail=0 skip=0 todo=0 cases=4/4` | 0 | 13:26:11Z–13:26:19Z | 5df400c6efa3d5f02582edd1ce09c18906130ec1a093b23d4e7cd97682d1d528 | 605f3c0c7938b95615788ff4e4f5029dd980122b98dce9d903f0e858c860a494 |
| 7 | `audit-chain` | T029 | `verify: audit-chain PASS exit=0 files=3 tests=22 pass=22 fail=0 skip=0 todo=0 cases=5/5` | 0 | 13:26:19Z–13:26:35Z | 47b3665b9fc18a4b270ca390d25de4c0801ed2afad122ba96fddae041f6e7318 | 1983b3327549064e52818b43eb9ddc3b727dc718007ae57041a07e5c91496197 |
| 8 | `outbox-restore` | T030 | `verify: outbox-restore PASS exit=0 files=3 tests=41 pass=41 fail=0 skip=0 todo=0 cases=4/4` | 0 | 13:26:35Z–13:27:24Z | 31340c9f68f61dc29a0d479a1968c294d478cfb99e57525e9b47447d0a000f31 | ac62a259ce123d40f90e3096ada91e2e4f12c590fb8cf90ac3d1ab2d333ea3d8 |
| 9 | `dev` | T004 | `verify: dev PASS exit=0 files=5 tests=18 pass=18 fail=0 skip=0 todo=0 cases=3/3` | 0 | 13:27:24Z–13:27:43Z | 3fe951b44ad4f45c1946bb573eaec46b5ef91a277650dd058ad398b605aaa038 | 4e026853388d99810d338871b2ea872549f12050754b74b50ec2de201c2acb3c |
| 10 | `parser-limits` | T033 | `verify: parser-limits PASS exit=0 files=10 tests=45 pass=45 fail=0 skip=0 todo=0 cases=12/12` | 0 | 13:27:43Z–13:28:54Z | 2cfe6ba87be16ec81c5f9fafc9efaedb27f2506377a2770509640832af0f946f | d3fe104622622be04f4005541dd2bcf58922cfbed0aa68a15115dba380eb41e9 |
| 11 | `auth` | T005 | `verify: auth PASS exit=0 files=4 tests=20 pass=20 fail=0 skip=0 todo=0 cases=10/10` | 0 | 13:28:54Z–13:29:02Z | ba94676fbdbd39f92bb0bc500a450fcf40f27aba77f684bec130c9047cdb4b3a | 4ab54621cd006b7dd752cc01777809448ae1b2e897763062ccb456aea265b872 |
| 12 | `server` | T031 | `verify: server PASS exit=0 files=12 tests=47 pass=47 fail=0 skip=0 todo=0 cases=6/6` | 0 | 13:29:02Z–13:29:43Z | ab234d67f6990840ae14d32e7b27219e6fd0c3548dc59f9d009ee69cf82d3a8a | 4950c5e7283f2e594f25ea43171a81f27eba982c972817fa439326d9541a3456 |
| 13 | `notifications` | T032 | `verify: notifications PASS exit=0 files=4 tests=14 pass=14 fail=0 skip=0 todo=0 cases=5/5` | 0 | 13:29:43Z–13:29:52Z | 27aba18aceedd304c76f126bfcb0ac647b504c039b53bddf1e801b1a418bc7fa | f2f1f2678aedde58d39bf5b501041bdab24191105e8d75d1330b990477d7f1ce |
| 14 | `envelope-contract` | T006 | `verify: envelope-contract PASS exit=0 files=6 tests=93 pass=93 fail=0 skip=0 todo=0 cases=0/0` | 0 | 13:29:52Z–13:30:00Z | 70d085d3a7bed931d8d25904e7a76dca9d475f52fbab8aca16eec28a2775b694 | 3e178ea573f54b9d8639601587869fa9e925d1c3895387809649e3150385cb36 |
| 15 | `envelopes` | T007 | `verify: envelopes PASS exit=0 files=9 tests=40 pass=40 fail=0 skip=0 todo=0 cases=8/8` | 0 | 13:30:00Z–13:30:16Z | c173ae027e052f76cef6b37001e027d5ea99ef9b20a717cc82269ac2f9c3894c | b9c672dec66d3e7d2bd3e317e1adf30d53b401df3119bb3b153c04afa9f2fb03 |
| 16 | `recipients` | T008 | `verify: recipients PASS exit=0 files=9 tests=30 pass=30 fail=0 skip=0 todo=0 cases=11/11` | 0 | 13:30:16Z–13:30:42Z | 336dab691344a0730bf47831d4997c0fb798e0327aab248033c7860a98dd13e0 | 89d0f38ee08775b14ce8fada0610fcc900da8410fb166e480eea7d0e9ac79b8f |
| 17 | `envelope-api` | T034 | `verify: envelope-api PASS exit=0 files=8 tests=27 pass=27 fail=0 skip=0 todo=0 cases=5/5` | 0 | 13:30:42Z–13:31:24Z | 12b1a384105c79362d33b277229de9cb1692d638e2ba89bf48ceb5e4a00a2cc3 | aa3cdb2e9ace596a9b9adf8f43a64d5a0864cd7063f332c3db37d3119948f3d9 |
| 18 | `approval` | T010 | `verify: approval PASS exit=0 files=8 tests=25 pass=25 fail=0 skip=0 todo=0 cases=8/8` | 0 | 13:31:24Z–13:31:55Z | 83309fc4a035a014dd71dcfe641919c95dcade59b243d9c5d1dfda5cc770a340 | 8fa9fe4f0470abb36a932c20d788b70ac6cb64cc9c86103fe17ea91f5ca9c91f |
| 19 | `ui-build` | T035 | `verify: ui-build PASS exit=0 files=6 tests=17 pass=17 fail=0 skip=0 todo=0 cases=4/4` | 0 | 13:31:55Z–13:32:16Z | 484f8dc3b911aa617f129536674cf92e52804498fe118b961ffbf4cb2b7ddaef | 38037eca186e4f261231825c8049922e8b30ef9f4e5abc216d07a65999d9487f |
| 20 | `autograph` | T009 | `verify: autograph PASS exit=0 files=6 tests=21 pass=21 fail=0 skip=0 todo=0 cases=6/6` | 0 | 13:32:16Z–13:33:53Z | 1a69bf97c4b489d180d2fcac4738a4514d628a1e839495fe54154a940df9b35a | 42d35d18ad70234dc6a6ce5e84ca6440a43116eada8106d8d24366c5073fd725 |
| 21 | `no-secrets` | T026 | `verify: no-secrets PASS exit=0 files=1 tests=29 pass=29 fail=0 skip=0 todo=0 cases=7/7` | 0 | 13:33:53Z–13:34:12Z | 97a831539bbf327d1ebdf77ef83ba53b0413a79fc0243deba7bb47345d3f274a | 2200409667c9aeed4f07746f3421bbb0b8d235cac9f25f00f4f7dbad82f0892d |

Totals: 21 of 21 PASS with `exit=0`; 608 tests; 0 fail, 0 skip, 0 todo, 0 cancelled, 0 only; no failure entries; one digest across all 42 digest fields; Node `v24.22.2` in every result. `autograph` and `us1` record the chromium capability as present.

## Table 3. `us1` evidence

| Label (`.verify-out/us1/evidence.json`) | Value |
|---|---|
| `browser_engine` | chromium |
| `browser_version` | 149.0.7827.55 |
| `tree_digest` | 3ef7edefc6be72762b00e8845d841ea052d98bbea62dc59422ab7710a1cf9ac4 |
| file sha256 | 59f515e90d83be699638fde8f01d5c23e3e60f484607aef15bba7b97fa910734 |

The session cookie is recorded by its attributes only, with no value: `__Host-opensig_rs`, host-only `127.0.0.1`, Path=/, Secure, HttpOnly, SameSite=Strict, accepted by the server over loopback HTTP.

| Case ID | Leading `[ID]` of a passing test (file:line at `code_head`) | That file's result |
|---|---|---|
| SC004-01 | `tests/acceptance/us1/keyboard.test.mjs:34` | tests=1 pass=1 fail=0 skip=0 todo=0 |
| SC004-02 | `tests/acceptance/us1/mobile.test.mjs:41` | tests=1 pass=1 fail=0 skip=0 todo=0 |
| SC004-03 | `tests/acceptance/us1/consent.test.mjs:59` | tests=1 pass=1 fail=0 skip=0 todo=0 |
| SC004-04 | `tests/acceptance/us1/link.test.mjs:54` | tests=1 pass=1 fail=0 skip=0 todo=0 |
| US1-01 | `tests/acceptance/us1/two-recipient.test.mjs:65` | tests=1 pass=1 fail=0 skip=0 todo=0 |
| US1-02 | `tests/acceptance/us1/one-recipient.test.mjs:49 and :77` | tests=2 pass=2 fail=0 skip=0 todo=0 |

`passed_case_ids` lists all six, `cases` is 6/6 with none missing, and the six files hold the run's 7 tests.

## Table 4. Independent tree digest

| Item | Value |
|---|---|
| Method | A Python re-implementation of the runner's tree digest from `git ls-tree -r` and `git cat-file --batch` at `code_head`: `tree_digest.py`, sha256 1eefc8d4c4d764be113f015d6a010f1549b5338e407b6c5d22baa7732f4e742f |
| Output | `independent-digest.txt`, sha256 af25bfcfe6323010ed8a38bb0f34e8f59ce3bc8b60a37fd7381801bacf94d8db |
| Decisive line (verbatim) | `tree_digest=3ef7edefc6be72762b00e8845d841ea052d98bbea62dc59422ab7710a1cf9ac4` |
| Scope | 337 tree entries, of which 327 are in scope (all mode 100644); 0 non-ASCII paths |
| Equal to | the `us1` label (table 3) and all 42 per-mode digest fields (table 2): yes |

## Table 5. Typecheck

| Step | Command | Exit | Output (verbatim) | Window (UTC) | Log sha256 |
|---|---|---|---|---|---|
| 22 | `npm run --silent typecheck` | 0 | none (an empty log) | 13:34:12Z–13:34:21Z | e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855 |

## Table 6. Worktree, window, load and processes

| Item | Value |
|---|---|
| Worktree | A fresh detached worktree that the orchestrator made at `code_head`, with `tools/` and `node_modules/` copied in. Local HEAD was 79f1863570c79530809ba2bf03c084a400c04081 before and after. Porcelain was empty before and after. `.verify-out/` and `dist/` were absent before; `dist/` was absent after |
| Load gate | One wait before the whole batch, 13:21:18Z–13:23:18Z (4 × 30 s). `/proc/loadavg` went from 17.35 16.39 24.56 to 11.51 14.58 22.91. Verbatim: `gate: 1-min load 11.51 < 12 after 4 wait(s)` |
| Run window | 13:23:18Z–13:34:21Z, serial, `us1` first. The 1-minute load at each step's start and end stayed between 6.39 and 12.80 |
| Processes | 23 started: the batch script and one npm per step. None was alive afterwards, and every step recorded no leftover process in its own session |

## Notes (INFO, not counted)

- The tooling self-test (`tests/tooling/verification-runner.test.mjs:322`, CLI arguments `['--','tooling']`) writes a USAGE record, `.verify-out/_usage/result.json`, in the ignored outputs. It is not one of the 21 counted results.
- The hosted review gate (code#2) stays OPEN. It does not block the checkpoint, just as it did not block Phase 2's close.

## Evidence files (the checkpoint agent's evidence set, orchestrator workspace)

The per-mode `results/<mode>.result.json` and `logs/<mode>.log` files are hashed in table 2.

| File | sha256 |
|---|---|
| `run-batch.sh` | d4a1994ad832326768f579d2cb5618640225ae48777e806954a2948d7664fc4a |
| `tree_digest.py` | 1eefc8d4c4d764be113f015d6a010f1549b5338e407b6c5d22baa7732f4e742f |
| `independent-digest.txt` | af25bfcfe6323010ed8a38bb0f34e8f59ce3bc8b60a37fd7381801bacf94d8db |
| `summary.tsv` | 989a025c7d751e80d373cc5232826a28e8c3862835319bb9dea35e3447d8340b |
| `progress.log` | ad73ab560f1bf0fda0fcfbaf96238a14b4a41c5b1a7b8f46b4de9b37b86c5b45 |
| `collect.py` | 7cc42edadb450564a7e523b9a626a3d4853d3539b61217b95cd3faafecf5586b |
| `collect.out` | 77c4578c9fb0e9d20e6562ff4db4030978b269074aff7502b8c49bc18d288f05 |
| `results/us1.evidence.json` | 59f515e90d83be699638fde8f01d5c23e3e60f484607aef15bba7b97fa910734 |
| `logs/typecheck.log` | e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855 |

## Determination

| Item (tasks.md § "Phase 3 checkpoint, the US1 independent test") | Receipt | Holds |
|---|---|---|
| Who: an independent agent that wrote and accepted none of the US1 work, with its model checked | Sources: agent `a7d40472466dc4f48`, model check MATCH (107 of 107) | yes |
| On what: the corrected published head, by full SHA, confirmed by `ls-remote`, in a clean detached worktree | Table 1 `code_head`; table 6 | yes |
| What runs: `us1` first, then every mode of the T011 acceptance, then typecheck, serially | Tables 2 and 5 | yes |
| Every mode PASS with zero fail, skip or todo; `us1` cases 6/6 with chromium present | Table 2 totals; table 3 | yes |
| The `us1` labels | Table 3 | yes |
| The independent digest equals the label and every `tree_digest` and `tree_digest_after`; none null | Table 4; table 2 | yes |
| Each of the six case IDs as the leading `[ID]` of a passing test | Table 3 | yes |
| One decisive output line per mode, verbatim | Table 2 | yes |
| Typecheck's exit status; the pinned Node's version and binary sha256 | Table 5; table 1 `runtime` | yes |
| The run window and load; no process of the agent's own remains | Table 6 | yes |

**Open after close (non-blocking).** The hosted review gate (code#2) stays OPEN. The US1 rows still open stay as ledgered: AG06-RUN1, T009-STARTREAD and the next-touch rows.
