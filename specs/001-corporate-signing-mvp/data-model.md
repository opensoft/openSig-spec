# Data Model and State Contract

Revision 2026-11-08 (M4). The API shapes are in [http-api.yaml](contracts/http-api.yaml) and behavior is in [sessions-events-adapters.md](contracts/sessions-events-adapters.md).

## Global rules

- **Identifiers.** All identifiers are opaque (`^[A-Za-z0-9_-]{16,64}$`), tenant-scoped and server-generated. A caller-supplied ID never implies a tenant.
- **Time.** Server time is UTC. Client timestamps never establish approval time. Server times are labeled "unsigned server time" and are never presented as trusted timestamps.
- **Versions.** Schema versions and entity versions are persisted. Every mutation compares and sets an expected version.
- **Digests.** SHA-256 over exact bytes, lowercase hex.
- **Canonical JSON** follows RFC 8785 (JCS), implemented originally and checked against the RFC 8785 test vectors before use. It applies to the field manifest, recipients digest, field values, approvals, audit events and receipts.
- **Persistence.** `node:sqlite` in a single instance. Transactions use `BEGIN IMMEDIATE` with a bounded busy timeout. Every row carries `tenant_id`, and every query is tenant-filtered by the storage layer, never by the caller.
- **Blobs.** Immutable, content-addressed and **tenant-namespaced**, with key `tenants/<tenant_id>/sha256/<digest>`. Writes go to a temp file, then fsync, then rename. Reads verify the digest. There is no cross-tenant deduplication.

## Entities

| Entity | Required fields and constraints |
|---|---|
| Tenant | id, identity mapping (issuer, audience, role claim to role, principal-kind claim), allowed roles, consent text in force, retention configuration (operational input; unset means retain) Retention configuration format, as implemented by T029 (c84988d; reconciliation note): `retention_json` = `{policy_ref, dispose_after_days, scope?: "documents" \| "all"}`. "archive" is not implemented, because it was never specified. |
| Principal (derived per request) | tenant_id, kind `human-operator` or `service`, roles ⊆ {sender, tenant_admin, service}, opaque ref. Classified from configured claims; a service principal can never hold `sender` or `tenant_admin` |
| Envelope | id, tenant_id, source app/record/version, state, monotonic version, created_by {kind, ref}, expires_at (default +7 days, maximum +30 days), recipients_count 1..2, base_revision_id, manifest_digest, approvals_complete, reissue_of / reissued_as; unique (tenant, idempotency key) with request digest |
| DocumentRevision | id, envelope_id, content_digest (the `document_digest` of the API), blob_ref, bytes ≤ 10 MiB, pages ≤ 50 with displayed geometry and rotate, parse report from the isolated parse service; immutable after prepare |
| FieldDefinition | key (unique per envelope), revision_id, recipient_id, recipient_ordinal, page, type `signature`/`date`, rect normalized to the displayed page (origin top-left, y downward, inside [0,1] and inside the page); each recipient has ≥ 1 signature field |
| FieldManifest | Canonical JSON `{schema, envelope_id, document_digest, fields}` frozen at prepare; `manifest_digest` = SHA-256 of its canonical bytes; immutable |
| Recipient | id, envelope_id, ordinal unique 1..2, email, operator-declared role, status (see below), invited_at, approved_at, approval_id |
| **ConsentText** | id, tenant_id, version (`^[a-z0-9.-]{1,40}$`), locale, exact text (UTF-8, ≤ 20000 chars), text_digest, effective_from. Immutable; a new wording is a new version. Each Approval stores consent_version **and** consent_text_digest, and the package carries the full text, so the exact wording a recipient saw can be reproduced |
| SendConfirmation | authorization_reference (id), tenant_id, envelope_id, envelope_version, document/manifest/recipients digests, confirmed_by (human-operator principal ref), confirmed_at, expires_at (min of +24 h and envelope expiry), consumed_at; single use; creation by a service principal is impossible |
| Invitation | id, recipient_id, revision and manifest binding, token hash (raw token ≥ 256 bits, mailed only), sent_at, expires_at (min of +7 days and envelope expiry), revoked_at, cumulative_wrong_codes (≥ 20 revokes the invitation) |
| Challenge | id, invitation_id, code_hmac (HMAC-SHA256 under a server key loaded from a 0600 file; code is 10 Crockford-base32 characters, 50 bits), expires_at (+10 min), attempts (≤ 5, then locked), consumed_at, superseded_at |
| RecipientSession | id, value hash (raw value only in the cookie), tenant/envelope/recipient/revision/manifest binding, csrf secret, created_at, last_seen_at (idle expiry 30 min), revoked_at |
| Approval | id, recipient_id unique, document_digest, manifest_digest, prior_approval_digest (null for ordinal 1), consent_version, consent_text_digest, typed field values (drawn strokes, typed text, server-filled dates), field_values_digest, assurance {mailbox-challenge, mailbox-control}, server_time, approval_digest = SHA-256(JCS(record without approval_digest)); immutable, tenant-bound |
| IdempotencyRecord | tenant_id, route, principal ref, key, request digest, response status/body digest, created_at; unique (tenant, route, principal, key) |
| SigningJob | envelope_id unique, reserved_version, status `pending`/`running`/`retryable`/`failed`/`succeeded`, attempt_count, max_attempts, **lease_owner, lease_expires_at, fencing_token** (= attempt_count at lease grant), retry_at (bounded exponential), last_error_code; no secret traces |
| FinalArtifact | envelope_id unique, artifact_id, artifact_digest (SHA-256 of final.pdf), package_digest (SHA-256 of the ZIP), approved_composition_digest, PDF/evidence/package blob refs, signer certificate SHA-256, development_certificate flag, verifier name/version/results, fencing_token of the publishing attempt; immutable |
| VerificationReport | artifact_digest, integrity, trust, trust_anchor_ref, revision_coverage, seal_type `organization`, timestamp_status (`absent` until implemented), revocation_status (`not-checked` until implemented), verifier, verified_at, checks; produced by `src/verifier/` only |
| AuditEvent | tenant hash chain: seq (monotonic per tenant), type, actor {kind, ref}, server_time, subject_digest, redacted metadata, prev_digest, event_digest = SHA-256(JCS(event without event_digest)); append-only through one restricted writer; never contains capability values, codes, keys or PDF content Digest preimage (T029): `{schema: "opensig.audit-event/1", tenant_id, seq, type, actor {kind, ref}, server_time, envelope_id, subject_digest, metadata, prev_digest}`, with null for an absent envelope_id, subject_digest or prev_digest. The audit receipt carries exactly this preimage plus event_digest for each event (contract `AuditEventRecord`), so any holder can recompute the digest. metadata is already redacted at write time, so exposing it reveals nothing unredacted. |
| AuditCheckpoint | tenant_id, seq, event_digest at seq, created_at. Written every 100 events and at every completion. The chain head at completion goes into the audit receipt and the acceptance receipt. Restore verifies chain continuity from the last checkpoint and that the head is not older than the last exported head. The chain is **tamper-evident, not tamper-proof** (no external immutable store in MVP) |
| OutboxEvent | unique event_id, tenant/envelope/version, type/schema, delivery attempts and acknowledgement; inserted in the same transaction as the state change it announces |
| WebhookRegistration / WebhookKey | registration: tenant, HTTPS url, event types, status. Key: key_id, secret protected at rest (encrypted under a server key from a 0600 file), created_at, expires_at (overlap), revoked_at; at most 2 active keys |
| ConsumerAttachment | consumer/source_id/source_version/artifact_id unique; a duplicate event is harmless; a stale source version is rejected; deduplication retained ≥ 72 hours (the maximum retry horizon) |
| AnchorReceipt | `disabled` or `fake`, artifact_digest, adapter/schema; fake=true and never a real transaction ID or network proof |
| FinalizationAttemptBlob | tenant_id, envelope_id, fencing_token (the attempt), blob_ref (tenant-namespaced), role `composed`/`sealed`/`evidence`, recorded_at. The record is written in its own transaction **before** the blob write, so no attempt blob is ever untracked. It is added by an appended migration in T038. A blob is collectable only when no final_artifacts row references it and its attempt is not the published one, and only after the attempt's lease has expired plus a 24-hour grace. Deduplicated content-addressed refs shared with the published artifact are never collected |
| DisposalRecord | tenant, envelope, artifact digests retained, disposed blob refs, policy reference, server_time; written with an audit event when retention disposes blobs. Completed evidence is never mutated in place |

## Envelope states and the "fully approved" mapping

| State | Entered when | Notes |
|---|---|---|
| `draft` | createEnvelope or reissueEnvelope | Upload and recipients are editable. Prepare freezes |
| `prepared` | prepareEnvelope: the isolated parse service accepts the PDF, the fields validate, and the document plus field manifest freeze | Edits after this need void or reissue |
| `sent` | sendEnvelope: recipient 1 invited by mail | Service-created envelopes need a valid SendConfirmation |
| `in-progress` | Recipient 1 approves **and** a second recipient exists. Recipient 2 is then invited | Used only between the first and the last approval of a two-recipient envelope |
| `finalizing` | The **last** required approval is recorded. Within the same transaction: approvals_complete=true, SigningJob reserved (CAS on version) | **"Fully approved" = approvals_complete=true** (state `finalizing`, or `completed` afterwards). For a one-recipient envelope the single approval moves `sent → finalizing` directly and never passes through `in-progress` |
| `completed` | The publish transaction commits only after independent verification and durable storage of the PDF and evidence: CAS on state, version **and** the job's current fencing_token, plus artifact insert and outbox event | Visibly distinct from "fully approved" |
| `declined` | A recipient declines (before reservation) | Terminal; reissue allowed |
| `expired` | The expiry sweeper or a request finds expires_at passed (before reservation), with the same CAS | Terminal; reissue allowed |
| `voided` | voidEnvelope before reservation | Terminal; reissue allowed. After reservation, void returns 409 |

**Recipient status.**
- `pending → invited` happens at send for ordinal 1, or after the prior approval for ordinal 2.
- From `invited`, a recipient moves to `approved` or `declined`.
- `invited` or `pending` becomes `expired` when the envelope expires.
- Any non-final status becomes `revoked` on resend (the old invitation only; a fresh invitation returns the recipient to `invited`), on void, or on reissue (the old envelope).

## Transitions and races

1. **Single CAS path.** Approval, decline, expiry, void and the finalization reservation all compare and set (state, version) inside one `BEGIN IMMEDIATE` transaction. Exactly one wins; losers get 409 and leave no partial record.
2. **Finalization runner** (`src/jobs/`). The runner acquires a lease (`lease_owner`, `lease_expires_at`) and increments `attempt_count`, which becomes the `fencing_token`. It then runs compose, seal, verify, and storage of the PDF and evidence under content-addressed keys; these steps are idempotent. Finally it publishes. A publish whose fencing token is stale (the lease expired and another attempt took over) is rejected and leaves no trace. Every blob an attempt writes is first recorded as a FinalizationAttemptBlob. Blobs of abandoned attempts are collected by the orphan collector (T038, `src/storage/retention/orphans.ts`) under the rule in that entity, which writes a DisposalRecord and an audit event. They are never mixed into a published artifact, because the artifact row references one attempt's digests.
3. **Failure.** A failure moves the job to `retryable`, with bounded exponential `retry_at`, until `max_attempts` is reached; it then becomes `failed`. The envelope stays `finalizing` and the failure is visible. `retryFinalization` is allowed **only** from `retryable` or `failed`; otherwise it returns 409 `finalization_not_retryable`. Retry never creates approvals.
4. **Outbox.** Completion publishes one outbox event ID, and delivery retries keep that ID.
5. **Reissue.** Reissue always creates a new envelope linked to the prior one (`reissue_of`/`reissued_as`). It copies the source, recipients and document, never the approvals, and revokes every outstanding capability of the prior envelope.
6. **Retention.** Retention and disposal never mutate completed evidence in place. Expiry, archive and disposal follow the tenant's configured retention and write a DisposalRecord plus an audit event. When retention is unset, nothing is disposed.

## Authentication and secrets

- **Operators.**
  - Operators present OIDC bearer tokens. Validation pins the algorithms (RS256, ES256), takes JWKS from configuration only, requires an exact issuer and audience, allows at most 60 s of clock skew, and maps tenant roles and principal kind from configured claims.
  - **There is no operator browser session in the MVP**, so the operator side has no cookie and no CSRF surface.
  - Service principals (Hermes, healthcare adapters) can never approve, decline, confirm sends, register callbacks, void or retry.
- **Recipients.**
  - The invitation (URL fragment), the mailed challenge code and the HttpOnly/Secure/SameSite=Strict session follow the parameters in the entities above.
  - GET, HEAD and prefetch never approve or consume.
  - Approval requires a CSRF token, an allowlisted Origin, consent text version and digest, the expected document, manifest, prior approval and version, and the session's bound recipient.
  - Resend, reissue, void, decline and expiry revoke outstanding invitations, challenges and sessions.
  - Mailbox challenge alone never creates representative authority.
- **Keys and secrets.**
  - The development signing key, the challenge-HMAC key and the webhook-key encryption key are generated at test runtime in a `mkdtemp` directory with mode 0600, outside the tree. They are passed by file path or inherited file descriptor, never through environment variables or argv.
  - Child-process environments are cleared of secrets.
  - Production managed key custody is a release prerequisite.
