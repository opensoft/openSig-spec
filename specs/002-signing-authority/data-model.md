# Feature 002 Data Model

Tenant boundaries apply to all records and blob references. Company-level
authorization additionally scopes every intake and review operation.

| Record | Required fields and rules |
| --- | --- |
| Entity and registry observation | OpenSig tenant and entity IDs, jurisdiction and registration ID, legal name, source, standing result and observation time. Preserve observations used by signing independently of later lookups. |
| Library binding | OpenSig tenant and allowed entity mapping, Entra tenant, SharePoint site, list, drive and permitted root, approval and activation status. The configured root is server-owned. A disabled binding rejects new browse and import requests. |
| Evidence submission | Entity, type, submitter, submitted time, size, digest, private immutable blob reference, and origin. SharePoint origin adds the approved library binding, item ID, selected provider version and available tags. Upload origin records upload provenance. |
| Review decision | Submission and expected review revision, reviewer, timestamp, disposition, corroborating evidence and source, effective dates, and restricted review notes. Preserve changes as additional decisions. |
| Authority decision | Entity and act, envelope and exact revision, allowed people or roles, reviewed governing versions and provisions, appointments, limits, timing, required approvals and co-signers. Link a separately verified resolution when required. |
| Signing-time proof | Human recipient, capacity, exact request revision, identity verification reference, evaluated authority decision, time, outcome and signing event. Restrict identity material to authorized evidence users. |
| Certification summary | Exact completed artifact and revision, mode, label, verified capacities and timing, and stable references to private authority evidence. Include only the information needed for recipient verification. |

## Intake and review transitions

1. Stage the file, enforce format and size bounds, and compute its digest.
   An incomplete, denied or changed-source transfer creates no reviewable
   submission. Recover uncommitted blob references safely.
2. Commit one immutable submission in `awaiting_review` with an audit event.
   The same actor retrying the same scoped operation returns the original
   result. A reused operation key with different input conflicts.
3. An authorized decision records `needs_evidence`, `verified` or
   `rejected`. Review carries an expected revision so a stale decision
   cannot overwrite a newer one. Resubmission establishes a new review
   revision or a new immutable file record when the bytes change.
4. Mark an older evidence version `superseded` after the replacement review
   establishes that it no longer applies. Preserve past review decisions
   and evidence needed for historical signatures. A source URL or mutable
   item reference can never replace an accepted blob silently.

Durability must cover committed rows, blob references and audited state across
restart, backup and restore. Tenant-bounded blob reads, retention references
and orphan collection must include corporate records. A blob still needed as
signing evidence cannot be removed through the normal supersession path.

## Signing and completion

Bind the selected mode and authority decision to the same signing version
as the document and recipient evidence. Authorization must be effective at the
time of each counted signature. Leave certification pending if evidence is
disputed or a required check cannot be established. A new request after send
requires fresh actions and invalidates obsolete invitation access. Preserve
the history of previous requests and proofs.

Use a separately versioned certified package profile. Bind the recipient-safe
certification summary to authenticated signing evidence before sealing while
keeping governance files and detailed identity records private. Existing
autograph-only package consumers retain their current behavior.
