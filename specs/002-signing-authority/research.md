# Feature 002 Research Decisions

## Teams host and session

**Decision** Host the same OpenSig intake page in a Teams personal static tab
and in a standalone browser. Use an HTTPS application origin, manifest
`staticTabs` with personal scope, `validDomains` and `webApplicationInfo`.
Qualify the Teams frame and content-security configuration. The server validates
the OpenSig-audience Teams token and maps its stable Entra tenant and user
identifiers to OpenSig's tenant and intake role. Require the configured tenant
issuer, audience and time checks. Qualify trusted signing-key rotation before
live exposure. Use on-behalf-of (OBO) delegated
Graph calls on the server. Handle consent and conditional access with the
Microsoft-supported interactive flow. Keep direct upload available to authorized
OpenSig users when the Teams integration is unavailable.

**Rationale** A shared UI limits duplicated authorization and status behavior.
Independent OpenSig roles are still required. The Graph token remains on the
server even when the UI runs in Teams.

**Alternatives considered** A second Teams-only frontend would duplicate the
intake UI. A bot or channel-wide tab would add distribution and access-review
scope before the personal flow is qualified.

[Teams tab SSO](https://learn.microsoft.com/en-us/microsoftteams/platform/tabs/how-to/authentication/tab-sso-overview) ·
[Tab manifest](https://learn.microsoft.com/en-us/microsoftteams/platform/tabs/how-to/authentication/tab-sso-manifest) ·
[Delegated Graph permissions for tabs](https://learn.microsoft.com/en-us/microsoftteams/platform/tabs/how-to/authentication/tab-sso-graph-api) ·
[Tab requirements](https://learn.microsoft.com/en-us/microsoftteams/platform/tabs/how-to/tab-requirements)

## Restricted SharePoint selector

**Decision** Configure a dedicated, versioned synthetic library by its tenant,
site, list, drive and permitted root identifiers. Start with delegated
`Lists.SelectedOperations.Selected` and an explicit library-level `read`
assignment. Require the intersection of that assignment, the signed-in user's
SharePoint access and OpenSig entity access. The custom selector lists only
permitted child metadata. It reads file bytes only after deliberate selection.
Tenant administrators provision the grant separately. OpenSig's runtime
credentials cannot grant permissions or write to the library.

**Rationale** Teams standard-channel files are in SharePoint. The same adapter
covers a Teams-backed location or a directly configured SharePoint library.
Microsoft documents resource-level delegated selected scopes, but the endpoint
references do not all enumerate those scopes. Prove the exact list, metadata,
version and byte-read operations in a controlled tenant. Activation requires
passed proofs with the proposed grant. Direct upload remains available if any
required operation cannot be qualified with that grant.

**Alternatives considered** The stock File Picker documents broader permissions
for general and Teams channel browsing. Tenant-wide discovery, broad Graph
read consent and app-only fallback are outside the pilot. A more restrictive
grant can be evaluated against the same acceptance suite if it supports every
required operation. Private and shared channels have separate sites and are
outside this first connector pilot.

[Teams and SharePoint storage](https://learn.microsoft.com/en-gb/microsoftteams/sharepoint-onedrive-interact) ·
[Selected permission grants and inheritance](https://learn.microsoft.com/en-us/graph/permissions-selected-overview) ·
[Folder children](https://learn.microsoft.com/en-us/graph/api/driveitem-list-children?view=graph-rest-1.0) ·
[Microsoft File Picker](https://learn.microsoft.com/en-us/onedrive/developer/controls/file-pickers/?view=odsp-graph-online)

## Exact imported version

**Decision** Prefer reading bytes for an explicit immutable version when the
provider supports it. Graph's previous-version content route excludes the
current version. For a current selection, a candidate implementation compares
provider version and item ETag before and after a bounded current-content read.
Any change, inability to bind the bytes to the selected version, or incomplete
read prevents promotion. Accept the current-version path only after the
controlled tenant check establishes exact-version behavior against known bytes
and concurrent edits. Otherwise require a supported explicit previous version
or direct upload. Persist the observed source identifiers, accepted bytes and
content digest. Host-approved HTTPS redirect downloads are transient. Do not
forward Graph authorization on the redirect or record the capability URL.

**Rationale** The reviewed submission must remain stable after SharePoint edits.
A matching ETag observation is a race detector. Graph documents
`If-None-Match` for current content and does not document an atomic version
precondition there. A digest protects the captured bytes after download.
Provider evidence and acceptance prove the claim about their source version.

**Alternatives considered** A live SharePoint link alone would change under
review. Automatically retrying against whatever version is current could
silently replace what the submitter chose.

[Current content and redirects](https://learn.microsoft.com/en-us/graph/api/driveitem-get-content?view=graph-rest-1.0) ·
[Version metadata](https://learn.microsoft.com/en-us/graph/api/driveitemversion-get?view=graph-rest-1.0) ·
[Download previous versions](https://learn.microsoft.com/en-us/graph/api/driveitemversion-get-contents?view=graph-rest-1.0)

## Evidence review and signing boundaries

**Decision** Start with PDF and DOCX uploads and imports as bounded private
evidence. Apply format and safety checks before exposing them for review.
Store immutable bytes without running scripts, macros or transformations on
the retained copy. The reviewer records separate conclusions about provenance,
current applicability and transaction scope. An exact revision and a verified
human action remain prerequisites for certified completion.

**Rationale** Tenant authorization governs access to the submission. Review
establishes the authority basis. Neither a storage location nor an organization
seal can by itself establish that basis.

**Alternatives considered** Reusing the signing PDF upload endpoint would couple
governance evidence to envelope handling and its distinct PDF parser contract.
Automated text extraction can be evaluated later as a review aid. It provides
no authority decision by itself.
