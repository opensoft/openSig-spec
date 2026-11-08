# Envelope Workflow and Consumer Contracts — Brainstorm

Status: brainstorm
Kind: architecture
Summary: One versioned envelope API coordinates evidence while consumer adapters preserve clinical, financial and human authority.
Topics: opensig, envelope-workflow, application-adapters
Repository context: openSig; Hermes, openChart and openPractice
Captured: 2026-11-08

## Possible feats

- **Recoverable integration** — retries do not duplicate signatures or downstream business actions.

## Records and lifecycle

Proposed records: Tenant, Envelope, DocumentRevision, Recipient, FieldDefinition, SigningSession, ApprovalEvent, FinalArtifact, VerificationReport, AuditEvent, WebhookDelivery and AnchorReceipt. Store source application/record/version, exact-byte SHA-256 digest, schema-versioned composition manifest and tenant-scoped storage references.

Lifecycle: draft → prepared → sent → in-progress → finalizing → completed; declined, expired and voided are distinct terminal outcomes. Preparation freezes inputs. Completion requires verified artifact and durable evidence. Failed jobs retry without generating competing completions. Anchoring has independent disabled/pending/confirmed/failed/reorged states.

API v1 should define create/upload/prepare/send, recipient-session approval, void, status/artifact/verification retrieval and optional anchor request. Specify OpenAPI schemas, idempotency keys, conditional revision checks, error codes and rate/size limits. Human approval is not a service-account capability.

Use tenant-scoped authorization at database/object/worker boundaries, hashed session tokens, compare-and-set state changes, transactional outbox and idempotent webhook consumers. Signed events carry event ID, schema version and replay protection; consumers fetch canonical state. Minimize notification payloads and exclude PHI from URLs, email subjects and telemetry. Isolate PDF parsers and constrain memory, runtime, network and upload size.

Immutable storage and restricted append-only evidence writers supplement hash chaining: an administrator able to rewrite the entire chain defeats chaining alone. Define retention, deletion, backups, access audits and recovery objectives.

## Hermes adapter

Expose prepare, populate-template, request-signatures, status and retrieve-package tools. Anchor requests need separate permission. Enforce tenant/template/recipient scope and show exact document/hash before authorized sending. Treat PDF contents as untrusted data, never agent instructions. Personal approval cannot be impersonated by an agent; automatic organizational sealing needs an explicit scoped policy. Preparing a draft does not itself authorize sending messages.

Acceptance: actual Hermes tool round trip with synthetic document, authorized request, human approval and independently verified download. Tool registration or health alone is insufficient.

## openChart adapter

Freshly inspected README describes an original Frappe v15 application, Python 3.11/MariaDB 10.6, with guarded versioned intake APIs and succession-based amendments. Add a governed extension rather than inventing existing signature endpoints or bypassing controllers. Bind patient, signer/representative authority and source record version. openChart owns clinical facts, attestation, provenance and amendments; a webhook cannot independently attest an encounter. Amendments create successor documents and new approval.

Acceptance: real synthetic Frappe workflow, immutable evidence attachment and duplicate/stale/cross-patient/unauthorized-representative tests. Real PHI use requires appropriate access, retention, operational and vendor arrangements first.

## openPractice adapter

Freshly inspected README says the application/data model is not yet implemented; package open_practice is planned. Build contracts and a synthetic harness first, then actual DocTypes/UI against its governed foundation. Use administrative agreements, financial policies and authorizations. Completion cannot independently charge, submit claims or alter clinical records.

Acceptance: actual app flow when its foundation exists, correct practice/account binding, replay handling and rejection of unauthorized financial transitions. Label contract-only checks separately from runtime acceptance.

## Open questions

Choose SSO/authentication, patient proxy policy, evidence retention, storage and supported consumer/API versions. No consumer source files are changed by this packet.

## Relationships

[Trust synthesis](opensig-synthesis-trust.md); [platform synthesis](opensig-synthesis-platform.md).
