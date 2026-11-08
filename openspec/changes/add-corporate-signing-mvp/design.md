## Context

The Triad foundation is in three open PRs. This change is proposed first-round MVP behavior, not an implementation claim. The existing brainstorm is non-normative. All original work is Apache-2.0; MIT/Apache-only shipped materials are a hard gate.

## Goals / Non-Goals

Goals: one PDF, one or two sequential human recipients, authenticated consent/approval, visible signature/date fields, final organizational seal, independent verification, evidence download and a bounded Hermes pilot. Separate tenant access and use synthetic data during qualification.

Non-goals: personal PKI enrollment, parallel signing, reusable signature-image vault, live blockchain, patient/clinical production workflows, billing/payment mutations, notarization, archival PAdES-LT/LTA and production cutover. Existing digitally signed/encrypted PDFs are rejected in MVP rather than invalidated silently. Trusted timestamp capability is optional and reported separately; it never receives an invented timestamp or trust result.

## Decisions

Use a standalone TypeScript service on Node 22 as the proposed implementation target, with a framework-neutral HTTP contract and browser UI. Use native HTTP where practical to limit dependencies. Candidate components are PDF.js, signature_pad, pdf-lib and @signpdf; pin versions only after transitive license/security/functional qualification. A separate signer worker owns certificate access; independent verifier output is mandatory. No candidate is considered qualified by its top-level license. If the allowlist or verification matrix fails, record a design amendment before substituting the engine.

Pilot persistence is SQLite through an explicitly qualified MIT/Apache driver plus immutable filesystem blobs, transaction/outbox and content digests. SQLite platform/native build dependencies must be inventoried too; qualification failure blocks this choice. One service instance is supported initially. Container/base-image inventory is explicit; do not silently exempt operating-system licenses or claim an entire image meets the policy. No release image is approved until its scope and full inventory satisfy owner policy.

Operators authenticate through OIDC with configured issuer/audience and tenant-role mapping. Recipients exchange an expiring invitation token and a separately issued email challenge for a short-lived bound session. Use HttpOnly/Secure/SameSite cookies, CSRF protection and no bearer token in analytics/referrers. Test mail spool is development-only; SMTP delivery and end-to-end recipient access must be verified for the pilot. Email challenge establishes mailbox control, not legal identity or delegated authority. Record assurance and require operator confirmation of recipient roles.

One immutable base revision and canonical field manifest are frozen before sending. Each sequential approval binds base hash, current manifest digest, prior approval-chain digest, signer/role, consent version and submitted field values. Finalization composes only those inputs, hashes output and applies one organization seal after all approvals. This is not multiple personal certificate signatures. Later edits require void/reissue; any PDF modification after completion fails exact-artifact matching even if an older signature still verifies.

Use unique idempotency records, expected-version transitions and a transactional outbox. A crash between blob write and database completion resumes against the same artifact/job; completion is committed once, only after verification and durable storage. Signing failure cannot produce completed status. Anchoring is disabled by default; fake receipts are conspicuously non-ledger evidence.

Development certificates are synthetic/self-signed and explicitly untrusted. Production certificate/key custody, trusted timestamps, retention and deployment approval remain release prerequisites. The initial pilot must not reuse AI-token vaults or expose private keys to Hermes. A secure file-backed test key adapter is permitted only in synthetic development, with ephemeral secrets outside Git; a qualified managed signing-key adapter is required before a production pilot.

## Risks / Trade-offs

Mailbox control is weaker than verified identity -> display assurance and do not claim notarization. PDF parsing is hostile-input processing -> isolate workers, disable scripts/external loads, set limits. Shared organization sealing does not identify individuals cryptographically -> retain approval manifest and label signer type. SQLite/single instance limits availability -> pilot only; backup/restore and explicit scaling migration before wider use. Dependencies or container components may fail license policy -> hard stop and documented design revision, no automatic waiver.

## Migration Plan

Build and test locally with synthetic data, qualify CI artifacts, then stage the complete flow. Keep production deployment separately authorized. Preserve evidence before any rollback; never downgrade/mutate completed artifacts. Initial migrations are versioned and tested for backup/restore. Foundation PRs are explicit dependencies; no implicit merge. Archive only after implementation and final assembly pin advancement land.

## Open Questions

Operational certificate issuer, OIDC configuration, SMTP sender, pilot host, retention period and immutable-storage mode are deployment-time inputs, not unresolved product behavior. Engine/driver pins and release-image eligibility are implementation qualification gates, with explicit stop conditions. Detailed tasks live only in the linked Speckit feature.
