Status: proposed. A draft for Brett Heap's review under his ruling 153; nothing here is ratified. Drafted on 2026-11-15 by Claude (Opus 5.5).

## Why

openChart is the clinical chart of a small medical practice. Its owner, Brett Heap, ruled that every medical decision entered in the chart needs the practitioner's own sign-off, made by a logged-in user with their openWallet and openSig (ruling 151): "the openWallet will have the verification of license and the openSig will attest that it is from that wallet."

Nothing in openSig attests that a signature came from an openWallet wallet. openSig today is a document-signing platform: in feature 001 a person approves an exact PDF and an organizational key seals it. Its drafts do not mention openWallet, and they use the word "wallet" only for a ledger wallet that pays for optional anchoring. So openChart's draft `define-clinical-acts-foundation` (openChart-spec#5) fails closed: until openWallet and openSig can do their part, no real decision is recorded (ruling 152).

On 2026-11-15 the owner accepted the recommendation that openSig gets a draft of its own: "a wallet-bound attestation in openSig" (ruling 153). He also ruled that openChart decides whether a sign-off counts and openSig only attests that the signature came from that wallet (155), and that a sign-off is checkable inside the practice's own deployment (156). This change is that draft. Its openWallet half is the draft change `add-practitioner-profile-and-licence-verification` in `opensoft/openWallet-spec`.

## What Changes

- Add a capability that, given a relying party's single-use challenge reference, a digest and a proof made under openWallet's ratified possession-only profile, verifies the proof with openWallet's own verification against the wallet key's current binding, and records the result as a signed, durable attestation.
- An attestation states the wallet, the key and its fingerprint, the custody the key declares, the binding it was checked against, the digest, the relying party and its challenge reference, the time, the result and the verification used. It states nothing about the content behind the digest, the signer's licence or authority, or whether a sign-off counts, and openSig never receives the content.
- Attestations are produced, and verified again later, inside the relying party's own deployment, with no outside call; the change says which keys and records the deployment holds for that.
- openSig never holds a holder's key, signs in their place or approves for them. It fails closed with distinct results for a request it refuses, a proof that verified, a proof that did not, and a check it could not complete.
- "Wallet" in this capability means an openWallet wallet, never an anchoring wallet.
- Feature 001 and its change `add-corporate-signing-mvp` are unchanged.

## Capabilities

### New Capabilities

- `wallet-bound-attestation`: openSig's durable, offline-verifiable attestation that a given proof over a given digest came from a given openWallet wallet's declared key.

### Modified Capabilities

None. `corporate-signing-mvp` (proposed in `add-corporate-signing-mvp`) is unchanged.

## Impact

What the rulings settle, what this change proposes beyond them (marked PROPOSED in `design.md`), and the open questions for the owner are in `design.md`. Implementation would belong in openSig-code; canonical governance and feature files belong in openSig-spec. The realization depends on openWallet's verification of its possession-only profile, pinned through openWallet's assembly root, and on openWallet's draft practitioner profile for the seam both drafts share. No Hermes, healthcare or production configuration is changed, no key is created, and no contract, code or release file changes. This change is stacked on the unmerged foundation pull requests #1 and #2, whose OpenSpec instance it uses; it changes nothing they own.

## Handoff

Exactly one Speckit feature, its number allocated at handoff, would own the executable implementation tasks. This proposal and its delta are proposed requirements, not ratified or shipped behavior. Do not archive until implementation, leg PRs and final assembly pins land.
