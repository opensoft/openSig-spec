## ADDED Requirements

### Requirement: Scoped immutable envelopes
The system SHALL isolate tenants and support exactly one PDF, one or two sequential recipients, frozen field definitions and revision-bound operations. It SHALL reject encrypted, already digitally signed, malformed or over-limit PDFs before sending.

#### Scenario: Frozen version and tenant checks
- **WHEN** a caller edits a sent revision or accesses another tenant envelope
- **THEN** the request is rejected without changing the envelope or exposing its existence

### Requirement: Explicit authenticated human approval
The system SHALL bind recipient mailbox-control authentication, explicit consent and visible signature/date values to an exact frozen document/manifest and prior approval digest. It SHALL enforce recipient order, expiry and revocation and SHALL NOT give service accounts human approval capability.

#### Scenario: Wrong recipient or expired session
- **WHEN** an out-of-order recipient, expired session or Hermes service account attempts approval
- **THEN** approval is rejected; the correct recipient can review and approve only its bound revision

### Requirement: Verified organization seal
The system SHALL compose approved inputs, apply an organizational certificate signature, independently verify protected bytes and archive exact final bytes plus evidence before completion. It SHALL label certificate trust and signer type honestly and detect later modifications.

#### Scenario: Finalization fails verification
- **WHEN** verification fails or a worker crashes before durable completion
- **THEN** the envelope does not report completed; safe retry produces at most one completed package

### Requirement: Recoverable lifecycle and evidence
The system SHALL provide distinct draft, prepared, sent, in-progress, finalizing, completed, declined, expired and voided outcomes; finalization failure SHALL remain retryable with a bounded error state. It SHALL deduplicate mutations/events and preserve revision lineage and audit evidence.

#### Scenario: Replay and concurrent void
- **WHEN** the same approval/event is replayed or void races with finalization
- **THEN** one valid state transition wins atomically; no duplicate approval or incompatible final artifact is published

### Requirement: Bounded consumer integration
Hermes SHALL prepare, request within authorization, check status and retrieve verified packages without private keys or personal approval. openChart/openPractice contracts SHALL preserve source version and domain authority and use synthetic harnesses in this feature.

#### Scenario: Consumer replays stale completion
- **WHEN** a consumer receives duplicate or stale-version completion
- **THEN** it deduplicates or rejects attachment and cannot independently attest a clinical record or initiate payment/claim action

### Requirement: Optional anchoring boundary
Ordinary signing SHALL work with anchoring disabled. MVP SHALL provide only disabled/fake adapters; fake proofs SHALL be labeled non-ledger and SHALL NOT submit transactions or contain patient/document contents.

#### Scenario: Anchoring unavailable
- **WHEN** an anchoring adapter is disabled or simulates failure
- **THEN** signing completes normally and no real ledger proof is claimed

### Requirement: License and release evidence
All original materials SHALL use Apache-2.0. Every application artifact SHALL pass the MIT/Apache allowlist with retained notices and an SBOM, with no waiver: distributed code, libraries, bundled assets and transitive application dependencies. Pre-existing platforms SHALL be inventoried separately with their licenses and SHALL NOT be required to be MIT/Apache-only: the Node runtime binary and its embedded components, the OS/base image, the browser and any JVM. Release SHALL require source-bound signing, trust, isolation and recovery acceptance evidence.

#### Scenario: Disallowed application artifact
- **WHEN** a transitive application dependency, library or bundled asset has an unknown or disallowed license
- **THEN** the release gate fails without changing the policy or waiving the artifact

#### Scenario: Platform component
- **WHEN** a pre-existing platform component (for example a library embedded in the Node runtime binary, or an OS package) carries a non-MIT/Apache license
- **THEN** it is recorded in the separate platform inventory, and the application license gate is unaffected
