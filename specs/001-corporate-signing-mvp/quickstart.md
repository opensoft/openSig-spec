# MVP Implementation and Acceptance Runbook

Status: a planned acceptance procedure, not executed runtime evidence. No app or build command exists until its task creates it; T001 creates the pinned runtime and the frozen fail-closed runner used below.

Run implementation commands from the paired code worktree, never from the assembly root or the pinned `code/` mount. The runtime, runner statuses and mode registry are defined in [plan.md](plan.md). The shapes are in [contracts/http-api.yaml](contracts/http-api.yaml), and behavior is in [contracts/sessions-events-adapters.md](contracts/sessions-events-adapters.md).

## Feature selection

From the assembly root:

```sh
export SPECIFY_FEATURE=001-corporate-signing-mvp
export SPECIFY_FEATURE_DIRECTORY=worktrees/001-corporate-signing-mvp/spec/specs/001-corporate-signing-mvp
```

Before writing, inspect both worktrees, read the OpenSpec change and the feature tasks, and keep the foundation PR dependencies explicit.

## Runtime and commands (available after T001)

```sh
sh scripts/fetch-node.sh                 # GPG- and SHA-256-verified Node 24.22.2 into gitignored tools/
export PATH="$PWD/tools/node/bin:$PATH"  # the pinned node and npm come first
npm ci
npm run verify -- <mode>                 # one mode from plan.md § Mode registry; read the final status line
npm test                                 # = verify -- all-unit (unit, harness and contract modes without capabilities)
npm run verify -- acceptance-local       # local synthetic acceptance (T025); release_ready:false while any gate is OPEN
npm run verify -- acceptance             # release gate only; FAILS while any gate is OPEN; no task uses it
```

**How to read exit codes.**
- Only exit 0 with `PASS` counts.
- `CAPABILITY_ABSENT` (3) means environment-blocked. It applies when OpenSSL, the Playwright Chromium revision or the isolated Hermes image is missing.
- `VACUOUS` (4) means no real tests ran: missing tests, skips, todo or a missing case ID.
- Any other non-zero status is a failure.

There is no relax flag. The local dev launcher (`node scripts/dev.mjs`, T004) binds only `127.0.0.1`. A container composition is deferred, and no production deployment command exists.

## Golden flow (local synthetic)

1. Generate synthetic fixtures (T004): a multi-page corporate agreement, a rotated page, and two identities on `.test` domains. Start the local API with the dev mail spool. Development keys are generated at runtime outside the tree.
2. As a mapped sender (local test issuer, bearer token):
   1. `createEnvelope` returns the recipient IDs with their ordinals.
   2. `uploadDocument` with `If-Match` returns the page geometry.
   3. `prepareEnvelope` with fields keyed by `recipient_ordinal` freezes the document and records `document_digest` and `manifest_digest`.
   4. `readRecipientPreview`, then `sendEnvelope`.
3. For a Hermes-created envelope, the steps differ:
   1. Hermes calls `prepare_envelope`.
   2. A human operator calls `confirmSend` with the preview digests.
   3. Hermes calls `request_signatures` with that `authorization_reference`.
   4. Hermes never sees a link, code or the spool.
4. Recipient 1:
   1. Opens the mailed link `/sign#i=…` from the spool. Opening, reloading or prefetching changes nothing.
   2. Requests a challenge code, enters it, then reviews the exact PDF (browser viewer, schematic and download), the consent text and the seal notice.
   3. Signs (drawn or typed; the date is set by the server) and approves.
5. Recipient 2 cannot act before recipient 1. After recipient 2's approval the envelope shows `approvals_complete=true` in state `finalizing` ("fully approved"), not completed.
6. Wait for `completed`. That state appears only after the shipped verifier (`src/verifier/`) passes and the PDF and evidence are stored durably.
7. Retrieve through the API and through Hermes `retrieve_completed_package`. The package holds `final.pdf`, `approval-manifest.json`, `audit-receipt.json`, `verification.json` and `package-index.json`, all cross-linked by digests (contract schemas).
8. Check the result independently with the test oracle (`openssl cms -verify` over the ByteRange bytes, plus the independent structure checker).
   - A self-signed development certificate reports `trust: untrusted` while `integrity: valid`.
   - Revocation reports `not-checked` and the timestamp `absent`.
9. Copy the artifact, then flip a byte, append a revision or alter the `/Contents` padding in the copy. The modified copy is never accepted as the completed original. Never edit the retained original.

## Required negative and recovery evidence

| Evidence | Mode or source |
|---|---|
| SC-002 catalog SC002-01..SC002-26 | `recipients`, `approval`, `envelope-api`, `artifacts`, `verification` |
| Parser limits PL-01..PL-12 | `parser-limits` |
| Security headers and log redaction SEC-01..SEC-06 | `server` |
| Mail guard | `notifications` |
| SSRF-safe webhooks | `webhooks` |
| Twenty finalization fault and race cases SC003-01..SC003-20, in separate OS processes | `recovery` |
| Fencing cases | `completion` |
| Backup/restore with audit-chain continuity | `outbox-restore`, `operations` |
| Synthetic healthcare harnesses (no clinical attestation or financial action) | `healthcare-contracts` |
| Disabled and fake-failing anchoring with an empty network-guard log | `anchoring-disabled` |
| Ten golden flows and indicative-local timing | `us2-local` |
| Hermes round trip through isolated local Hermes v0.23.0 (scripted-deterministic model) | `us3` |
| Optional real-model attempt | `us3-real-model` (CAPABILITY_ABSENT when unavailable) |

## Receipt and release gates

`acceptance-local` (T025) writes a receipt bound to the tree digest. It records:
- the lockfile and SBOM digests;
- the exact runtime, verifier and oracle versions;
- the browser engine and version;
- case IDs and artifact digests;
- trusted and untrusted results and the timing report;
- the audit chain head;
- the backup/restore receipt.

It also lists every external gate as OPEN with `release_ready: false`. Failure outputs are preserved with secrets removed.

A passed local suite is not CI, deployed acceptance, PHI readiness, public certificate trust or legal effectiveness.

The release gate `acceptance` stays failing until all of these are closed with evidence:
- an external PDF reader check;
- declared pilot hardware timing;
- a real CI run;
- an approved trusted certificate and key custody;
- validated OIDC and SMTP;
- the PDF parse worker's network-namespace sandbox shown active in the deployment environment (`parser-network-sandbox`); local runs only record the observed capability;
- configured retention;
- owner-authorized environment and notifications.

The platform license question is resolved by the owner (plan.md). Platforms are inventoried separately (`docs/platform-inventory.md`) and are not a release gate; the application gate stays strict.

Production deployment, live healthcare integration and blockchain need separate, later authorization.
