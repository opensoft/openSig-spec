# Data Model and State Contract

Revision 2026-11-08 (M4). The API shapes are in [http-api.yaml](contracts/http-api.yaml) and behavior is in [sessions-events-adapters.md](contracts/sessions-events-adapters.md).

## Global rules

- **Identifiers.** All identifiers are opaque (`^[A-Za-z0-9_-]{16,64}$`), tenant-scoped and server-generated. A caller-supplied ID never implies a tenant.
- **Time.** Server time is UTC. Client timestamps never establish approval time. Server times are labeled "unsigned server time" and are never presented as trusted timestamps.
- **Versions.** Schema versions and entity versions are persisted. Every mutation compares and sets an expected version.
- **Digests.** SHA-256 over exact bytes, lowercase hex.
- **Canonical JSON** follows RFC 8785 (JCS), implemented originally and checked against the RFC 8785 test vectors before use. It applies to the field manifest, recipients digest, field values, approvals, audit events and receipts.
- **Persistence.** `node:sqlite` in a single instance. Transactions use `BEGIN IMMEDIATE` with a bounded busy timeout. Every row carries `tenant_id`, and every query is tenant-filtered by the storage layer, never by the caller.
- **Blobs.** Immutable, content-addressed and **tenant-namespaced**, with key `tenants/<tenant_id>/sha256/<digest>`. Writes go to a temp file, then fsync the file, then rename. After the rename the destination directory is fsynced. Every directory on the path (`tenants`, the tenant directory and `sha256`) also has its parent fsynced before the write reports success, whether this write created it or found it existing. A store instance may skip that parent fsync only for a directory whose parent fsync already succeeded in the same instance; a failed fsync never marks a directory durable (A-010, Codex [19]). The deduplicated path (the entry already exists) also fsyncs the directory (A-008, S3; T028 BL-10, BL-11). Reads verify the digest. There is no cross-tenant deduplication.

## Entities

| Entity | Required fields and constraints |
|---|---|
| Tenant | id, identity mapping (issuer, audience, role claim to role, principal-kind claim), allowed roles, consent text in force, retention configuration (operational input; unset means retain) Retention configuration format, as implemented by T029 (c84988d; reconciliation note): `retention_json` = `{policy_ref, dispose_after_days, scope?: "documents" \| "all"}`. "archive" is not implemented, because it was never specified. |
| Principal (derived per request) | tenant_id, kind `human-operator` or `service`, roles ⊆ {sender, tenant_admin, service}, opaque ref. Classified from configured claims; a service principal can never hold `sender` or `tenant_admin` |
| Envelope | id, tenant_id, source app/record/version, state, monotonic version, created_by {kind, ref}, expires_at (default +7 days, maximum +30 days), recipients_count 1..2, base_revision_id, manifest_digest, approvals_complete, reissue_of / reissued_as; unique (tenant, idempotency key) with request digest |
| DocumentRevision | id, envelope_id, content_digest (the `document_digest` of the API), blob_ref, bytes ≤ 10 MiB, pages ≤ 50 with displayed geometry and rotate, parse report from the isolated parse service; immutable after prepare. A page whose effective (inherited) CropBox differs from its MediaBox is rejected at upload with 422 `unsupported_pdf`, detail `cropbox_differs` (A-008, S2) |
| FieldDefinition | key (unique per envelope), revision_id, recipient_id, recipient_ordinal, page, type `signature`/`date`, rect normalized to the displayed page (origin top-left, y downward, inside [0,1] and inside the page); each recipient has ≥ 1 signature field |
| FieldManifest | Canonical JSON `{schema, envelope_id, document_digest, fields}` frozen at prepare; `manifest_digest` = SHA-256 of its canonical bytes; immutable |
| Recipient | id, envelope_id, ordinal unique 1..2, email, operator-declared role, status (see below), invited_at, approved_at, approval_id |
| **ConsentText** | id, tenant_id, version (`^[a-z0-9.-]{1,40}$`), locale, exact text (UTF-8, ≤ 20000 chars), text_digest, effective_from. Immutable; a new wording is a new version. Each Approval stores consent_version **and** consent_text_digest, and the package carries the full text, so the exact wording a recipient saw can be reproduced |
| SendConfirmation | authorization_reference (id), tenant_id, envelope_id, envelope_version, document/manifest/recipients digests, confirmed_by (human-operator principal ref), confirmed_at, expires_at (min of +24 h and envelope expiry), consumed_at; single use; creation by a service principal is impossible |
| Invitation | id, recipient_id, revision and manifest binding, token hash (raw token ≥ 256 bits, mailed only), sent_at, expires_at (min of +7 days and envelope expiry), revoked_at, cumulative_wrong_codes (≥ 20 revokes the invitation) |
| Challenge | id, invitation_id, code_hmac (HMAC-SHA256 under a server key loaded from a 0600 file; code is 10 Crockford-base32 characters, 50 bits), expires_at (+10 min), attempts (≤ 5, then locked), consumed_at, superseded_at |
| RecipientSession | id, value hash (raw value only in the cookie), tenant/envelope/recipient/revision/manifest binding, csrf secret, created_at, last_seen_at (idle expiry 30 min), revoked_at |
| Approval | id, recipient_id unique, document_digest, manifest_digest, prior_approval_digest (null for ordinal 1), consent_version, consent_text_digest, typed field values (drawn strokes as integer fixed-point coordinates 0..10000, at most 15,000 points per approval so the compact body fits the 256 KiB JSON limit (contract 0.2.3); typed text; server-filled dates), field_values_digest, assurance {mailbox-challenge, mailbox-control}, server_time, approval_digest = SHA-256(JCS(record without approval_digest)); immutable, tenant-bound |
| IdempotencyRecord | tenant_id, route, principal ref, key, request digest, response status/body digest, response_body, created_at; unique (tenant, route, principal, key). **response_body (A-010, migration 0011):** the exact response bytes the client received, so an exact replay returns the original result after the envelope has moved on. It is at most 256 KiB; a larger body fails the transaction (500 `internal`) and is never truncated. The schema enforces that bound too, so no insert path can exceed it (migration 0012, A-010). It stores nothing the response did not carry, and operator responses never carry a capability value (send returns no link). A route whose response reveals a one-time secret (webhook key issuance, T019) stores no body, and its replay never re-reveals the secret; T019 rules the replay status. Records live 24 h. The idempotency repository purges expired records in bounded batches, T010's expiry sweeper runs that purge for every tenant, and a replay after that is a new request. A stored body may hold what its response held, such as a `confirmSend` `authorization_reference` or recipient email addresses. That is accepted: a replay returns it only to the same tenant, route, principal and key; the reference is single-use and digest-bound; and the copy lives at most 24 h. These records are operational data, not evidence; a purge writes no DisposalRecord. Backups carry them as part of the database |
| SigningJob | envelope_id unique, reserved_version (the envelope version written by the reservation transaction, that is, the transition into `finalizing`; A-010), status `pending`/`running`/`retryable`/`failed`/`succeeded`, attempt_count, max_attempts (default 5, `FINALIZATION_MAX_ATTEMPTS`, set by the reservation; migration 0004 allows 1–10; A-010, request #24), **lease_owner, lease_expires_at, fencing_token** (= attempt_count at lease grant), retry_at (bounded exponential), last_error_code; no secret traces |
| FinalArtifact | envelope_id unique, artifact_id, artifact_digest (SHA-256 of final.pdf), package_digest (SHA-256 of the ZIP), approved_composition_digest, PDF/evidence/package blob refs, signer certificate SHA-256, development_certificate flag, verifier name/version/results, fencing_token of the publishing attempt, approval_evidence_digest; immutable. **Evidence binding (A-008, S1).** approval_evidence_digest = SHA-256 of the RFC 8785 form of approval-manifest.json without `final_pdf_digest`. The manifest is built before sealing, after the `envelope.seal_started` audit anchor is written. The digest is committed inside the seal as the CMS signed attribute `opensig-approval-evidence-digest` (OID 2.25.254263291653419018820250361524609646248) |
| VerificationReport | artifact_digest, integrity, trust, trust_anchor_ref, revision_coverage, seal_type `organization`, timestamp_status (`absent` until implemented), revocation_status (`not-checked` until implemented), verifier, verified_at, checks; produced by `src/verifier/` only |
| AuditEvent | tenant hash chain: seq (monotonic per tenant), type, actor {kind, ref}, server_time, subject_digest, redacted metadata, prev_digest, event_digest = SHA-256(JCS(event without event_digest)); append-only through one restricted writer; never contains capability values, codes, keys or PDF content Digest preimage (T029): `{schema: "opensig.audit-event/1", tenant_id, seq, type, actor {kind, ref}, server_time, envelope_id, subject_digest, metadata, prev_digest}`, with null for an absent envelope_id, subject_digest or prev_digest. The audit receipt carries exactly this preimage plus event_digest for each event (contract `AuditEventRecord`), so any holder can recompute the digest. metadata is already redacted at write time, so exposing it reveals nothing unredacted. |
| AuditCheckpoint | tenant_id, seq, event_digest at seq, created_at. Written every 100 events and at every completion. The completion checkpoint is written at package assembly (A-010, S4), and the chain head at that point goes into the audit receipt and the acceptance receipt. Restore verifies chain continuity from the last checkpoint and that the head is not older than the last exported head (ledger rules in § Backups and the exported-head ledger). The chain is **tamper-evident, not tamper-proof** (no external immutable store in MVP) |
| OutboxEvent | unique event_id, tenant/envelope/version, type/schema, delivery attempts and acknowledgement; inserted in the same transaction as the state change it announces |
| WebhookRegistration / WebhookKey | registration: tenant, HTTPS url, event types, status. Key: key_id, secret protected at rest (encrypted under a server key from a 0600 file), created_at, expires_at (overlap), revoked_at; at most 2 active keys |
| ConsumerAttachment | consumer/source_id/source_version/artifact_id unique; a duplicate event is harmless; a stale source version is rejected; deduplication retained ≥ 72 hours (the maximum retry horizon) |
| AnchorReceipt | `disabled` or `fake`, artifact_digest, adapter/schema; fake=true and never a real transaction ID or network proof |
| FinalizationAttemptBlob | tenant_id, envelope_id, fencing_token (the attempt), blob_ref (tenant-namespaced), role `composed`/`sealed`/`evidence`, recorded_at. The record is written in its own transaction **before** the blob write, so no attempt blob is ever untracked. It is added by an appended migration in T038. A blob is collectable only when no final_artifacts row references it and its attempt is not the published one, and only after the attempt's lease has expired plus a 24-hour grace. Deduplicated content-addressed refs shared with the published artifact are never collected |
| DisposalRecord | tenant, envelope, artifact digests retained, disposed blob refs, policy reference, server_time; written with an audit event when retention disposes blobs. Completed evidence is never mutated in place |

## Backups and the exported-head ledger (A-009)

The exported-head ledger is one file beside the backups, on the local filesystem of the single service host. It records, per tenant, the audit-chain head (`seq`, `event_digest`) of the latest published backup, plus any retired tenants. The **last exported head** of a tenant is the value written by the most recent committed ledger replace.

1. **Single locked writer path.** Every ledger write, by backup, by first restore, or by any future receipt writer, goes through `src/storage/backup/ledger.ts`:
   - Take an exclusive lock: `${ledger}.lock`, created with O_CREAT|O_EXCL, recording pid, hostname, start time and a random nonce.
   - **Lock session (A-010).**
     - The owner keeps the lock file descriptor open for the whole session, from the O_EXCL create until release.
     - Release first checks that the path's device and inode still equal those of the held descriptor, then unlinks the path and closes the descriptor. On a mismatch it never unlinks the path and reports `lock-lost` (exit 70). That exit code takes precedence over the outcome's own, and the status line still names the underlying outcome and its recovery command. The operator runs verify before trusting that outcome (A-010, T30R3-3).
     - Every session path releases, error paths included. Only a crash leaves a lock behind.
   - Re-read the ledger and its digest.
   - Check ancestry for every tenant.
   - Do **one** atomic replace: temp file, fsync, rename, directory fsync. Immediately before the replace, compare the digest with the one read under the lock (CAS); a mismatch refuses.

   Lock contention refuses at once and never waits silently. A stale lock is never broken automatically. Only an explicit operator `--break-lock <nonce>` can break it, and only when the nonce matches, the recorded host is this host, and the recorded pid is not alive. A **damaged lock** (empty, truncated or unparseable, as a crash between the O_EXCL create and its fsync leaves it) has no nonce. Only `--break-lock damaged` can break it, and only when all of these hold:
   - the content still does not parse as a lock record;
   - the file is older than 60 seconds;
   - no process of the invoking user on this host holds a descriptor whose `/proc/<pid>/fd` entry resolves to the lock's device and inode. Because the owner holds the descriptor for the whole session, this proves that no live writer owns the lock.

   If `/proc` cannot be read, or any process of the user cannot be inspected, the break refuses and names those pids. An uninspectable process is always a potential holder and is never skipped. A non-dumpable writer can read its own `/proc/self/fd` while its peers cannot (OR-40 premise proof), so no self-check can show that a writer is inspectable. **Known limitation:** on a host with a same-user non-dumpable process (such as ssh-agent), a zombie, or a session under another primary gid, the break always refuses, and recovery is manual (T024, OP-04). Writers and the break run as the same user, in the same PID namespace, on the single host (A-010, T30R3-4 re-ruled). Immediately before acting, it re-checks that the file is the same (device, inode and digest). It moves the file aside to `${ledger}.lock.damaged-<time>` instead of deleting it, and reports the path. It never breaks a lock that parses; such a lock goes only through `--break-lock <nonce>` (T30R2-1, A-010). No other module writes the ledger file. Multi-host and shared-storage (for example NFS) backup is out of MVP scope.
2. **All or nothing.** A multi-tenant backup records every tenant's new head in that one replace, after every tenant has verified. Any refusal or failure before the commit point (rule 3) leaves the ledger byte-identical and the previous published backup restorable.
3. **Commit point and publish ordering.** The **commit point** is the ledger's atomic replace: the fsynced temp file is renamed over the ledger, and the ledger directory is fsynced. Everything before that rename is pre-commit. From the moment the rename returns, the run is post-commit, even if the directory fsync then fails, because the ledger may already show the new heads.
   1. Write the backup into a staging directory (`.staging-*`).
   2. Verify it.
   3. Under the lock, update the ledger. This is the commit point.
   4. Still under the lock, publish: rename staging to its final name, then fsync the parent directory. Release the lock, on success or failure.

   Verify and restore refuse any staging directory or any backup without its final manifest. Staging is recognised by its staged manifest, never by its directory name alone, so a copy or symlink of a staging directory under any other name is refused too (T30R2-3). A staging directory is the **pending committed staging** when its heads equal the committed ledger heads for every tenant and no published backup carries those heads. A backup may record the staging it is about to commit in an intent record (`<ledger>.publishing`) before the ledger rename. A staging named by a surviving intent record is also pending, even when a published backup already carries its heads (T30D-1, ratified in A-010): when the heads are unchanged, the ledger bytes cannot show whether the rename happened. Completing it publishes a second backup with the same heads. `--complete-staged` and abandoning clear the intent record.

   **Pre-commit refusal or failure.** This covers verification, lock contention, CAS mismatch, ancestry, the staging write, the ledger temp write or temp fsync, a failed ledger rename, and a crash at any of these points. The ledger is byte-identical, the previous published backup stays restorable, and the uncommitted staging may be discarded. If a crash loses a ledger rename whose directory fsync had not completed, the ledger shows the old heads again, and the same holds.

   **Post-commit publication failure.** This covers a failed ledger-directory fsync, a failed staging rename, a failed parent-directory fsync, and a crash after the ledger rename. The ledger is committed and can be ahead of every published backup. It is **not** byte-identical, and no message may say it is.
   - The staged backup is preserved under exactly one of its two names, staging or final. The failing run never deletes it.
   - After a failed ledger-directory fsync, the run does not publish.
   - The run reports `committed-pending-publication` (exit 75), naming the directory, the committed ledger digest and the recovery command.
   - After a crash, the lock stays held until `--break-lock` (rule 1). The first later run that takes the lock reports the same status.
   - While a pending committed staging exists, backup, verify and restore report `committed-pending-publication` (exit 75) and change nothing.
   - The window affects restore availability only, never integrity. The ledger is never rolled back.

   **Recovery** (each under the lock):
   - `--complete-staged <dir>`, where `dir` is the staging or the final name. It re-verifies the staged backup in full and requires its heads to equal the committed ledger heads for every tenant. It then re-commits the ledger's current bytes by a fresh atomic replace, renames the directory if it is still staged, and fsyncs the parent. A bare re-fsync is never enough, because after an earlier fsync error a retry can return success without the entry being durable. It reports `published` only after the parent fsync succeeds. A head mismatch or a failed re-verification reports `refused` and leaves the ledger and the directory untouched. A repeated I/O failure reports `committed-pending-publication` again.
   - `--discard-staged <dir>` deletes only uncommitted staging: its heads differ from the committed ledger heads, or a published backup already carries those heads. It reports `discarded`.
   - `--discard-staged`, with or without `--abandon-committed`, acts only on a directory under its staging name, addressed by its exact current name. If that name does not exist, including when the backup already sits under its final name, it reports `refused`, says where the backup is, and deletes nothing. A backup under its final name is a published backup, and `--discard-staged` never deletes one (T30D-4, rejected in A-010).
   - On the pending committed staging, `--discard-staged` refuses (`refused`) unless `--abandon-committed <ledger-digest>` is also given and matches the current ledger digest. Abandoning deletes the staging, leaves the ledger as committed, and reports `abandoned-committed`. Unless a published backup already carries the committed heads (the T30D-1 case), every restore then refuses until a backup carrying them is published. The next backup's heads must descend from the committed heads.

   **Post-commit failure in other ledger writers (A-010).** This covers `--first-restore`, `--retire-tenant`, `recordExportedHead` called outside a backup, and any future receipt writer, when the ledger rename has returned and the ledger-directory fsync then fails. A backup run in the same situation reports `committed-pending-publication`, because its staging still has to be published.
   - The writer reports `committed-unsynced` (exit 74). The new ledger is visible, but it is not known to be durable. The message names the ledger, the previous and new digests and the recovery command. It never says the ledger is unchanged, byte-identical or durable.
   - Nothing is rolled back. The restored database of a first restore stays in place, and the ledger is never rewritten to its previous bytes.
   - The writer performs no effect that depends on the new head, such as releasing a receipt, until the ledger is durable.
   - **Recovery:** `--resync-ledger <ledger-digest>`, under the lock. If the current ledger digest equals the given digest, it re-commits the same bytes by a fresh atomic replace (never a bare re-fsync) and reports `ledger-synced`. Otherwise the ledger has changed since: a later replace made its own bytes durable, or a crash lost the write. It reports `refused`, changes nothing and names the digest it found. The operator re-runs the original command if its effect is missing. Any later successful ledger replace also makes its own bytes durable.
   - A crash that loses the rename leaves the previous ledger, which is the pre-commit outcome.

   **Ledger statuses.** `scripts/backup.mjs` exits with these codes. Library callers such as `recordExportedHead` get the same status names as typed results.

   | Status | Exit | Meaning |
   |---|---|---|
   | `published` | 0 | A backup or `--complete-staged` is published and its parent directory fsynced |
   | `discarded` | 0 | Uncommitted staging was deleted |
   | `abandoned-committed` | 0 | The pending committed staging was deleted on `--abandon-committed`; the ledger stays ahead |
   | `refused` | 1 | Any pre-commit refusal or failure, or a refused recovery; this run left the ledger byte-identical |
   | `committed-pending-publication` | 75 | A post-commit publication failure, or a pending committed staging; staging is preserved |
   | `committed-unsynced` | 74 | A non-backup ledger write passed the rename, but its ledger-directory fsync failed; the new ledger is visible and not known to be durable |
   | `lock-lost` | 70 | At release, the lock path no longer matched the held descriptor; the underlying outcome is still reported |
   | usage error | 2 | Bad or conflicting arguments, detected before any lock is taken; nothing is written |
   | `ledger-synced` | 0 | `--resync-ledger` re-committed the current ledger bytes |

   Exit 75 means only `committed-pending-publication`. Exit 74 means only `committed-unsynced`. Exit 70 means only `lock-lost`. Exit 2 means only a usage error. A refusal found during a run, such as a database appearing at the restore target (never overwritten), is `refused` (exit 1) (A-010, T30R3-5).
4. **First runs.** A missing ledger refuses backup, verify and restore unless `--first-backup` or `--first-restore` is given. Damaged ledgers are refused even with those flags.
   - `--first-backup` creates the ledger, empty when no tenant has audit events yet.
   - `--first-restore` writes the restored heads under the lock, all or nothing, so the next backup needs no flag. It restores the database first and writes the ledger as its last step (T30D-3, ratified in A-010). A failure before the commit point removes only the database files this run created, never touches a database that existed before the run, and leaves the ledger byte-identical (`refused`). After the commit point, the restored database is never removed.
   - Other restores never write the ledger.
5. **Retired tenants.** A ledger tenant absent from the database refuses backup, because the tenant was deleted or the database replaced. `--retire-tenant <tenant_id>` (under the lock; the tenant must be absent) records a retirement entry with its last head. A retired tenant that reappears refuses backup. Verify and restore read the full ledger, retired entries included. A backup that contains a retired tenant is refused (`refused`, exit 1) and is never restored or reported verified. Its head for that tenant is first checked against the retained head, so the message says whether the backup is older than that head, diverges from it, or would reintroduce the tenant at it. There is no un-retire in the MVP (A-010, Codex [18]).
6. **Required blobs.** The blobs a backup requires are computed with the same reference-protection predicate that T029's disposer uses. That predicate is exported from `src/storage/retention/`; backup never keeps its own copy of the rule.

## Envelope states and the "fully approved" mapping

| State | Entered when | Notes |
|---|---|---|
| `draft` | createEnvelope or reissueEnvelope | Upload and recipients are editable. Prepare freezes |
| `prepared` | prepareEnvelope: the isolated parse service accepts the PDF, the fields validate, and the document plus field manifest freeze | Edits after this need void or reissue |
| `sent` | sendEnvelope: recipient 1 invited by mail | A send needs a valid SendConfirmation when the envelope is service-created **or** the sending principal is a service (A-010). A service send of a human-created envelope without one is 409 `confirmation_required`. The recipient preview's `requires_human_confirmation` applies the same rule to the reading principal |
| `in-progress` | Recipient 1 approves **and** a second recipient exists. Recipient 2 is then invited | Used only between the first and the last approval of a two-recipient envelope |
| `finalizing` | The **last** required approval is recorded. Within the same transaction: approvals_complete=true, SigningJob reserved (CAS on version) | **"Fully approved" = approvals_complete=true** (state `finalizing`, or `completed` afterwards). For a one-recipient envelope the single approval moves `sent → finalizing` directly and never passes through `in-progress` |
| `completed` | The publish transaction commits only after independent verification and durable storage of the PDF and evidence: CAS on state, version **and** the job's current fencing_token, plus artifact insert and outbox event | Visibly distinct from "fully approved" |
| `declined` | A recipient declines (before reservation) | Terminal; reissue allowed |
| `expired` | The expiry sweeper or a request finds expires_at passed (before reservation), with the same CAS | Terminal; reissue allowed |
| `voided` | voidEnvelope before reservation | Terminal; reissue allowed. After reservation, void returns 409 |

**Transition graph (A-010).** The graph governs every UPDATE that changes `state`. These are the only legal transitions:
- `draft` → `prepared`, `voided` or `expired`;
- `prepared` → `sent`, `voided` or `expired`;
- `sent` → `in-progress`, `finalizing`, `declined`, `voided` or `expired`;
- `in-progress` → `finalizing`, `declined`, `voided` or `expired`;
- `finalizing` → `completed`.

`completed`, `declined`, `expired` and `voided` are terminal. A finalization failure leaves the envelope in `finalizing`. An update that keeps the state is not a transition; the freeze and version rules govern it.

**Inserts.** The graph does not govern inserts. Storage-level inserts in any state the existing CHECK and FK constraints admit stay allowed, for fixtures and for restore. Domain code (create and reissue) inserts only `draft` at version 1.

**Recipient status.**
- `pending → invited` happens at send for ordinal 1, or after the prior approval for ordinal 2.
- From `invited`, a recipient moves to `approved` or `declined`.
- `invited` or `pending` becomes `expired` when the envelope expires.
- Any non-final status becomes `revoked` on resend (the old invitation only; a fresh invitation returns the recipient to `invited`), on void, on reissue (the old envelope), or on decline (every other recipient of the declined envelope; A-010, request #24).

## Transitions and races

1. **Single CAS path.** Approval, decline, expiry, void and the finalization reservation all compare and set (state, version) inside one `BEGIN IMMEDIATE` transaction. Exactly one wins; losers get 409 and leave no partial record. **Version (A-010).** Create sets `version` to 1. Each successful operation that changes the envelope's state or content, upload and prepare included, increments it by exactly 1 inside its transaction. A refused request, a read and an idempotent replay leave it unchanged. Every envelope column except `updated_at` counts as state or content. The version only ever moves by exactly +1, and any state or content change requires that +1. A storage update of `updated_at` alone may leave the version unchanged, as DB-28 shows (A-010). **Migration rule:** the version-step trigger names the envelope columns it covers. Any migration that adds an `envelopes` column must update that trigger in the same migration; T007's ST-15 fails if any column other than `updated_at` is left uncovered.
2. **Finalization runner** (`src/jobs/`). The runner acquires a lease (`lease_owner`, `lease_expires_at`) and increments `attempt_count`, which becomes the `fencing_token`. It then runs compose, seal, verify, storage of the PDF and evidence under content-addressed keys, and package assembly; these steps are idempotent. Finally it publishes. **Package assembly (A-010, S4).** After verification and evidence storage, and before the publish transaction, the attempt:
   1. writes the completion checkpoint;
   2. builds audit-receipt.json from every event of the envelope up to the tenant chain head at that moment;
   3. builds the ZIP with T016's deterministic package builder;
   4. records and stores the ZIP blob.

   The publish transaction then inserts the immutable FinalArtifact with `package_digest` and the package blob ref. The `envelope.completed` event, written in that transaction, comes after the receipt's chain head and is not in the receipt. Contract 0.2.3 states this in the AuditReceipt description. A publish whose fencing token is stale (the lease expired and another attempt took over) is rejected and leaves no trace. Every blob an attempt writes is first recorded as a FinalizationAttemptBlob. Blobs of abandoned attempts are collected by the orphan collector (T038, `src/storage/retention/orphans.ts`) under the rule in that entity, which writes a DisposalRecord and an audit event. They are never mixed into a published artifact, because the artifact row references one attempt's digests.
3. **Failure.** A failure moves the job to `retryable`, with bounded exponential `retry_at`, until `max_attempts` is reached; it then becomes `failed`. The envelope stays `finalizing` and the failure is visible. `retryFinalization` is allowed **only** from `retryable` or `failed`; otherwise it returns 409 `finalization_not_retryable`. Retry never creates approvals.
4. **Outbox.** Completion publishes one outbox event ID, and delivery retries keep that ID. A delivery success that arrives after the event was abandoned, whether by `attempts_exhausted` or by `retry_horizon_passed`, is still recorded as its acknowledgement, so the evidence of delivery is kept. The event keeps its abandon state, is never relabelled, and gets no further attempts (T30R2-6 and T30R3-2, A-010).
5. **Reissue.** Reissue always creates a new envelope linked to the prior one (`reissue_of`/`reissued_as`). It copies the source, recipients and document, never the approvals, and revokes every outstanding capability of the prior envelope.
6. **Retention.** Retention and disposal never mutate completed evidence in place. Reference checks inside a disposal transaction use indexed lookups, never a table scan. T003 indexes `document_revisions(tenant_id, blob_ref)` and `(tenant_id, <column>)` for every blob-ref column of `final_artifacts` (A-010, T30R3-1). Expiry, archive and disposal follow the tenant's configured retention and write a DisposalRecord plus an audit event. When retention is unset, nothing is disposed.

## Authentication and secrets

- **Operators.**
  - Operators present OIDC bearer tokens. Validation pins the algorithms (RS256, ES256), takes JWKS from configuration only, requires an exact issuer and audience, allows at most 60 s of clock skew, and maps tenant roles and principal kind from configured claims.
  - **There is no operator browser session in the MVP**, so the operator side has no cookie and no CSRF surface.
  - Service principals (Hermes, healthcare adapters) can never approve, decline, confirm sends, register callbacks, void or retry.
- **Recipients.**
  - **Generic 202 (A-010).** `requestChallenge`'s generic 202 must be indistinguishable in timing as well as in status, body and headers.
    - Before responding it does only work that is the same for every request: schema validation, and the rate-limit decision keyed by the presented token's hash.
    - The lookup, supersession, challenge write and mail run after the 202, serialized per invitation in their own transaction.
    - A failure after the response is logged with the correlation ID (never a token or code) and counted, never silently lost. The recipient can request again.
    - Durable counters and rate limits are unchanged.
  - The invitation (URL fragment), the mailed challenge code and the HttpOnly/Secure/SameSite=Strict session follow the parameters in the entities above.
  - GET, HEAD and prefetch never approve or consume.
  - Approval requires a CSRF token, an allowlisted Origin, consent text version and digest, the expected document, manifest, prior approval and version, and the session's bound recipient.
  - Resend, reissue, void, decline and expiry revoke outstanding invitations, challenges and sessions.
  - Mailbox challenge alone never creates representative authority.
- **Keys and secrets.**
  - The development signing key, the challenge-HMAC key and the webhook-key encryption key are generated at test runtime in a `mkdtemp` directory with mode 0600, outside the tree. They are passed by file path or inherited file descriptor, never through environment variables or argv.
  - Child-process environments are cleared of secrets.
  - Production managed key custody is a release prerequisite.
