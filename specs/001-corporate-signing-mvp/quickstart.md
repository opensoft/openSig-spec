# MVP Implementation and Acceptance Runbook

Status: planned acceptance procedure, not executed runtime evidence. No app/build commands exist yet. T001 creates the package scripts and fail-closed verification runner used below. Run implementation commands from the paired code worktree, not the assembly root or pinned code mount.

## Feature selection

From the assembly root export SPECIFY_FEATURE=001-corporate-signing-mvp and SPECIFY_FEATURE_DIRECTORY=worktrees/001-corporate-signing-mvp/spec/specs/001-corporate-signing-mvp. Inspect both worktrees before writing. Read the OpenSpec change and feature tasks. Keep the foundation PR dependencies explicit.

## Planned commands

After T001/T002 implement and qualify them: npm ci; npm run verify -- qualification; npm test; npm run verify -- acceptance. Each verifier mode must fail on missing evidence, skipped required cases or absent runtime. Do not implement a success-only placeholder. Synthetic compose/start commands and exact ports are established in T004, then documented in the code README. No production deployment command is supplied by this planning work.

## Golden flow

Generate a synthetic multi-page corporate agreement and two synthetic identities. Start the local single-instance API, UI, signer worker and test mail spool. Log in as mapped sender; create/upload/prepare and record base/manifest digests. Preview recipient roles and document hash. Send only to local synthetic mail recipients. Exchange first recipient's invite/challenge, review with keyboard and mobile layout, explicitly consent and sign/date. Confirm second recipient cannot sign first. Complete second approval, wait for independently verified seal and retrieve package through UI and actual Hermes tool calls in an authorized test environment.

Confirm final package has final.pdf, approval-manifest.json, audit-receipt.json and verification.json, all cross-linked by digests. Verify externally with the qualified independent verifier and a separate PDF reader. Self-signed test cert reports untrusted even when integrity is valid. Make a copy, alter bytes/add a revision and confirm the modified artifact cannot be accepted as the completed original. Never edit the retained original.

## Required negative and recovery evidence

Exercise every FR-001..FR-012 case, invite scanners/GET without approval, wrong recipient/order, expired/reissued/replayed challenge, CSRF/origin failure, cross-tenant guessed IDs, mismatched idempotency payload, stale source version, malformed/encrypted/already-signed/oversized PDF, field outside rotated page, signer key failure, verifier outage, crash before/after storage and before completion commit, full disk, duplicate webhook/SSRF target and concurrent void/finalize. Twenty injected recovery/race cases must produce one valid outcome each.

Run both synthetic healthcare harnesses and show they cannot attest a clinical encounter or perform financial actions. Disable anchoring, then simulate fake failure: ordinary signing still completes and no real chain request occurs. Run ten golden flows and the stated ten-concurrent-envelope timing profile; record environment and exclude email/user delay.

## Receipt and release gates

Capture source commit, lockfile/SBOM digest, exact runtime/compiler/verifier versions, environment, case IDs, artifact hashes, trusted/untrusted results, browser accessibility observations, timing report and backup/restore receipt. Preserve failure outputs with secrets removed. A passed local suite is not CI, deployed acceptance, PHI readiness, certificate public trust or legal effectiveness.

Before any real corporate pilot: owner-authorized environment/notifications, validated OIDC and SMTP, managed key custody, approved certificate/trust policy, configured evidence retention and backup/restore. Before production: separate deployment authorization and artifact license inventory including runtime/container contents. Live healthcare and blockchain need later governed features.
