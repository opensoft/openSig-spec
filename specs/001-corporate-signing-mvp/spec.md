# Feature Specification: Corporate Signing MVP

**Feature Branch**: `001-corporate-signing-mvp`  
**Created**: 2026-11-08  
**Status**: Draft for review; no implementation or production approval  
**Input**: User requested the first-round openSig MVP feature documented through the prescribed Triad OpenSpec/Speckit workflow.  
**Governance**: [proposal](../../openspec/changes/add-corporate-signing-mvp/proposal.md)

## Clarifications

### Session 2026-11-08

- Q: Is the recipient invitation link (or any challenge code or session value) ever returned to Hermes or any other tool or service caller? → A: No. Invitations go to the recipient's mailbox only. No API response, tool result, log, audit event or operator/service notification contains a link, token or code, and Hermes has no access to the mail spool. Governing principles: P2 (human personal approval cannot be delegated to Hermes service credentials) and P4 (secrets kept out of Git and logs).
- Q: Can an envelope that Hermes (a service principal) created be sent without a human? → A: No. A human operator of the tenant confirms the recipient preview (document, manifest and recipients digests). That creates a single-use `authorization_reference`, which `request_signatures` must present. Hermes can never create the confirmation itself. Governing principle: P2.
- Q: What consent evidence is persisted: the version label, or the wording? → A: The exact wording. Consent text is an immutable, versioned entity with a SHA-256 text digest. Each approval binds both `consent_version` and `consent_text_digest`, and the approval manifest in the package carries the full text. Governing principle: P2 (preserve exact-version consent evidence).
- Q: What does "fully approved" mean in the state model, including the one-recipient case? → A: `approvals_complete = true`, meaning every required approval is recorded. In the same transaction the envelope moves to `finalizing`, and only verified durable output moves it to `completed`. A one-recipient envelope goes `sent → finalizing` on its single approval. `in-progress` exists only between the first and last approvals of a two-recipient envelope. "Fully approved" is never shown as completed or sealed. Governing principle: P3 (verify before completion; separate human authority from certificate integrity).
- Q: Who supplies the value of a date field? → A: The server, from server UTC time at approval, labeled unsigned server time. Clients submit no date value. Governing principles: P2 (exact approval evidence) and P3 (a server time is never presented as a trusted timestamp).
- Q: How are typed signatures and dates rendered without shipping a font file? → A: In standard-14 Helvetica, which is not embedded. Characters outside WinAnsiEncoding are rejected (422 `unsupported_characters`). Governing principle: P1 (no third-party font asset ships outside the MIT/Apache allowlist).

## User Scenarios & Testing

### User Story 1 - Review and approve a corporate document (Priority: P1)

An authorized sender prepares one PDF and one or two sequential recipients. Each recipient reviews the frozen document, gives explicit consent and places a visible signature/date. The sender sees approval progress without being able to approve as the recipient.

**Why this priority**: This establishes the human action that all later protection depends on.

**Independent Test**: A synthetic two-recipient document reaches fully approved status; a second recipient cannot approve before the first, and neither sender nor service account can impersonate either approval. Fully approved is visibly distinct from completed/sealed.

**Acceptance Scenarios**:

1. Given a draft with valid fields, when prepared, then its document and field manifest freeze; when sent, only the first recipient can approve.
2. Given a frozen document, when a recipient authenticates mailbox control, reviews and explicitly consents, then approval records its version, field values, role, the exact consent text version and digest, and assurance.
3. Given expired/revoked access or a mismatched version, when approval is attempted, then it fails without recording approval.

### User Story 2 - Obtain and verify a protected package (Priority: P1)

After all required approvals, the sender obtains a visible signed PDF protected by the organization's certificate and an evidence package identifying each human approval.

**Why this priority**: A usable MVP must deliver independently verifiable output, not only image placement.

**Independent Test**: From a fully approved fixture, seal, verify, download and independently validate the exact artifact; modification is detected and an untrusted certificate is not presented as trusted.

**Acceptance Scenarios**:

1. Given all approvals, when finalization succeeds, then completed status appears only after cryptographic verification and durable PDF/evidence storage.
2. Given a self-signed development certificate, when verified, then integrity can pass while certificate trust remains explicitly untrusted.
3. Given retry or worker interruption, when recovery runs, then at most one completed package is published and failures remain visible/recoverable.

### User Story 3 - Use signing through Hermes and shared consumer contracts (Priority: P2)

An authorized user asks Hermes to prepare/request a signature, follow progress and retrieve the protected result. Healthcare maintainers can test the same contract with synthetic source records without changing live patient/billing workflows.

**Why this priority**: Proves the intended first consumer while preserving reusable boundaries.

**Independent Test**: Actual Hermes tool calls complete a synthetic human-approved flow; openChart/openPractice harnesses deduplicate and reject stale source-version attachments.

**Acceptance Scenarios**:

1. Given authorized preparation and a human operator's confirmation of the recipient preview, when Hermes requests signing, then the human recipient gets a usable link by mail only; Hermes never receives the link, cannot call human approval and cannot access signing keys.
2. Given duplicate or mismatched completion, when a consumer handles it, then no duplicate attachment, clinical attestation, charge or claim action occurs.
3. Given no configured live ledger, when signing completes, then it succeeds normally; fake evidence is never labeled blockchain-confirmed.

### Edge Cases

Void versus approval/finalization races; duplicate idempotency key with changed payload; out-of-order recipients; link consumption by email scanners; browser refresh/back; consent refusal; malformed/encrypted/already-signed PDF; rotated pages; retry after evidence write; storage full; expired/untrusted/revoked certificate; verification unavailable; stale callbacks; cross-tenant guessing; fake anchoring failure. Viewing or prefetching a link must not consume approval.

## Requirements

### Functional Requirements

- **FR-001**: Isolate tenants and enforce sender, recipient and service-account capabilities on every document/action.
- **FR-002**: Support one PDF of at most 10 MiB and 50 pages, one or two sequential recipients and signature/date fields; reject malformed, encrypted or already digitally signed input before sending.
- **FR-003**: Freeze document bytes and field definitions before sending; bind explicit consent and each approval to the document, manifest, prior approval and intended recipient. Editing requires void/reissue.
- **FR-004**: Require recipient mailbox-control authentication plus a bound invitation, enforce expiry/revocation/order and prevent sender/service-account human approval. Record assurance without claiming independently verified legal identity.
- **FR-005**: Compose approved visual values, apply an organization certificate seal, independently verify integrity/revision coverage and store exact bytes and evidence before completed status.
- **FR-006**: Distinguish integrity from certificate trust, organization seal from personal certificate signature, and actual timestamps from unsigned server times; do not invent unavailable trust/revocation results.
- **FR-007**: Provide draft/prepared/sent/in-progress/finalizing/completed/declined/expired/voided outcomes, bounded recoverable finalization failure, idempotent mutation and deduplicated completion events.
- **FR-008**: Retrieve authorized final PDF, approval manifest, audit receipt and verification report without exposing other tenants, signing secrets or tokens in logs/notifications.
- **FR-009**: Support bounded Hermes preparation/request/status/retrieval and synthetic openChart/openPractice version-bound contracts; completion cannot attest clinical facts or execute financial actions.
- **FR-010**: Keep anchoring disabled or fake only in MVP; no real wallet/fees/network submission; fake receipts explicitly say non-ledger.
- **FR-011**: Use Apache-2.0 for original materials and fail release on unknown/disallowed third-party licenses, including transitive assets and release-image inventory; preserve notices.
- **FR-012**: Demonstrate independent verification, tamper detection, tenant/session/replay protection, worker recovery, evidence retention controls and backup/restore using synthetic fixtures before pilot acceptance.

### Key Entities

Tenant; Envelope; DocumentRevision; FieldDefinition; FieldManifest; Recipient; ConsentText; SendConfirmation; Invitation; Challenge; RecipientSession; Approval; SigningJob; FinalArtifact; VerificationReport; AuditEvent; AuditCheckpoint; OutboxEvent; WebhookRegistration; ConsumerAttachment; AnchorReceipt. Exact validation/transitions are in [data model](data-model.md).

## Success Criteria

- **SC-001**: Ten consecutive synthetic one/two-recipient flows produce one retrievable verified package each; every approval references the displayed revision.
- **SC-002**: All defined wrong-recipient, stale-version, cross-tenant, expired/replayed session and modified-PDF tests reject or report invalid without completing the wrong envelope.
- **SC-003**: Twenty scripted finalization retry/crash/concurrency cases produce no duplicate package or incompatible completed/voided state.
- **SC-004**: A user completes review/approval on keyboard-only and mobile layouts, with consent and organization-seal wording visible; no signature action occurs from opening a link alone.
- **SC-005**: In the declared single-instance pilot environment, 95% of status/review navigation operations finish within 2 seconds and supported finalization within 60 seconds across 10 concurrent envelopes; report hardware, PDF sizes and measurement separately from authentication/email delay.
- **SC-006**: One actual Hermes-mediated flow and both synthetic healthcare consumer harnesses pass; signing still completes with anchoring disabled/failing fake adapter.
- **SC-007**: Every distributed artifact has a passing license inventory and provenance receipt; no real PHI, signatures or keys enter Git/test fixtures.

## Assumptions and Scope

Corporate pilot first; one final organization seal after human approvals, not personal certificate enrollment or parallel signing. Drawn and typed signatures supported; upload/reuse vault deferred. No production deployment, live healthcare adapter, legal execution or ledger spending is authorized by planning. Trusted timestamps and managed key custody are supported extension points; synthetic self-signed keys are development-only and honestly labeled. Operational issuer/identity/SMTP/retention inputs are required before a real pilot. Meaningful automated security/integrity/recovery tests are explicitly required.
