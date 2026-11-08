# Sessions, Events and Consumer Contracts

Draft contract 0.1; all original materials Apache-2.0.

## Recipient ceremony

GET /sign/{opaque-invitation} only opens the review shell; it never approves/consumes. Exchange invitation through POST /v1/recipient-sessions/challenge with invitation and no tenant override; return a generic delivery result to avoid enumeration. Challenge has 10-minute TTL, five-attempt maximum and a rate limit per invitation and origin. POST /v1/recipient-sessions/exchange accepts invitation plus challenge and sets HttpOnly/Secure/SameSite cookie bound to recipient/revision. No raw token appears in redirect URLs, referrers or logs. GET /v1/recipient-sessions/current returns only the bound revision/fields/current digest/consent version after authentication. POST /v1/recipient-sessions/decline requires CSRF and expected version; returns generic envelope outcome. Opening/viewing does not sign. Invitation lifetime is seven days or envelope expiry, whichever comes first; session idle TTL thirty minutes. Reset/reissue revokes all prior capability tokens. Use an explicit deployment-origin allowlist and CSRF validation, not reflected origins.

## Mutation behavior

Operator POST requires Idempotency-Key and operation-specific expected_version after creation. Scope key by tenant, route and caller; same payload returns original result, changed payload is 409. Approval uses recipient uniqueness plus expected_version and body digest to make exact retry harmless, changed retry 409. Cross-tenant absent/inaccessible is uniformly 404. Decline/expiry/void versus finalizing uses the reservation rule in data-model.md. Read/download never returns private keys, session/challenge values or another recipient's capability.

## Webhook

Event schema: {event_id, schema_version, type, envelope_id, envelope_version, artifact_id?, occurred_at}. Tenant identity is bound through registered callback configuration, not trusted from arbitrary event fields. Event carries no PDF, patient name or signing tokens. Sign timestamp plus raw body with a per-registration HMAC key; deliver signature/version/key-ID headers, reject timestamps older than five minutes and deduplicate event IDs. Key rotation supports overlap. Consumer fetches canonical status with its own credential and checks tenant/source version before attaching. Registration is operator controlled with HTTPS allowlist, no private/link-local/metadata destinations, DNS/IP validation at connection time, bounded response size/time and no redirects; prevent webhook SSRF.

## Hermes tools

prepare_envelope(source/version, PDF, recipients, fields); request_signatures(envelope, expected_version, authorization_reference); get_status(envelope); retrieve_completed_package(envelope). No approveAsHuman, arbitrary callback registration, private key access or automatic ledger submission. Validate document hash/recipient preview and applicable user authorization before notification. Actual notification delivery is an acceptance gate, not permission from this document to message anyone.

## openChart and openPractice

Both synthetic harnesses accept {source_app, source_record, source_version, envelope_id, artifact_id, artifact_digest}. Check current source version and authorized tenant mapping. Duplicate same attachment is a no-op; stale/mismatched context rejects. openChart keeps clinical attestation/amendment; openPractice keeps financial/claim/payment authority. No real patient records or production consumers in MVP. Harness proof is explicitly not deployed app proof.

## Anchoring seam

Adapter methods: submit(artifact_digest), observe(receipt), verify(artifact_digest,receipt). Disabled returns disabled. Fake returns {fake:true, status:simulated, artifact_digest} and never a real network/transaction identifier. No wallet or external transaction call is allowed. Failure does not alter completed PDF/signing state.
