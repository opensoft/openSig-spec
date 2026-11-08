# Sessions, Events and Consumer Contracts

Draft contract 0.2 (2026-11-08); all original materials Apache-2.0. The operation names refer to [http-api.yaml](http-api.yaml) (OpenAPI 3.1 written as JSON). That file is normative for request and response shapes; this document is normative for behavior.

## Capability values

Invitation tokens, challenge codes, session values, CSRF tokens and webhook secrets are capability values. They follow these rules:

- **Where they never appear:** a URL path or query, a redirect, a Referer, an analytics beacon, a log line, an error body, an audit event, a notification sent to an operator or service, or any response returned to an operator or service principal.
- **Logging:** one central log redactor (T031) removes them, along with cookies, `Authorization` headers and PEM blocks. A test greps the captured logs for them.
- **Invitation link:** the mailed link is `https://<origin>/sign#i=<invitation>`. The fragment never reaches the server.

## Recipient ceremony

1. **`GET /sign` (`openReviewShell`)** serves a static shell.
   - It carries a strict CSP (no inline script, no `eval`), `frame-ancestors 'none'`, `Referrer-Policy: no-referrer` and `X-Content-Type-Options: nosniff`.
   - GET, HEAD, reload, back navigation and scanner prefetch never consume, approve or change any state.
   - The shell reads the fragment, then removes it from history with `history.replaceState`.
2. **`requestChallenge`** (`POST /v1/recipient-session/challenges`, body `{invitation}`) mails a challenge code to the recipient's address.
   - It always returns a generic 202, so it never reveals whether an invitation exists.
   - It is rate-limited per invitation and per client address.
   - The code is 10 characters of Crockford base32 (50 bits). It is stored only as an HMAC under a server key, compared in constant time, and expires after 10 minutes. A new code supersedes the previous one.
3. **`createRecipientSession`** (`POST /v1/recipient-session`, body `{invitation, code}`) sets the cookie `__Host-opensig_rs` (HttpOnly, Secure, SameSite=Strict, Path=/).
   - The session is bound to the tenant, envelope, recipient, revision and manifest.
   - The response returns the full review state, including `csrf_token`.
   - Five wrong codes lock the current challenge. Twenty wrong codes over an invitation's lifetime revoke the invitation, so a resend is required.
   - Failures are a generic 401, or 429 when the request is locked or rate-limited.
4. **`readRecipientSession`** (`GET /v1/recipient-session`) returns every approval input: `document.digest`, `manifest_digest`, this recipient's fields, `prior_approval_digest`, the exact consent text with its version and digest, the assurance statement, the organization-seal notice, `csrf_token`, `turn` and `session_expires_at`.
   - **Document bytes.** `readRecipientDocument` serves the exact frozen PDF bytes. That response alone allows `frame-ancestors 'self'`, so the browser's built-in viewer can display it.
   - **Manifest bytes.** `readRecipientManifest` serves the canonical manifest bytes, so the client can recompute both digests.
5. **`approveAsHuman`** (`POST /v1/recipient-session/approval`) requires the cookie, `X-CSRF-Token`, an allowlisted `Origin`, `consent: true`, the consent version and text digest, every digest, `expected_version` and exactly one typed value per field.
   - **Drawn signature:** vector strokes, with point caps.
   - **Typed signature:** text in standard-14 Helvetica. It must be WinAnsi-representable, and it is always encoded on output.
   - **Date:** no client value; the server fills it.
   - **Order:** only the recipient whose turn it is can approve.
   - **Retries:** an exact retry returns the original receipt; a changed retry returns 409.
   - **Who can call it:** no operator or service credential, which gets 401.
6. **`declineAsRecipient`** requires CSRF and `expected_version`, and returns a generic outcome.
7. **`endRecipientSession`** clears the cookie.

**Lifetimes.** Invitations live for 7 days from sending or until envelope expiry, whichever comes first. Sessions expire after 30 idle minutes. Resend, reissue, void, decline and expiry revoke every outstanding invitation, challenge and session of the affected recipients. The deployment origin allowlist is explicit; reflected origins are never trusted.

## Operator mutation behavior

- **Idempotency.** Operator POST/PUT requests require `Idempotency-Key`, scoped by tenant, route and principal. The same payload returns the original result; a changed payload returns 409 `idempotency_conflict`.
- **Expected version.** Every mutation after creation carries an expected version: `expected_version` (minimum 1) in the JSON body, or `If-Match: "<version>"` on `uploadDocument`.
- **Cross-tenant access.** Absent and inaccessible resources across tenants are both 404.
- **Races with finalization.** Decline, expiry or void racing finalization follows the reservation rule in data-model.md.
- **What reads never return.** Read and download operations never return private keys, capability values or another recipient's capability.

## Sending and human confirmation

Invitations are delivered **by mail only** (T032 notifications adapter). `sendEnvelope` and `resendInvitation` return status, never a link, token, code or mail content. That holds for every caller, and in particular for Hermes and other service principals (constitution P2 and P4).

**Envelopes created by a service principal** (`created_by.kind = service`, for example Hermes) cannot be sent without human confirmation:

1. A human operator of the same tenant (role `sender` or `tenant_admin`, principal kind `human-operator`) reads `readRecipientPreview`. They then call `confirmSend` with the current `document_digest`, `manifest_digest` and `recipients_digest`.
2. This returns a `SendConfirmation` whose `authorization_reference` is single-use. It is bound to that exact envelope version and digest set, and expires after 24 hours or at envelope expiry, whichever comes first.
3. The service then calls `sendEnvelope` with that `authorization_reference`.

Sending without a valid reference returns 409 `confirmation_required` or `confirmation_invalid` (negative test SC002-24). A service principal calling `confirmSend` gets 403. A human-created envelope sent by a human sender needs no separate confirmation.

**Outbound mail guard.**
- The SMTP adapter refuses a non-loopback relay host, and any recipient domain outside the reserved test domains (`.test`, `example.com`, `example.org`), unless the owner supplies explicit pilot configuration.
- Sends are rate-limited per tenant.
- The development spool is a local directory that neither Hermes nor any service principal can read.

## Webhook

**Registration.** Operations: `createWebhookRegistration`, `readWebhookRegistration`, `disableWebhookRegistration`, `rotateWebhookKey` and `revokeWebhookKey`.
- Only human tenant administrators can call them. Service principals, including Hermes, cannot register callbacks.
- The destination must be HTTPS and must match the tenant allowlist.
- Private, loopback, link-local, metadata, IPv4-mapped and alternate-form addresses are rejected. The check runs at registration and again at every connection: resolve once, validate every A/AAAA record, connect to that address with SNI, follow no redirects, ignore proxy environment variables, and bound the response size and time.
- Tests use an injected resolver and connector only.

**Keys.** Keys are per registration. A secret is shown once and is protected at rest. Rotation keeps the previous key valid for `overlap_seconds` (at most 24 hours), and revocation is immediate.

**Delivery** (`webhooks.envelopeEvent`). The body is a `WebhookEvent`: `{event_id, schema_version, type, envelope_id, envelope_version, artifact_id, artifact_digest, occurred_at}`.
- **Content.** No PDF, names, contact details or tokens.
- **Headers.** `X-OpenSig-Signature: v1=<hex HMAC-SHA256 over "<timestamp>.<raw body>">`, `X-OpenSig-Timestamp` and `X-OpenSig-Key-Id`.
- **Consumer obligations.** Reject timestamps older than 5 minutes. Deduplicate `event_id` for at least the 72-hour maximum retry horizon. Fetch canonical status with their own credential, and check the tenant and source version before attaching.
- **Tenant identity.** Bound through the registration, never trusted from event fields.

## Hermes tools

Hermes reaches openSig only through a stdio MCP server, `src/integrations/hermes/mcp-server.mjs`. Its config uses `mcp_servers.opensig` with `tools.include` set to exactly the four tools below. Each tool maps to operations as follows (also listed in `x-opensig-hermes-tools`):

| Tool | Operations | Returns |
|---|---|---|
| `prepare_envelope(source, pdf, recipients, fields)` | `createEnvelope`, `uploadDocument`, `prepareEnvelope`, `readRecipientPreview` | Envelope id, version, digests, `recipients_digest` and the preview, which a human must confirm |
| `request_signatures(envelope_id, expected_version, authorization_reference)` | `sendEnvelope` | Status only |
| `get_status(envelope_id)` | `readEnvelope` | Status, recipient progress, finalization and artifact summary |
| `retrieve_completed_package(envelope_id)` | `downloadPackage`, `readVerification` | Package and verification report for a completed envelope |

**Exclusions.** The MCP server advertises no approve, decline, session, challenge, confirmation, callback-registration, void, retry, key or ledger tool. `tools.include` is a second guard on top of that.

**What Hermes cannot reach.** Hermes uses a `service` principal. It cannot read invitation links, challenge codes, the mail spool or signing keys.

**Approvals.** Hermes's one-shot mode bypasses Hermes's own approvals (`HERMES_YOLO_MODE`), so human approval is enforced only by openSig: the recipient session plus the human send confirmation.

**Notifications.** Actual notification delivery is an acceptance gate. This document grants no permission to message anyone.

## openChart and openPractice

Both synthetic harnesses accept `{source_app, source_record, source_version, envelope_id, artifact_id, artifact_digest}`.
- They check the current source version and the authorized tenant mapping.
- A duplicate of the same attachment is a no-op; a stale or mismatched context is rejected.
- openChart keeps clinical attestation and amendment authority; openPractice keeps financial, claim and payment authority.
- The MVP uses no real patient records and no production consumers. Harness proof is explicitly not proof about a deployed app.

## Anchoring seam

Adapter methods: `submit(artifact_digest)`, `observe(receipt)` and `verify(artifact_digest, receipt)`.
- Disabled returns `disabled`.
- Fake returns `{fake: true, status: "simulated", artifact_digest}` and never a real network or transaction identifier.
- No wallet or external transaction call is allowed.
- A failure does not alter the completed PDF or the signing state.
