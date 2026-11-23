# Corporate Records Intake Contract

## Roles and surfaces

| Actor | Access |
| --- | --- |
| Intake submitter | Select an authorized entity, upload supported files, browse allowed source metadata, select files to import, and view allowed submission status. |
| Evidence reviewer | Read private submission contents within their assigned entity scope, record corroboration, request more evidence, verify or reject, and supersede a record with the correct review revision. |
| Tenant administrator | Configure or disable the test library binding and operator mappings. Provision SharePoint grants outside the intake app. |
| Signing recipient, Hermes, and service credentials | No governance library browsing, private record retrieval or evidence review through a signing session or service authorization. |

The Teams personal tab and standalone browser present the same intake form and
review states. Validate the user and role before listing entity options or
source items. The tab manifest enables hosted UI and sign-in. It creates no
OpenSig role, no library grant and no recipient signing proof.

## Operations

| Operation | Input | Observable result |
| --- | --- | --- |
| List intake and review status | Authorized entity, cursor if needed | Tenant- and entity-scoped submission references and allowed review status. |
| Browse SharePoint | Entity and configured binding, parent selection within permitted root, cursor | One bounded page containing only allowed display fields. No provider URLs, tokens or other tenants' items. |
| Submit direct upload | Entity, evidence type, bounded PDF or DOCX bytes, operation key | Immutable submission reference and receipt with upload origin, byte count, digest and awaiting-review status. |
| Import selected version | Entity, evidence type, binding, selected item and expected provider version or tag, operation key | Equivalent private submission receipt with a SharePoint origin and the accepted provider version. No receipt if exact-version retrieval cannot be established. |
| Review submission | Submission reference, expected review revision, disposition and corroboration | Audited review revision and authorized status. Changing source bytes requires a separate submission. |
| Read private evidence | Submission and version reference | Immutable original evidence only when the caller's private evidence permission covers that entity and version. |

Use the signed-in user's delegated context for SharePoint browse and import.
Resolve source identifiers against the configured root on the server. Verify
file eligibility and compare selection freshness before reading. A download
redirect is evaluated server-side and remains transient. Do not forward
authorization to the download host. A retry after a transient Graph fault must
retain the original source version or require a new selection. Scope cursors
and operation keys to the actor, entity and source.

## Error and evidence behavior

- A denied, unknown or out-of-scope source item returns a generic denial with
  no names, metadata, provider URL, or existence confirmation. Grant
  revocation is rechecked on new reads.
- A changed selection or stale review reports a conflict with a fresh selection
  or review path. A failed or incomplete import produces no reviewable
  submission.
- Record successful origins and provenance decisions as distinct events.
  Log allowed outcome codes and opaque references only. Do not expose Graph
  responses, file bytes, token claims, or presigned download URLs in logs or
  outward error details.
- Keep the original source file, permissions and retention settings intact.
  Evidence receipt, review history and backup restore resolve from OpenSig's
  retained copy.
