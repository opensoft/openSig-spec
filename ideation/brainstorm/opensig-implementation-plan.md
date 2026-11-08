# Implementation Plan — Brainstorm

Status: brainstorm
Kind: plan
Summary: Qualify a permissive signing core, deliver human approval and cryptographic protection, then add three adapters and optional anchoring.
Topics: opensig, implementation-plan, acceptance-gates
Repository context: openSig; Hermes, openChart and openPractice delivery
Captured: 2026-11-08

## Possible feats

- **Evidence-based releases** — every phase proves source, artifact and actual user workflow separately.

## Delivery sequence

The owner has selected the Triad: openSig assembly root with openSig-spec mounted at spec/ and openSig-code mounted at code/. This packet and canonical OpenSpec/Speckit work belong in the spec leg; implementation and consumer adapters belong in code. Phase 0 starts by scaffolding this new project and importing the preserved packet. See [Triad setup](../../TRIAD-SETUP.md) for placement, checks and pending organization/visibility.

| Phase | Implementation tasks | Dependencies and exit evidence |
|---|---|---|
| 0: Foundation | Confirm repository owner/name availability; Apache license/NOTICE; threat model; full MIT/Apache SBOM gate; engine comparison; runtime/storage/identity selection; OpenAPI and manifest/proof schemas; certificate/TSA/key strategy | Pinned license inventory, ADRs, synthetic sign/verify/tamper matrix and independent-reader results. No unresolved dependency license blocker. |
| 1: openSig + openAutograph | Tenant/envelope/revision model; template/field UI; accessible review/consent; recipient authentication; session expiry/revocation; immutable approval; outbox/webhooks; upload isolation | Phase 0 contracts. Two authorized synthetic signers complete approval; replay, stale revision, cross-tenant and expired-link cases fail safely; keyboard/mobile review demonstrated. |
| 2: openCryptograph | Isolated signing worker; remote-key adapter; approved composition; certificate seals/signatures; scoped incremental signing; timestamps; independent verification; durable evidence and recovery | Phases 0–1. External verification succeeds, tampering is detected, untrusted/revoked certificates reported correctly, crash/retry yields one completion, key rotation/restore demonstrated. |
| 3: Hermes pilot | Bounded tools; approved templates; recipient/hash preview; authorized delivery; human signing link; status and package retrieval | Phase 2. Actual Hermes tool round trip and human approval; verified download; no approval bypass or key leakage. |
| 4: openChart | Governed Frappe extension; patient/proxy and version binding; consent UI; immutable document/provenance references; amendment handling; PHI readiness | Phase 2 and supported openChart runtime. Real synthetic Frappe workflow, authorization/replay/stale-record tests and preserved clinical authority. Operational privacy/security gate before PHI. |
| 5: openPractice | Contract harness now; administrative-agreement extension and UI when application foundation exists | Phase 2; actual app foundation for runtime proof. Correct practice/account attachment; duplicates harmless; no unauthorized payment/claim transitions. Contract tests alone are not app acceptance. |
| 6: Optional anchoring | Disabled/fake adapter; commitment/nonce/Merkle schemas; ledger adapter; budget controls; finality/reorg observer; portable/offline proof verifier | Stable Phase 2 artifact contract plus explicit ledger policy. Independent proof succeeds; byte/nonce/path tampering fails; outage/reorg/retry tests pass; signing works with anchoring disabled. |
| 7: Release operations | CI and SBOM gates; parser limits; audit/redaction; certificate expiry and worker alerts; backup/restore; retention; staged rollout and rollback | Applicable consumer phases. Source/image-bound receipts, staged artifact qualification and actual authorized production acceptance; health/readback alone is insufficient. |

Phases 3–5 can advance independently after their prerequisites. Core delivery does not wait for openPractice's missing application. Anchoring is never required for ordinary signing. Estimate effort and assign named owners after phase 0; no delivery dates are invented here.

## Validation matrix

Use synthetic fixtures only. Cover malformed/encrypted PDFs, scripts/attachments, parser resource exhaustion, Unicode/fonts, page rotations and field positions, multiple signers, concurrent approve/void/finalize, retry/idempotency, protected revision coverage, trust/revocation/TSA outages, tenant/object authorization, webhook replay, token leakage and log redaction. Preserve source commit, image digest, SBOM, environment, case IDs, artifact hashes and independent verification output for each release.

## Operational plan

Proposed home is the existing Opensoft platform, but discover the exact current subscription/cluster/namespace/capacity before provisioning. Use dedicated signing identity, key access and storage; do not reuse AI token vaults. Pilot synthetic corporate documents, then authorized real corporate documents, then healthcare after its separate readiness gate. Blockchain keys and spend limits remain separate.

Responsible roles: platform maintainer for core/runtime, security/key custodian for trust, consumer maintainers for adapters/domain rules and product owner for consent/templates/retention. Named assignees remain open.

## Open decisions

Repository publication, core framework, engine, certificate/TSA provider, authentication assurance, parallel-signing scope, retention and recovery objectives. Blockchain selection/budget can wait until phase 6. This document is a proposed plan, not implementation or production authorization.

## Relationships

[Licensing](opensig-licensing.md); [workflow/adapters](opensig-workflow-adapters.md); [anchoring](opensig-anchoring.md); [platform synthesis](opensig-synthesis-platform.md).
