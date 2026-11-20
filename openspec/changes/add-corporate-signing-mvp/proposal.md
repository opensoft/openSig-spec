## Why

openSig needs its first usable corporate signing flow: a person approves an exact PDF through openAutograph and openCryptograph seals and verifies the result. This feature turns the captured architecture into one bounded implementation target for Hermes, with reusable integration contracts for openChart and openPractice.

## What Changes

- Add tenant-scoped PDF envelopes with frozen revisions, one or two sequential recipients, signature/date fields, explicit consent and authenticated approval.
- Add organizational certificate sealing, independent PDF verification, durable evidence, expiration/void handling and recoverable finalization.
- Add bounded Hermes prepare/request/status/retrieve tools and a real synthetic end-to-end acceptance flow.
- Define openChart/openPractice adapter contracts and synthetic consumer harnesses; no PHI or production EHR/practice integration in round one.
- Ship disabled and fake anchoring interfaces; live blockchain submission, wallets, fees and network selection remain excluded.
- Keep all original implementation Apache-2.0 and gate shipped third-party artifacts through the MIT/Apache allowlist.

## Capabilities

### New Capabilities

- `corporate-signing-mvp`: end-to-end human approval, organizational PDF sealing, evidence retrieval and bounded consumer contracts.

### Modified Capabilities

None; this is the first executable feature proposal.

## Impact

Implementation belongs in openSig-code; canonical governance and feature files belong in openSig-spec. No Hermes or healthcare production configuration is changed by this proposal. Database/object storage, signer worker, identity, notifications and third-party PDF dependencies require qualification. The foundation PRs remain unmerged dependencies; implementation must preserve their exact reviewed baselines and later reconcile onto main.

## Handoff

Exactly one Speckit feature, `001-corporate-signing-mvp`, owns executable implementation tasks. This proposal and its delta are proposed requirements, not ratified or shipped behavior. Do not archive until implementation, leg PRs and final assembly pins land. The user authorized creation/documentation of this feature, not production deployment or legal execution.
