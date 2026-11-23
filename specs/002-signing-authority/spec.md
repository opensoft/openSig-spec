# Feature Specification for Signing Authority

**Feature branch** `002-signing-authority`

**Created** 2026-11-23

**Status** Draft

**Sources** [Signing authority roadmap](https://github.com/opensoft/openSig-spec/blob/a759b0af7f44c44b2a34c7fdd7823b9f59f28d9e/ROADMAP.md#optional-signing-authority-verification) · [Corporate Signing MVP](https://github.com/opensoft/openSig-spec/blob/283d00a74fbd3ee001c6eaeea40e7bd6dfa51a4c/specs/001-corporate-signing-mvp/spec.md)

## User Scenarios & Testing

### User Story 1 - Complete autograph-only signing (Priority P1)

A sender uses ordinary signing without submitting governing records or
requesting authority certification. The recipient reviews and signs the
document through the existing human signing workflow. The result is labeled
**Autographed** wherever OpenSig presents its verification status.

**Why this priority** Certification is optional and ordinary signing must
remain usable for entities that decline it or keep their records outside OpenSig.

**Independent test** Complete the mock Viktory consent with no governance
records and inspect the delivered document, verification view and package.

1. **Given** an autograph-only request with no authority records, **when** the
   recipient completes the ordinary review and signing steps, **then** the
   document completes as **Autographed** and carries no blue checkmark.
2. **Given** an autograph-only result whose organization seal verifies,
   **when** a recipient reviews its verification status, **then** OpenSig
   reports the seal result and **Autographed** status without awarding the badge.

### User Story 2 - Submit and review corporate records (Priority P1)

An authorized company submitter uploads governing records or selects them from
an authorized SharePoint library, including one backing a Teams channel. The
intake UI shows the entity, documents, versions, and what is ready for review.
An authorized reviewer confirms provenance and current applicability, requests
missing evidence, and records the approved authority basis.

**Why this priority** The certification decision depends on a reliable record
of who supplied the documents, which versions apply, and how they were verified.

**Independent test** Submit synthetic Viktory records through upload and
through a permission-scoped SharePoint selection. Review both submissions and
verify that the same authority can be established with either source.

1. **Given** a draft with a verified entity record, **when** an authorized
   submitter uploads or selects records, **then** the UI shows the submitted
   versions and review status and allows missing or replacement records to be added.
2. **Given** a submitted operating agreement whose source is unverified,
   **when** the reviewer checks it, **then** the reviewer can request evidence
   and the record remains ineligible for use in certification until verified.
3. **Given** a file outside the submitter's allowed SharePoint scope, **when**
   the submitter tries to select or import it, **then** OpenSig denies access
   without displaying or importing the file's contents or metadata.

### User Story 3 - Complete fully certified signing (Priority P1)

A sender opts in before inviting the signer. OpenSig verifies that the
entity's governing records, appointment and any additional approval cover the
exact act. The recipient proves their identity at signing, sees the document
and their representative capacity, and explicitly signs.

**Why this priority** The blue checkmark gives recipients a specific,
verifiable claim about the human's authority for this document.

**Independent test** Complete two versions of the mock Viktory QA flow. Use
verified standing authority in one and a duly adopted transaction-specific
resolution in the other.

1. **Given** an operative agreement and verified appointment that authorize
   one manager to accept the appointment alone, **when** that manager proves
   their identity and signs, **then** OpenSig completes with the blue
   **Signing authority verified** checkmark and records the standing grant.
2. **Given** the operating agreement requires separate approval for this
   consent, **when** the competent body validly adopts a resolution covering
   the consent and the authorized signer proves their identity and signs,
   **then** OpenSig completes with the checkmark and identifies the resolution.
3. **Given** evidence is missing, stale or contradictory, an identity does
   not match, or a required human has yet to sign, **when** certification is
   attempted, **then** it stays pending with a reason and no checkmark.
4. **Given** a completed certified document, **when** a recipient verifies
   that exact revision, **then** they see the verified capacity and outcome.
   A modified document fails certification verification.

### Edge Cases

- Preparation may continue while the authority record is incomplete. An
  invitation or an operator's approval to send does not satisfy a human
  identity, signature or authority requirement.
- If standing authority has a limit, applies to a different entity or act,
  or requires a second signer, the workflow holds certification until the
  applicable requirements are satisfied.
- A disputed or superseded governance document, a revoked grant, or an
  unverified appointment cannot serve as the basis for a signature made
  while that issue applies. Evidence records preserve the time each human signed.
- If an imported source file changes or becomes inaccessible, a retained
  submission continues to identify the exact material reviewed. Any new
  version must be submitted and reviewed before it can support a new decision.
  If current applicability cannot be established, certification stays pending.
- Any change in mode or substantive document revision after invitation
  requires the sender to issue an updated signing request. Earlier approvals
  remain in the history and do not count for the updated request.

## Requirements

### Functional Requirements

- **FR-001** OpenSig MUST support **Autographed** and opt-in **Fully certified**
  entity signing. The sender and signer MUST be able to see the selected mode.
  Existing ordinary signing and new requests without an opt-in MUST use
  autograph-only rules.
- **FR-002** Autograph-only completion MUST retain the ordinary human signing
  requirements. OpenSig MUST allow that workflow without authority evidence
  and MUST show **Autographed** with no blue checkmark.
- **FR-003** During preparation for certification, OpenSig MUST obtain and
  record the official registry source, entity legal name, jurisdiction,
  registration ID, good-standing result and time checked. The draft MAY be
  prepared before a human is named or identity-proofed.
- **FR-004** OpenSig MUST provide an intake UI for authorized submitters to
  upload documents or select specific files from an authorized SharePoint
  library. The UI MUST support governing documents, amendments, formation and
  appointment records, resolutions, and corroborating evidence.
- **FR-005** The SharePoint path MUST honor both OpenSig and source-library
  access. It MUST read only explicitly selected files within the approved
  scope and MUST leave source files and their permissions intact. If access
  is denied, the import MUST fail without exposing restricted information or
  increasing access.
- **FR-006** OpenSig MUST retain the exact source version submitted for review,
  with the entity, document type, source and version reference where
  available, submitter, and submission time. Authorized users MUST be able to
  see whether a submission is awaiting review, needs more evidence, is
  verified, has been rejected, or has been superseded.
- **FR-007** A reviewer authorized for the entity MUST verify the provenance
  and effective version of the bylaws or operating agreement and amendments
  used for certification. The reviewer MUST corroborate governing records and
  appointments through an independently established source, such as a verified
  records custodian or company counsel. A title, upload or location in Teams
  or SharePoint alone MUST NOT qualify as verification.
- **FR-008** The authority decision MUST identify the entity, act, authorized
  role or person, source provisions, effective dates, scope limits, and any
  required additional approvers or co-signers. It MUST include evidence of
  current appointment when relying on a role.
- **FR-009** If standing authority does not cover the act or separate approval
  is required, OpenSig MUST require a valid resolution or written consent.
  An authorized reviewer MUST verify that the competent body adopted it under
  the governing rules, that the required approvals were obtained, and that it
  is effective and covers the proposed act, signer or role, and any limits.
  A resolution that conflicts with a binding restriction MUST NOT satisfy the gate.
- **FR-010** At each certified signing action, OpenSig MUST verify the human's
  identity, match that human to the applicable appointment or named grant,
  and check the authority and approval were effective at that signing time.
  OpenSig MUST capture explicit intent for the exact revision and capacity.
  An agent or service credential MUST NOT supply a human's identity proof
  or signing action.
- **FR-011** OpenSig MUST keep the request pending in **Fully certified** mode
  until the official registry confirms the entity is in good standing and the
  governing records, transaction authority and every required signature
  qualify. OpenSig MUST withhold completed status, the final certification
  package and the blue checkmark until the checks pass. It MUST show the
  missing or failed check to authorized users and protect private evidence
  from other users.
- **FR-012** OpenSig MUST require an explicit sender action to switch modes.
  If the signing request has already been sent, a mode change or substantive
  document revision MUST require a new request and fresh human approvals.
  Consent given for an earlier request MUST remain traceable in its history.
- **FR-013** The blue checkmark and the text **Signing authority verified**
  MUST appear only for the completed certified revision supported by the
  verification evidence. The visual indicator MUST have a readable text
  equivalent. A verified PDF seal alone MUST NOT yield the authority badge.
  Modification of the certified document MUST cause verification to fail.
- **FR-014** The certification package MUST bind the completed revision to the
  entity, verified signing capacities, applicable governing version and
  provisions, any required resolutions, and the signing-time check records.
  A recipient MUST be able to verify the result and see a safe summary.
  Full governance documents and identity evidence MUST be restricted to
  authorized reviewers and other authorized evidence recipients.
- **FR-015** The first acceptance run MUST use the isolated synthetic Viktory
  registered-agent consent for every applicable case below. It MUST use test
  records, identities and delivery. It MUST NOT use production signing, mail,
  corporate records, credentials, or submit any state filing.

### Key Entities

- **Entity record** carries registry identity and dated standing evidence.
- **Governance submission** identifies the exact source material and version,
  provenance, effective dates, and review decision.
- **Authority decision** binds a document's act and revision to the people or
  roles permitted to sign, the governing basis, and required approvals.
- **Signing event** records the verified human, capacity, authority check
  time, document revision, and explicit signing action.
- **Completion evidence** identifies the selected mode, the verified document,
  and the claims a recipient can verify.

## Success Criteria

### Measurable Outcomes

- **SC-001** The autograph-only QA flow completes with zero governance
  submissions and zero authority badges. Its ordinary seal can still verify.
- **SC-002** Both authorized Viktory fixtures qualify after the signer completes
  signing-time checks. Each package identifies its distinct authority basis.
- **SC-003** Every negative certification case below withholds the badge and
  produces the documented pending or failed verification outcome.
- **SC-004** Both authorized intake paths submit the same exact fixture
  version and support the same review decision. Every denied import yields
  zero imported files and zero disclosure of restricted item details.
- **SC-005** Every certified result is verifiable for the completed revision,
  identifies the signing capacity and time, and exposes zero private source
  documents to an unauthorized recipient.

### First QA Case - Mock Viktory Registered-Agent Consent

In isolated QA, prepare the synthetic one-page Suite 485 consent from Viktory
Management, LLC to MedxRx LLC. Use synthetic versions of registry data,
operating agreements and amendments, appointment records, and human identities.
Provide one fixture with valid standing authority, one with a separate
approval requirement and a valid resolution, and variants for negative cases.
For the SharePoint path, use authorized synthetic files in a controlled test
library. Record which integration or fixture was exercised.

| Case | Exercise | Pass evidence | Requirements |
| --- | --- | --- | --- |
| VIK-01 | Prepare and complete an autograph-only consent without governing records. Verify its ordinary seal. | The result and package show **Autographed** and no blue checkmark even when the seal passes. | FR-001, FR-002, FR-013 |
| VIK-02 | Prepare a certified draft, verify the mock registry result and upload the governance records. Do not identity-proof the recipient during preparation. | The draft shows the matched entity, exact submitted versions, source and review status. No signer identity proof is recorded before signing. | FR-003, FR-004, FR-006, FR-010 |
| VIK-03 | Select the same records from a Teams-backed or direct SharePoint library. Attempt import from a denied location and replace a source file after import. | The authorized import records the exact reviewed version and matches the upload decision. The denied item leaks no details. A changed source file cannot replace reviewed evidence without a new submission and review. | FR-004, FR-005, FR-006, FR-007 |
| VIK-04 | Certify the standing-authority fixture with the verified, currently appointed manager. | Signing-time evidence matches the human, capacity, authority and exact revision. The completed package and verification view show the blue checkmark and standing grant. | FR-007, FR-008, FR-010, FR-013, FR-014 |
| VIK-05 | Certify the separate-approval fixture using a duly adopted, effective resolution for this consent. | The checkmark appears only after the same identity and signature checks pass. The package identifies the valid resolution and scope. | FR-008, FR-009, FR-010, FR-013, FR-014 |
| VIK-06 | Supply no governing documents, an unverified upload, or a superseded agreement. | Each affected run stays pending with a reason. The reviewer can request or review a replacement. No checkmark appears. | FR-006, FR-007, FR-011 |
| VIK-07 | Omit a required resolution. Repeat with an unauthorized approver, a conflicting grant, an expired approval, or an approval for another act or entity. | Every variant stays pending with a reason. No checkmark appears. | FR-008, FR-009, FR-011 |
| VIK-08 | Use a mismatched, unavailable or adverse registry result. Repeat with the wrong human, an unverified appointment, or authority withdrawn before signing. Attempt the human signing action using a service credential. | The request stays pending in each run. OpenSig issues no completed certification package or badge. | FR-003, FR-007, FR-008, FR-010, FR-011 |
| VIK-09 | Use a fixture requiring two signers. Complete one signature, then verify and collect the second required signature. | The first signature leaves certification pending. The checkmark appears only after both distinct required signatures qualify. | FR-008, FR-010, FR-011, FR-013 |
| VIK-10 | Leave certification pending, explicitly switch to autograph-only after invitation, and attempt to reuse prior consent. Repeat with a substantive document revision. | The earlier request cannot complete the updated one. Fresh approvals complete an explicitly selected autograph-only request without a checkmark. | FR-001, FR-002, FR-011, FR-012 |
| VIK-11 | Verify a certified package as an authorized reviewer and as an ordinary recipient. Modify the completed document and verify it again. | The reviewer can trace retained authority evidence. The recipient sees the certified result and safe summary without private records. The altered document fails verification. | FR-013, FR-014 |
| VIK-12 | Inspect the recorded environment, files, identities and delivery for the acceptance run. | The run uses synthetic records and test delivery in isolated QA. It creates no production signing or filing activity. | FR-015 |

## Assumptions

- Feature 002 builds on the Corporate Signing MVP's ordinary human approval,
  protected document and evidence package. Organization seals and any existing
  human confirmation to send are independent of signing authority verification.
- An OpenSig intake view in a Microsoft Teams app is the proposed first
  interface. Teams channel files are stored in SharePoint. Direct upload
  remains available, and the same review rules apply to either intake route.
  Tenant approval and a scoped Microsoft 365 test library are prerequisites
  for a live connector pilot. [Microsoft Teams and SharePoint integration](https://learn.microsoft.com/en-gb/microsoftteams/sharepoint-onedrive-interact)
- An authorized reviewer makes the authority decision when judgment is
  required. The verification claim applies to the evidence for the specific
  act and revision. The first QA run makes no determination about a real
  signer, an actual filing or the legal enforceability of an agreement.
- The feature does not require a public key filing, changes to source-library
  records, a production tenant integration or a notarization service.
