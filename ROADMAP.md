# openSig Roadmap

Planning snapshot for 2026-11-22. Milestones are ordered by their prerequisites. Dates and individual assignees are open.

openSig brings authenticated human approval and independently verifiable PDF protection to corporate documents. openAutograph handles review and consent. openCryptograph seals the approved PDF and verifies the resulting package. Feature 001 starts with one PDF and one or two sequential recipients.

## Delivery sequence

| Milestone | Status | Exit evidence |
| --- | --- | --- |
| Triad foundation | Scaffolded. Foundation PRs are open. | Spec and code foundation changes land through their PRs, and assembly validation confirms both exact pins. [Assembly #1](https://github.com/opensoft/openSig/pull/1), [spec #1](https://github.com/opensoft/openSig-spec/pull/1), [code #1](https://github.com/opensoft/openSig-code/pull/1) |
| Corporate approval (US1) | Independent Phase 3 checkpoint closed for synthetic workflows. | Recorded one- and two-recipient approval flows with authenticated review and exact-version consent. [Phase 3 receipt](https://github.com/opensoft/openSig-spec/blob/7a489258c1d8ae695e39a585e8939758f47bbe94/specs/001-corporate-signing-mvp/receipts/phase-3-checkpoint.md) |
| Protected PDF and evidence package (US2) | **Current milestone.** T017 and the independent Phase 4 checkpoint remain open. | Record scope admission and independent acceptance for the corrected T017 candidate and its T030 test follow-up, including the trust-anchor control. After accepted publication, run the complete independent Phase 4 suite and typecheck on the exact published code commit and record the receipt. [MVP tasks](https://github.com/opensoft/openSig-spec/blob/7a489258c1d8ae695e39a585e8939758f47bbe94/specs/001-corporate-signing-mvp/tasks.md), [code PR #2](https://github.com/opensoft/openSig-code/pull/2) |
| Hermes and shared consumer contracts (US3) | Queued after Phase 4 closes. | Demonstrate the bounded local Hermes workflow through human approval and verified package retrieval. Prove webhook behavior, the synthetic openChart and openPractice consumer contracts, and signing with anchoring disabled or simulated. [MVP specification](https://github.com/opensoft/openSig-spec/blob/7a489258c1d8ae695e39a585e8939758f47bbe94/specs/001-corporate-signing-mvp/spec.md) |
| MVP qualification and assembly | Queued. | Wire CI, document pilot prerequisites and rollback, and record tree-bound synthetic acceptance. Reconcile feature amendments, review and land both legs through their PRs, and advance the matching assembly pins through the lockstep gate. [MVP plan](https://github.com/opensoft/openSig-spec/blob/7a489258c1d8ae695e39a585e8939758f47bbe94/specs/001-corporate-signing-mvp/plan.md), [spec PR #2](https://github.com/opensoft/openSig-spec/pull/2), [reconciliation issue](https://github.com/opensoft/openSig-spec/issues/3) |
| Authorized corporate pilot | Pending release qualification and approval. | Record evidence for trusted certificate and key custody, validated OIDC and SMTP, an external PDF reader, declared hardware, real CI, production Hermes, and parser network isolation. Demonstrate recovery and rollback before an authorized corporate signing workflow. [Acceptance runbook](https://github.com/opensoft/openSig-spec/blob/7a489258c1d8ae695e39a585e8939758f47bbe94/specs/001-corporate-signing-mvp/quickstart.md) |

The published MVP code PR remains a draft. Local passing results for the corrected US2 candidate are recorded separately from scope admission, independent acceptance and the exact published commit's Phase 4 receipt. [MVP tasks](https://github.com/opensoft/openSig-spec/blob/7a489258c1d8ae695e39a585e8939758f47bbe94/specs/001-corporate-signing-mvp/tasks.md), [code PR #2](https://github.com/opensoft/openSig-code/pull/2)

## Optional signing authority verification

Planned follow-on to the corporate signing MVP. Offer entity signing in two
modes. **Autographed** completes the ordinary signing workflow and carries no
blue checkmark. **Fully certified** is opt-in and earns the blue checkmark
labeled **Signing authority verified** only when all required authority and
signing checks pass for the completed document.

- **Entity and governing records.** In preparation, confirm the entity's legal
  name, jurisdiction, registration ID and good standing against the official
  registry. Require its operative bylaws or operating agreement, including
  amendments, and verify their provenance through an independently established
  source. Use the formation and appointment records needed to establish who
  actually holds the applicable office or role. An uploaded copy and a
  self-asserted title alone are insufficient.
- **Authority for the act.** Record the governing provision or existing
  delegation, its effective dates, scope, limits and any co-signer requirements.
  When standing authority does not cover the act or separate approval is
  required, require a valid resolution or written consent from the governing
  body empowered to approve it. Verify due adoption, effective status and scope
  before relying on it.
- **Human verification at signing.** Verify each human's identity when they
  sign, match them to the verified appointment or named grant, and check that
  their authority is still in effect. Capture their intent for the exact
  document revision and their capacity. A certification request stays pending
  until all required signatures and authority checks pass. Switching to
  autograph-only requires an explicit choice.
- **Verification evidence.** Bind the certification and blue checkmark to the
  exact completed document and its evidence package. Retain the governing
  version, applicable provisions, appointment and any resolution, and the date
  and result of each check. Keep private governance records available for
  authorized review. A valid PDF seal alone does not qualify for the checkmark.

### First QA case — mock Viktory registered-agent consent

Use the synthetic Suite 485 consent from Viktory Management, LLC to MedxRx LLC
in isolated QA. Model the operating agreement, appointments, resolutions and
human identities with synthetic records.

- Complete the autograph-only flow and verify that the result is labeled
  **Autographed** and has no blue checkmark.
- Opt into certification and prove that verified standing authority for one
  signer can produce a blue checkmark after human identity and authority are
  checked at signing. Exercise a second fixture in which the same consent
  needs separate approval and a valid resolution supplies that authority.
- Verify that certification stays pending and the blue checkmark is withheld
  for missing or unverified governing records, a missing or invalid required
  resolution, a signer mismatch, revoked authority, or a missing required
  co-signature. Verify that the certified package records the authority basis
  and that verification fails for a modified document.

## Later work

- **openChart.** Qualify the native consent and provenance workflow with synthetic records. Operational privacy and security acceptance precedes use of patient data. Clinical authority stays in openChart. The openWallet-backed attestation proposal awaits ratification and is a separate dependency for the clinical acts workflow that uses it. [Wallet-bound attestation draft](https://github.com/opensoft/openSig-spec/pull/4)
- **openPractice.** Qualify native administrative agreements once the application foundation supports an end-to-end workflow. Verify account and practice attachment and preserve financial authority in openPractice.
- **Optional anchoring.** Consider a live adapter once the signing artifact contract is stable and the ledger policy and budget are approved. Qualify independent commitment verification, retry and reorganization handling. Ordinary signing works with anchoring disabled.

Use the [original implementation plan](ideation/brainstorm/opensig-implementation-plan.md) for product direction and the [feature task list](https://github.com/opensoft/openSig-spec/blob/001-corporate-signing-mvp/specs/001-corporate-signing-mvp/tasks.md) for current task status and check commands. Update this roadmap when a checkpoint receipt lands, assembly pins advance, release qualifications change, or a proposal is ratified.
