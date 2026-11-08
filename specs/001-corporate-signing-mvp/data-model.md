# Data Model and State Contract

All identifiers are opaque, tenant-scoped and server generated. Server time is UTC; client timestamps never establish approval time. Persist schema versions and expected entity versions. Digests use SHA-256 over exact bytes; canonical manifest JSON uses deterministic UTF-8 key ordering/number/string rules with test vectors before use.

| Entity | Required fields and constraints |
|---|---|
| Tenant | id, configured identity mapping, allowed templates/roles; no implicit tenant from caller-supplied envelope ID |
| Envelope | id, tenant_id, source_app/record/version, state, monotonic version, expires_at, recipients_count 1..2, base_revision_id; unique tenant/idempotency key with request digest |
| DocumentRevision | id, envelope_id, content_digest, blob_ref, bytes <=10 MiB, pages <=50, media type PDF, schema; immutable after prepare |
| FieldDefinition | id, revision_id, recipient_id, page, normalized rectangle, type signature/date; finite coordinates contained in actual page |
| Recipient | id, envelope_id, ordinal unique 1..2, email, role declared by operator, challenge assurance; next ordinal only |
| SigningSession | hashed random invitation/challenge/session values, recipient/revision/manifest binding, expiry, attempts, consumed/revoked; never persist raw bearer secrets in audit |
| Approval | id, recipient_id unique, document/manifest/prior_approval digests, values, consent_version, assurance, server_time; immutable and tenant-bound |
| SigningJob | envelope_id unique, reserved_version, attempt_count, retry_at, status pending/running/retryable/failed/succeeded, error code; no secret traces |
| FinalArtifact | envelope_id unique, artifact_id, exact_pdf_digest, approved_composition_digest, PDF/evidence refs, certificate fingerprint, verifier/version/results; immutable |
| AuditEvent | append-only actor/type/subject/server_time/digests, redacted metadata, previous digest; restricted writer and immutable checkpoint storage |
| OutboxEvent | unique event_id, tenant/envelope/version, type/schema, delivery attempts/ack; transactionally linked completion |
| ConsumerAttachment | consumer/source_id/source_version/artifact_id unique; duplicate event harmless, stale source version rejected |
| AnchorReceipt | disabled or fake, artifact_digest, adapter/schema; fake=true and no real transaction ID/network proof |

## Transitions

Draft accepts upload/recipient/field changes. Prepare validates supported PDF and freezes inputs; send requires valid recipient/auth/delivery configuration and moves to sent. First approval moves to in-progress; last approval reserves finalization atomically and moves to finalizing. Completed requires verified durable output and evidence. Decline/expiry/void is allowed before reservation. Once finalizing has won the compare-and-set, void returns 409; the operator can observe recovery but cannot misrepresent completed bytes as never signed. Reissue always creates a new envelope linked to the prior one.

Retry after finalization failure preserves finalizing and a bounded job state; no new approvals or duplicate package. A terminal failed job is visible and may be explicitly retried under policy. Completion publishes one outbox event ID; delivery retries retain that ID. Deletion/retention cannot mutate completed evidence in place; define expiry/archive/disposal with audit and operational retention configuration.

## Authentication and secrets

OIDC issuer/audience and mapped tenant roles validate operator/service claims. Recipient invite is exchanged with a separately delivered challenge into an HttpOnly/Secure session; fail after five incorrect attempts, challenge expires after ten minutes, invitation after seven days or earlier envelope expiry, and session after thirty idle minutes. GET/review never approves or consumes a signature action. Approval requires CSRF proof, consent version, expected document/manifest/version and intended recipient. Reset/reissue revokes outstanding sessions. No mailbox challenge alone creates representative authority.
