# Implementation Plan: Corporate Signing MVP

**Branch**: `001-corporate-signing-mvp` | **Date**: 2026-11-08 | **Spec**: [spec.md](spec.md)

## Summary

Deliver one corporate signing flow with openAutograph human approval and an openCryptograph organizational PDF seal; then expose bounded Hermes tools and synthetic openChart/openPractice contracts. No production deployment or live anchoring is included. [Tasks](tasks.md) is the sole executable checklist.

## Technical Context

Language: TypeScript targeting Node 22; browser HTML/CSS/TypeScript. Proposed dependencies: PDF.js, signature_pad, pdf-lib, @signpdf, qualified OIDC and SQLite adapters. Exact pins and transitive/runtime eligibility are qualification gates, not claimed results. Use native HTTP and Node test runner where practical; TypeScript compiler/tool dependencies are inventoried. Pin the runtime and compiler during T001/T002; do not substitute latest silently.

Storage: single-instance SQLite transactions/outbox, immutable filesystem blobs behind an object-store interface; driver selection must pass full allowlist and crash/recovery tests. Development keys are ephemeral self-signed fixtures; production managed key access is a separate rollout gate. Target: local/staged Linux service and browser client, not a provisioned AKS deployment. UI supports keyboard and mobile; no CDN/font loading from unqualified external sources.

Scale: one 10 MiB/50-page PDF, one or two sequential recipients, ten concurrent envelopes. Status/review navigation p95 <=2 seconds and finalization <=60 seconds in declared hardware; exclude user/email latency. Retain finalization jobs with bounded exponential retries and visible failure, never silent completion.

Testing: Node unit/contract/integration suites, browser acceptance, independent PDF verifier and tamper corpus. Versioned test evidence contains source, dependency lock/SBOM, environment and artifact digests. No build command below is claimed available until its implementation task creates it.

## Constitution Check

Planning passes owner constraints: Apache-2.0 originals, MIT/Apache-only shipped materials; human approval separation; verified completion; synthetic fixtures; Triad placement and no implicit deployment/merge. Full shipped dependency/image eligibility is NOT proven; T002 is a blocking gate. The root's planning constitution reflects existing constraints, not a newly ratified policy. Recheck after qualification and before release. Record the baseline in this feature so it travels with the spec leg.

## Research and Design

See [research](research.md), [data model](data-model.md) and [contracts](contracts/http-api.yaml). Operator OIDC and recipient mailbox challenge are separate assurance domains. Challenge state and invitation binding prevent a forwarded link alone from constituting approval. Single seal over all approval values is the MVP; individual certificate signatures and parallel composition remain out of scope.

Finalization is a job/outbox transaction: reserve expected envelope version, compose into immutable temporary content-addressed storage, seal, independently verify, durably store PDF/evidence, then atomically publish one artifact and completion event. Void can win only before finalizing reservation; later cancellation returns conflict and cannot claim the PDF was revoked. Failures retain finalizing with retryable/failed job status; operator retry never creates new approvals. A compare-and-set state/version and uniqueness constraints prevent duplicate completion.

## Project Structure

Spec feature owns spec.md, plan.md, research.md, data-model.md, contracts/, quickstart.md, tasks.md, checklists/ and analysis.md. The OpenSpec proposal/delta remains in the spec leg and hands off here. Code implementation is in the paired code worktree:

```text
src/api/                 HTTP handlers, auth and input limits
src/domain/              envelope/approval/version rules
src/storage/             transactions, blobs and outbox
src/autograph/           browser review/consent/signature UI
src/cryptograph/         isolated compose/sign/verify worker and key adapters
src/integrations/        Hermes tools, synthetic healthcare consumers
src/anchoring/           disabled and fake adapters only
scripts/                 qualification, verification and release receipts
tests/                   unit, contract, browser, integrity and recovery suites
fixtures/synthetic/      generated documents and identities only
```

## Governed invariants

- `src/domain/` — approval/version/state transitions and tenant isolation.
- `src/api/auth/` — operator/recipient/service authority.
- `src/cryptograph/` — key custody, verification and completion integrity.
- `src/storage/` — durable evidence and exactly-once publication.
- `src/integrations/` — domain authority and notification permissions.
- `src/anchoring/` — privacy and no real ledger activity.
- `package-lock.json` — pinned dependency inventory.

## Delivery and Landing

T001 establishes test/verify commands; T002 must pass before building atop selected dependencies. US1 delivers human approval visibly short of sealed completion, US2 completes the signing MVP, US3 proves Hermes reuse and healthcare contracts. Run synthesis/security/recovery acceptance before claiming the whole feature complete. Keep all implementation tasks unchecked until executed.

The feature branches depend on foundation PRs openSig#1, openSig-spec#1 and openSig-code#1. A stacked spec PR targets the spec foundation branch until it lands. Code worktree exists but contains no new implementation. Do not create an empty implementation PR. After actual implementation, review/land both legs, advance matching assembly pins on the feature branch and only then archive. No assembly feature pin is advanced by this planning work.
