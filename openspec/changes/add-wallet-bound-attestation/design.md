## Context

Drafted on 2026-11-15 by Claude (Opus 5.5), on Brett Heap's ruling 153, as a draft for his review, and revised the same day after an independent review. **Nothing here is ratified.** The owner's rulings quoted below are his decisions; every other choice in this change is a design call, marked PROPOSED where it is made and listed in "Design calls" below, so that it can be confirmed or overturned one by one.

The consumer is openChart's draft `define-clinical-acts-foundation` (openChart-spec#5, read at `d9a9c63`). Its practitioner's sign-off checks, among other things, that "the openSig attestation verifies against that wallet, over the SHA-256 of the sign-off statement" (its CA-04). Nothing in openSig provides that today, so every sign-off fails closed and drafts stay drafts (its AN-14; ruling 152). This change specifies the attestation. openWallet's draft `add-practitioner-profile-and-licence-verification` specifies the licence verification and what a sign-off's proof is. The two share one seam, stated in the same words in both designs.

This change says what openSig provides. It does not restate or change openChart's rules; where it checks its design against openChart's needs, it cites them.

**Read for this change**, at these heads:

- `opensoft/openSig-spec`: `main` at `23ebbfe`; pull request #1 (`docs/opensig-foundation`) at `8668f30`; pull request #2 (`001-corporate-signing-mvp`) at `a05fcee`, including `add-corporate-signing-mvp` and the agent instructions both pull requests add;
- `opensoft/openSig` (assembly root): `main` at `d6aca3f` and its pull request #1;
- `opensoft/openWallet-spec` `main` at `a2d554d`: the canonical `openxwallet`, the ratified possession-only profile in `add-challenge-proof-runtime` with its ratification record and feature 016, and `add-reference-registry-custody`; and `opensoft/openWallet-code` at `bb0e9da`, read only, for the closed custody registry and the record schema;
- openChart-spec#5 at `d9a9c63`: Decision 4, Decision 19, Open Question 11 (Open Question 10 is closed there by ruling 156), the requirements AN-14, CA-04, CA-08 and CA-14, and the content schema's sign-off evidence.

## The owner's rulings

Every row is a ruling by **Brett Heap**. **Rulings are not ratification.** This change stays proposed until he ratifies it.

**Ruling 151**, 2026-11-15, his words, verbatim:

> "for 12, and any medical decision we need the practitioner to sign off. so we need a logged in user that we use their openWallet and openSig to sign and validate that entry. the openWallet will have the verification of license and the openSig will attest that it is from that wallet."

"12" is clause 12 of the approved wording of his ruling 80, which openChart-spec#5 records in its public form. Where ruling 151 and a clause of ruling 80 differ, ruling 151 is the later ruling and governs.

**Rulings 152, 153, 155, 156 and 186.** On 2026-11-15 he answered the decisions numbered 152 to 194 with the words "accept your recs for 152-194". Each became a ruling in the words of the recommendation as last presented to him. The rows that bind this change, question and ruling verbatim:

| No. | Question | Ruling |
|---|---|---|
| 152 | Until openWallet and openSig can do their part, does openChart record any decision? | No real decision until both exist. It can still be built and tested against stand-ins. |
| 153 | Draft the missing pieces as changes in those two repos? | Yes: a practitioner profile carrying a licence verification in openWallet, and a wallet-bound attestation in openSig, each as a draft for your review. |
| 155 | openSig keeps clinical attestation in openChart. | Leave that as is. openChart decides whether a sign-off counts; openSig only attests that the signature came from that wallet. |
| 156 | openChart promises the clinical record works with no outside system. Where must a sign-off be checkable? | Inside the practice's own deployment, so the chart never waits on an outside service to accept a decision. |
| 186 | Ruling 151 has the practitioner sign as a logged-in user. Logged in where: must it be openChart itself, or may the sign-off be made in another system and checked by the chart? | In openChart only, in version 1, as ruling 35 has physicians working there. A sign-off made in another system counts later, once openWallet and openSig can prove it to the chart. |

## Terms

This change uses these words in these senses, and no other (PROPOSED, WA-1).

| Term | Meaning here | Not to be confused with |
|---|---|---|
| wallet | An openWallet wallet: a signing key, or a declared set of signing keys, held by a holder, as openWallet's `openxwallet` capability defines it. | The ledger wallet that pays for optional anchoring in openSig's foundation documents. That wallet is never attested, never an attestation key and never consulted here. |
| key binding, registry | The trusted record of a wallet key's current binding (wallet, key, public key, custody reference, revision, validity) that openWallet's possession-only profile verifies a proof against, and the registry that holds it. | openSig's envelope records. |
| held wallet record | The relying party's copy of a practitioner's wallet record, taken from the practitioner, from which the custody each key declares is read. | The wallet itself, which stays with its holder. |
| proof | A proof of possession made under openWallet's ratified `wallet-challenge-proof` profile. | A PDF signature, an organizational seal, an approval event. |
| wallet-bound attestation | openSig's signed record that a given proof over a given digest did or did not verify against a given wallet key's binding at a given time. | openChart's clinical attestation statement, which is the practice's coded wording of what a signer attests; feature 001's organizational seal; an anchoring receipt. |
| relying party | The authenticated caller that issued the challenge and asks for the attestation: openChart, for a practitioner's sign-off. | A signer or recipient in feature 001. |
| attester | The openSig component that makes and signs wallet-bound attestations, running inside the relying party's deployment. | Feature 001's signer worker. |

## What this change stands on

| Change or record | Standing | How this change uses it |
|---|---|---|
| openWallet `add-challenge-proof-runtime` (capability `wallet-challenge-proof`) | ratified 2026-11-10 for its possession-only wire profile, not yet archived; runtime acceptance pending | The proof openSig verifies, with openWallet's own verification (W2), within the limits its ratification states (W4). |
| openWallet's closed custody registry, version 1 | ratified | The custody models an attestation records (W3). |
| openWallet `add-reference-registry-custody` | commissioned implementation, not a ratified contract | One way to run the key binding registry inside a deployment (W5). |
| openWallet draft `add-practitioner-profile-and-licence-verification` | proposed | Says what a sign-off's proof is made over, how keys are bound, and the custody reference form, and states the seam in the same words (W2, W3; "The seam"). |
| openSig pull requests #1 and #2, `add-corporate-signing-mvp` | open, not merged, not ratified | This change uses #2's OpenSpec instance and changes nothing either owns (W10, W11). |

## Decisions

### W1 — One new capability, separate from feature 001, and what it adds (PROPOSED, WA-1 and WA-2)

`wallet-bound-attestation` is a capability of its own. It adds nothing to `corporate-signing-mvp` and removes nothing from it, and it shares no record with feature 001's envelopes. It defines its terms (above) so that "wallet" cannot be read as the anchoring wallet and "attestation" cannot be read as a clinical attestation.

**What a wallet-bound attestation adds, stated without overstating it.** openChart's Open Question 11 offered the owner two readings: an openSig change, or openWallet's own proof as the attestation. Ruling 153 chose an openSig draft, and ruling 151 says "the openSig will attest that it is from that wallet". What openSig's attestation adds to openWallet's proof is:

- **a sealed record of the binding and the time.** openWallet's profile verifies a proof against the CURRENT binding, and "an old proof refuses" once a binding is revoked, expired or revised. The attestation seals the proof with the binding it was checked against and the time it was checked, so the result can be shown again after the practitioner's key has rotated;
- **a single-purpose key in a separate process.** The attester signs with a key used for nothing else, held by a component other than the chart's application;
- **a second check, and replay refusal.** The attester runs openWallet's verification itself, and refuses a challenge reference or a digest it has attested before (W8).

What it does **not** add, and must not be read as adding:

- **independence from whoever runs the deployment.** Ruling 156 puts the attester inside the practice's deployment, under the practice's operator, checking against a registry and wallet records the practice holds. Whoever runs the deployment controls the attester too;
- **outside evidence of time.** Its time is the deployment's clock;
- **a way for the relying party to skip openWallet's verification.** At acceptance the relying party verifies the sealed proof itself, under the sealed public key, over its own recomputed digest, so that a forged attestation cannot stand in for a proof (the seam, "Who verifies what"). A chart that kept binding snapshots itself could re-verify a proof on its own; the attestation's value is that the snapshot and the time are sealed by a single-purpose key in a separate process.

**The options, put to the owner** (Open question 1): (a) the attestation as specified here; (b) the same, with the digests of attestations optionally anchored as a successor change, for outside evidence against back-dating, which never blocks signing because anchoring is optional by openSig's own rule; (c) the same, with the attester run under an administrator other than the chart's. The recommendation is (a) now, with (b) as a named successor.

REJECTED:

- **A mode of feature 001's envelope workflow.** An envelope binds a PDF and its approvals; a sign-off attestation binds a digest the relying party computed. Folding one into the other would make openSig's envelope records carry clinical sign-off traffic, which feature 001's non-goals exclude.
- **openSig verifying the relying party's statement itself.** It would need the statement, which carries the patient and the act (W4).
- **Describing the attestation as signed by "a party other than the chart".** The first draft did. Inside the practice's deployment it is not, in any sense that matters against the operator.

### W2 — What openSig verifies, and with whose verification (PROPOSED, WA-3)

openSig receives a challenge reference, a digest and a proof from an authenticated relying party. It resolves the proof's binding from the key binding registry the deployment holds, and runs openWallet's verification of `wallet-challenge-proof` against it, with the submitted digest as the expected challenge digest. It does not implement a second verification of the profile: one profile, one verification, so the two cannot drift. It then checks that the digest it was given is the digest the proof was made over, and compares the binding's custody reference with the custody the held wallet record declares for that key (W6). It records the result.

The profile already refuses a changed challenge digest, binding, signature or profile; duplicate JSON keys; unknown fields; noncanonical signatures; unsupported algorithms; inline public keys; a proof whose references differ from its binding's; and a binding that is revoked, expired or revised. openSig maps each refusal to a `not_verified` reason (W6), and adds none of its own about the proof's cryptography.

**What openSig cannot check, and says so.** It never sees the statement, so it cannot check that the challenge reference it was given is the one inside the statement behind the digest. It records the challenge reference as the relying party gave it, and refuses a repeat of either the reference or the digest (W8). The relying party, which issued the challenge and wrote the statement, checks that the sealed challenge reference is its statement's, and stays the challenge's owning issuer: the profile says "The owning issuer enforces freshness, caller binding and one-use."

### W3 — What an attestation states (PROPOSED, WA-4)

The fields, at the level the requirement binds; the record contract is realization work (`tasks.md` §2).

| Field | What it carries |
|---|---|
| `attestation_id` | The record's identity, a random value. |
| `profile` | `opensig.wallet-bound-attestation.v1`. |
| `relying_party_ref` | The authenticated caller that asked (W12). |
| `challenge_ref` | The relying party's single-use challenge reference, as it gave it: 32 random bytes in unpadded base64url, which carries no meaning (W4). |
| `challenge_digest` | The digest the proof was made over: `sha256:` and 64 lowercase hex digits. |
| `proof` | openWallet's proof, exactly as received. It holds references and a signature, never key material or content. |
| `binding` | The binding the proof was checked against, as the registry returned it: binding reference and revision, wallet, key, subject and custody references, public key, validity, and when and from which registry it was resolved. |
| `key_fingerprint` | Recomputed by openSig from the binding's public key, in openWallet's spelling: `sha256:` and the lowercase hex SHA-256 of the raw public key. |
| `custody` | The custody model the key declares in the held wallet record, from openWallet's closed registry, with the registry version, and `basis: declared`. |
| `wallet_state`, `key_state` | The wallet's and the key's declared state when checked. |
| `result`, `reason` | `verified`, or `not_verified` with a reason from the closed list of W6. |
| `verification` | Which openWallet verification ran: the profile constant `openwallet.challenge-proof.v0` and the openWallet release the deployment pins. |
| `attested_at` | openSig's time, from the deployment's clock. |
| `attester` | The attester and the attestation key that signed. |
| `signature` | Over the canonical form of every other field (W7). |

The proof's `subject_ref` is the relying party's own reference for the binding's subject; for openChart, its reference for that account's binding of that key. openSig records it as part of the proof and never resolves it.

### W4 — What an attestation never states, and what openSig never receives (ruling 155) (PROPOSED, WA-5)

Ruling 155: "openChart decides whether a sign-off counts; openSig only attests that the signature came from that wallet." So an attestation states nothing about:

- **the content.** openSig never receives the statement, the act or the patient; only the digest. A request that carries anything besides the challenge reference, the digest and the proof is refused, and nothing it carried is stored. openChart's sign-off statement binds the patient, the act and the content digests (its CD-53); keeping it out of openSig keeps patient data out of openSig, which feature 001 and openSig's project rules already require. The challenge reference is sealed in every attestation, so it must carry no meaning: openSig accepts only the fixed opaque form (32 random bytes, unpadded base64url) and refuses any other. That cannot prove a relying party put no meaning into random-looking bytes, but it keeps a readable identifier out;
- **the signer's licence or authority.** That is openWallet's licence verification, checked by the relying party, and the relying party's own grants;
- **the signer's consent.** openWallet's ratified profile does "not assert MFA, human presence, explicit human consent, workload composition, delegation or domain permission", and leaves "separate exact-request human consent" to the relying party. A wallet-bound attestation adds none: the relying party evidences what the signer was shown and their re-authentication;
- **whether the holder was present.** The attestation records the custody the key declares and never raises it. Only `isolated_per_use_authorized` evidences that the holder acted, and even that is a declaration: openWallet's ratified profile "does not attest that the referenced custody or agent composition is actually isolated";
- **whether the sign-off counts.** openChart decides.

A wallet-bound attestation is therefore not clinical attestation, which openSig's project rules keep in openChart, and openSig never presents it as one.

### W5 — Inside the relying party's deployment (ruling 156) (PROPOSED, WA-6)

Ruling 156 puts the check "Inside the practice's own deployment, so the chart never waits on an outside service to accept a decision." For an attestation to be produced there, and verified again there later, the deployment holds:

1. **an openSig attester**, running inside the deployment, with its attestation key in custody there;
2. **the key binding registry** that proofs are checked against, or a current replica of it. openWallet's draft has the relying party run or hold it, populated, for openChart, by its per-key account bindings;
3. **the held wallet records** of the wallets bound there, for the custody and state each key declares;
4. **openSig's attestation public keys**, retired ones included, held by the relying party;
5. **every attestation, with the proof and binding it seals**, kept by the relying party with the record it supports.

Nothing else is contacted or waited on: no hosted openSig, no openWallet service outside the deployment, no directory and no ledger. The practitioner's key stays in the practitioner's custody; producing the proof needs it, checking the proof does not.

**At acceptance and when verifying again,** the relying party needs only items 4 and 5. It checks openSig's signature over the attestation; verifies the sealed proof itself with openWallet's verification, under the sealed binding's public key, over the digest it recomputes from its own statement; checks that the sealed fingerprint is a key it bound and that a licence verification names; and checks that the sealed challenge reference is its statement's. Verifying again later establishes what was true at `attested_at`, and says so: it never claims the binding is still current.

REJECTED:

- **A hosted openSig service that every practice calls.** The chart would wait on it, which ruling 156 forbids.
- **Verifying again by asking the registry.** The registry answers for the current binding only, and an old proof refuses once the binding moves.

### W6 — Four results, fail closed (PROPOSED, WA-7)

Every request gets exactly one result:

| Result | Attestation | When | Reasons (closed, PROPOSED) |
|---|---|---|---|
| `refused` | none, and no check is made | The request is not one openSig accepts. | `caller_not_authenticated`, `request_shape_invalid`, `unexpected_field`, `challenge_already_attested`, `digest_already_attested` |
| `verified` | yes | openWallet's verification passed, the digests agree and the custody references agree. | none |
| `not_verified` | yes, recording the failure | The check was made and failed. | `digest_mismatch`, `signature_invalid`, `proof_malformed`, `profile_unsupported`, `binding_not_found`, `binding_not_current`, `key_not_declared`, `key_retired`, `wallet_not_active`, `custody_reference_mismatch` |
| `unavailable` | none | The check could not be completed. | `binding_registry_unreachable`, `wallet_record_unavailable`, `attester_key_unavailable`, `clock_untrusted` |

**`custody_reference_mismatch`** (closes the gap between an opaque reference and the closed registry). The ratified profile treats `custody_ref` as opaque, and a proof whose custody reference differs from its binding's is already refused by the profile. openWallet's draft defines, for a binding used for a sign-off, the form `openxwallet-custody:<registry_version>:<model>`. `custody_reference_mismatch` means the binding's custody reference is not in that form, or names a model or registry version other than the one the held wallet record declares for that key.

**`clock_untrusted`** means the attester's clock reports itself unsynchronized, or reads earlier than the newest `attested_at` the attester has issued. Both are facts the attester can test locally.

openSig never turns `unavailable` into another result, and treats any doubt it cannot classify as `not_verified`.

**Why "could not complete" and "did not verify" stay distinct.** openChart's AN-14 treats both as failures the signer can cure by trying again, and its closed list has a code for each (`signing_service_unavailable`, `attestation_not_verified`). They mean different things for an operator: an outage is retried; a proof that does not verify over its digest may be a forgery or a replay. A signed `not_verified` attestation is evidence of that attempt, for openChart's trail of failed sign-off attempts (its CA-08). What openChart does with each is openChart's to decide (Open question 11).

### W7 — openSig's attestation key and signature (PROPOSED, WA-8)

**A key of the deployment,** one per deployment, used for wallet-bound attestations and nothing else: never feature 001's organizational seal, never an anchoring key, never a holder's key. It is not declared in an openWallet wallet: ruling 154's exception covers a practitioner's sign-off and the binding it needs, not the deployment's own keys. openSig's foundation documents already keep signing keys separate by purpose.

**The signature** is Ed25519 over the domain separator `openSig.wallet-bound-attestation.v1` and a NUL byte, then the RFC 8785 canonical UTF-8 bytes of every field but `signature`. That is the construction of openWallet's ratified profile with a separator of its own, so an attestation can never verify as a wallet proof, nor a wallet proof as an attestation.

**What the key evidences.** The attester signs automatically, with no person authorizing each signature. In openWallet's custody terms such a key evidences at best that the attester's environment acted (`isolated_invocable`, where the attester's own application process cannot read it), never a person. That is all an attestation claims.

**Rotation keeps history.** A retired attestation key's public half stays with the relying party, so an attestation it signed still verifies.

### W8 — One attestation per challenge reference, and one per digest (PROPOSED, WA-9)

For each relying party, openSig keeps the challenge references and the challenge digests it has attested, and refuses a repeat of either: `challenge_already_attested` or `digest_already_attested`. The digest is the key that matters, because the proof binds it and openSig cannot check the reference against the statement: without it, the same proof and digest submitted with a fresh reference would get a second `verified` attestation. The relying party stays the challenge's owning issuer and enforces one use itself (openChart's CA-04 and CA-08 do); openSig's refusal is a second line, local to the deployment.

### W9 — "Wallet" is never the anchoring wallet (PROPOSED, WA-10)

openSig's foundation documents give optional anchoring "dedicated wallet custody" for fees, and feature 001 excludes "live blockchain submission, wallets, fees and network selection". That wallet is a ledger account. This capability's wallet is an openWallet wallet. The requirement "A wallet is an openWallet wallet, never an anchoring wallet" makes the separation testable: an anchoring wallet's key has no openWallet binding, so a proof from it never verifies, and producing, verifying or relying on an attestation never depends on anchoring. Anchoring the digests of attestations, for outside evidence of time, is a possible successor (W1, option b); it would sit beside the attestation path and never in it.

### W10 — Fit with feature 001 and openSig's project rules (PROPOSED, WA-11)

This change touches nothing in `add-corporate-signing-mvp` or feature 001. It is consistent with what they say:

| Feature 001 or project rule | This change |
|---|---|
| Non-goal: "personal PKI enrollment" | Enrolls no key. Keys are openWallet's; bindings are the relying party's registry. |
| Non-goal: "patient/clinical production workflows" | Receives no patient data or clinical content, and attests no clinical fact. Production use would need its own feature and release gates. |
| "Keep clinical attestation and amendments in openChart" | Kept: ruling 155, and W4. |
| "human personal approval outside Hermes service credentials" | openSig approves nothing. A holder's per-use authorization happens on the device that holds their key. |
| "Never commit real patient data, signature images, credentials, private keys or wallet secrets. Use synthetic fixtures." | This change commits no key of any kind; its realization uses synthetic fixtures only. |
| "Optional anchoring publishes commitments only and does not block ordinary signing" | Attestation works with anchoring disabled and never depends on it (W9). |
| "This is not multiple personal certificate signatures." | Adds no personal certificate signature to a PDF; an attestation is not a PDF signature. |
| A consumer "cannot independently attest a clinical record" | Unchanged; nothing here attests a clinical record. |
| The MIT/Apache allowlist | Applies to the realization. Its one new dependency is openWallet's verification, which is Apache-2.0 and pinned through openWallet's assembly root. |

### W11 — Placement (PROPOSED, WA-12)

openSig-spec's `main` has no OpenSpec instance; pull request #2 adds it (`openspec/config.yaml`), stacked on pull request #1. The assembly root's pull request #1 says that while the foundation pull requests are unmerged, "the intended base for first-feature creation is docs/opensig-foundation". This change is an OpenSpec change, not a feature, and it needs an OpenSpec instance to validate. So it branches from `001-corporate-signing-mvp` at `a05fcee`, adds only its own directory, and its pull request targets that branch. It changes nothing either pull request owns. It can land only after #2, and if #2's branch is rewritten this branch is rebased onto it (Open question 9).

REJECTED: branching from `main` and adding `openspec/config.yaml`, which would create a file pull request #2 owns.

### W12 — Code surface, the relying party's credential, and names (PROPOSED, WA-13)

Named here and performed by nothing in this change:

- openSig code leg: an attester and a verifier for wallet-bound attestations, deployable inside a relying party's deployment, depending on openWallet's verification of `wallet-challenge-proof` at a pinned openWallet assembly root; independently generated known-answer vectors, on openWallet feature 016's precedent;
- an attestation record contract, placed where openSig's governance places contracts;
- no change to feature 001's HTTP contract.

**The relying party's credential to the attester.** openSig authenticates the relying party that asks, and records it. PROPOSED: a credential internal to the deployment, such as mutual TLS with the relying party's client certificate pinned by fingerprint, as openWallet's reference service does, issued when the attester is installed. The relying party holds it; nothing outside the deployment is involved.

Names that would become machine keys once released, all PROPOSED and cheapest to change now: the capability `wallet-bound-attestation`; the profile `opensig.wallet-bound-attestation.v1`; the signing domain `openSig.wallet-bound-attestation.v1`; the four results; and every reason code in W6.

### W13 — Non-goals and named successors (PROPOSED, WA-14)

Each is a scope choice the owner may overturn:

1. **A sign-off made in another system.** Ruling 186: openChart only, in version 1; it counts later, once openWallet and openSig can prove it to the chart. That needs a challenge the chart can trust from another system, and its own change.
2. **Anything about the licence.** openWallet's licence verification, checked by the relying party.
3. **Evidence that a key's custody is what its wallet declares.** A successor in openWallet; openSig records the declaration.
4. **Attesting other proof profiles**, such as the WebAuthn assertion profile openWallet's draft names as a successor, until openWallet ratifies one.
5. **Anchoring the digests of attestations**, for outside evidence of time. A possible successor (W1, option b); it would never block signing.
6. **Multi-party or threshold attestations.**

## The seam between openWallet and openSig

This section is written in the same words in openWallet's
`add-practitioner-profile-and-licence-verification` and in openSig's
`add-wallet-bound-attestation`. A change to it in one draft is a change to it
in both. Every line of it is PROPOSED.

**The split.** openWallet owns the keys, the proof of possession and the
licence verification. openSig owns a sealed record that a given proof over a
given digest verified, or did not, against a given key binding at a given time.
The relying party (openChart, for a practitioner's sign-off of a medical
decision) issues the single-use challenge, writes the statement, owns
exact-request consent, binds each sign-off key to its account, checks the
proof, the attestation and the licence verification, and alone decides whether
the sign-off counts. Neither openWallet nor openSig decides clinical meaning.
openWallet's draft calls the relying party the relying domain, after its
core's "consuming domain"; the two words name one party.

**The aim, put to the owner.** Nobody in the practice, including whoever runs
its deployment, can produce a sign-off in a practitioner's name that an honest
check accepts without that practitioner's key. Its limits are stated as
plainly: whoever runs the deployment can still delete or hide records, run
altered software that skips the checks, and back-date within what its own
clock allows. An honest check is one run on a copy of the records with the
verifier keys from a release of the relying system. Outside evidence of time
is a successor.

| Question | The answer both drafts give |
|---|---|
| What identifies a wallet | The `wallet_id` of its openWallet wallet record. It is `wallet_ref` in a proof and in an attestation, and `openwallet_id` in openChart's sign-off evidence. It is attestation, never the identifier of a person or a patient. |
| What identifies a key | The pair (`did`, `key_id`) under which the wallet declares it, and its `key_fingerprint`: `sha256:` and the lowercase hex SHA-256 of the raw public key, openWallet's one spelling. It is `key_ref` in a proof, written as the DID URL `<did>#<key_id>`. An attestation records the reference and the fingerprint recomputed from the public key that the binding named. |
| Which keys may make a sign-off | Only a key that the relying party bound to the practitioner's account individually, by that key's own proof of possession in the practitioner's own session, and whose fingerprint a licence verification in force names. The relying party takes its held copy of the wallet record only from the practitioner, and no administrator adds a key to it. Each bound key has its own binding; with a registry that keeps one current binding per subject, the subject is that account's binding of that key. Replacing a key is a new binding and needs a verification that names the new key. |
| What custody a key declares | One model of openWallet's closed custody registry (`openxwallet-custody`, registry version 1), declared for that key in its wallet record: `holder_readable`, `isolated_invocable` or `isolated_per_use_authorized`. In a binding used for a sign-off, the custody reference is `openxwallet-custody:<registry_version>:<model>` and equals the model the held wallet record declares for that key; openSig's `custody_reference_mismatch` compares those two. Only `isolated_per_use_authorized` evidences that the holder acted, and only where each use is authorized on the device that holds the key, never by a code typed into the computer that asks for the signature. A wallet may declare such a key only if its own custody is the same model, because no declared key may outrank its wallet. A declaration is not proof: neither openWallet's proof nor openSig's attestation shows that a key's custody is what the wallet declares. |
| Which custody a sign-off needs | The relying party decides. openChart's draft proposes (its design call CD-48) to accept only `isolated_per_use_authorized` for a practitioner's sign-off. openSig records the model the key declares and never raises it. |
| What the proof is made over | openWallet's ratified possession-only profile `wallet-challenge-proof`: profile constant `openwallet.challenge-proof.v0`; Ed25519 over the domain separator `openWallet.challenge-proof.v0` and a NUL byte, then the RFC 8785 canonical form of every unsigned proof field. Its `challenge_digest` is `sha256:` and the SHA-256 of the relying party's RFC 8785 canonical statement, which carries the relying party's single-use challenge. For openChart that statement is the sign-off statement, and the digest is its `statement_sha256`. The challenge reference is an opaque random value, 32 random bytes in unpadded base64url, that carries no meaning. The proof also binds the wallet, the key, the subject, the custody reference, the binding and its revision. openSig is given the digest and the challenge reference, never the statement. |
| Who owns consent | The relying party. A sign-off is consent to an exact request, and openWallet's ratified profile does "not assert MFA, human presence, explicit human consent, workload composition, delegation or domain permission". The relying party evidences what the signer was shown, refusing a signature over a digest that differs from it; re-authentication at signing; and, through the custody it accepts, per-use authorization at the key. Neither the proof nor the attestation is consent. |
| Who verifies what | openWallet's verification checks a proof against the key's current binding. openSig runs that verification, never a second implementation of it, and seals the result with the proof, the binding it rested on and its own time. At acceptance the relying party verifies the attestation under openSig's attestation key; verifies the sealed proof itself under the sealed public key over its own recomputed statement digest; checks that the sealed fingerprint is a key it bound and that the licence verification names; checks the sealed challenge reference against its statement; and checks the licence verification under the verifier key it holds. |
| Where it is checked | Inside the relying party's own deployment (ruling 156). The key binding registry, the held wallet records, the openSig attester and its attestation keys, the held verifier keys and the verifiers' revocation lists are held there. The practitioner's key stays in the practitioner's custody. The check contacts and waits on nothing outside the deployment: a revocation list older than the relying party's freshness bound flags the reliance and never blocks it. |
| What neither states | The content behind the digest, the practitioner's authority, their consent, whether they were present beyond what the declared custody evidences, and whether the sign-off counts. openWallet's licence verification states the licence; openSig's attestation states nothing about it. |

## Design calls

Every call below goes beyond the owner's words and is PROPOSED, and each is put to the owner.

| # | Design call | Where |
|---|---|---|
| WA-1 | One capability separate from feature 001, with defined terms. | Terms; W1 |
| WA-2 | What the attestation adds (a sealed binding and time, a single-purpose key in a separate process, a second check, replay refusal) and what it does not (independence from the operator, outside evidence of time, a way to skip verifying the proof); options (a), (b) and (c). | W1 |
| WA-3 | openSig runs openWallet's verification, never a second; compares custody references; records the challenge reference as given. | W2 |
| WA-4 | The fields of an attestation, including the sealed proof and binding, the recomputed fingerprint and `basis: declared`. | W3 |
| WA-5 | openSig receives only the challenge reference, in a fixed opaque form, the digest and the proof, and states nothing about content, licence, authority, consent, presence or whether a sign-off counts. | W4 |
| WA-6 | The attester, the binding registry, the held wallet records, the attestation keys and the attestations are held inside the deployment; at acceptance the relying party verifies the sealed proof itself; verifying again never claims currency. | W5 |
| WA-7 | Four results with closed reasons; `not_verified` is signed, `refused` and `unavailable` are not; what `custody_reference_mismatch` and `clock_untrusted` test. | W6 |
| WA-8 | The attestation key is a key of the deployment, not of a wallet, evidencing at best the attester's environment; Ed25519 over RFC 8785 with its own signing domain; retired keys kept. | W7 |
| WA-9 | One attestation per relying party's challenge reference and one per challenge digest. | W8 |
| WA-10 | "Wallet" is an openWallet wallet, never the anchoring wallet; anchoring digests stays beside the attestation path. | W9 |
| WA-11 | Consistent with feature 001 and the project rules, and changes nothing in them. | W10 |
| WA-12 | Stacked on pull request #2. | W11 |
| WA-13 | The code surface, the relying party's credential to the attester, and every name that becomes a machine key. | W12 |
| WA-14 | The non-goals and named successors. | W13 |
| WA-15 | The seam with openWallet, in the words both designs share. | "The seam between openWallet and openSig" |

## Tensions

- **T1. The attester runs under the practice's operator.** Ruling 156 puts it inside the deployment, so it adds no independence from whoever runs it, and no outside evidence of time (W1; Open question 1).
- **T2. openSig's scope widens.** openSig is a document-signing platform, and this capability signs no document. It fits openSig's role as the place signing evidence is made, and it attests no clinical fact, but it is a second kind of thing in the project.
- **T3. openSig cannot see what the challenge reference belongs to.** It records the reference as given and refuses repeats of the reference and of the digest (W2, W8); only the relying party can check it against the statement.
- **T4. One more component in a small practice's deployment.** Ruling 156 requires it to run there (Open question 10).
- **T5. Verifying again does not restore currency.** It shows what was true when the attestation was made, which is what an audit needs, and not that the key is still bound (W5).
- **T6. The pull request is stacked on two unmerged pull requests** (W11).

## Open questions

Each is a judgment the author made, or a choice the owner must make, with a recommendation. None is ruled. The first two are the ones the rest hang on.

1. **What is the attestation worth, given that it runs under the practice's operator?** The options are W1's: (a) as specified; (b) the same, with attestation digests optionally anchored, as a successor, for outside evidence against back-dating, never blocking signing; (c) the same, with the attester under a separate administrator. Recommendation: (a) now, with (b) as a named successor. The attestation is a sealed record of the binding and the time under a single-purpose key in a separate process, which ruling 151's "the openSig will attest" asks for; it is not protection against the operator, and openWallet's draft states that threat model's aim and limits.
2. **Who evidences exact-request consent?** Recommendation: the relying party, as openWallet's ratified profile's record says; openSig's attestation claims none, and says so (W4).
3. **Refuse repeats of both the challenge reference and the digest?** Recommendation: yes; the digest is the one the proof binds (W8).
4. **How is the attestation key held?** Recommendation: a key of the deployment, used for nothing else, that the attester's own application process cannot read; it evidences the attester's environment at best and is never declared in an openWallet wallet (W7); retired public keys kept by the relying party.
5. **How long are attestations, and the attester's record of attested references and digests, kept?** Recommendation: for as long as the records they support, under whatever retention rule counsel sets for those records.
6. **Sign `not_verified` results?** Recommendation: yes, as evidence of failed attempts; never `refused` or `unavailable`, where there is nothing to attest.
7. **The form of the challenge reference.** Recommendation: 32 random bytes in unpadded base64url, refused in any other form (W4).
8. **The relying party's credential to the attester.** Recommendation: mutual TLS inside the deployment, with the client certificate pinned by fingerprint (W12).
9. **Placement.** Recommendation: stay stacked on pull request #2, and retarget to `main` once #1 and #2 merge.
10. **How does a small practice run the attester?** Recommendation: as a small service deployed beside openChart, under the practice's operator; never a hosted openSig. Option (c) of Open question 1 would change who administers it.
11. **How should openChart use the four results?** Recommendation, as openChart's own decision: record which result and reason each failed attempt had; treat `unavailable` and `not_verified` as AN-14 already does; and raise a `not_verified` with reason `signature_invalid` or `digest_mismatch`, and any `digest_already_attested`, to the medical director as a possible forgery or replay.
12. **The names that become machine keys** (W12). Recommendation: confirm or change them now.
13. **Should an attestation carry the relying party's statement type or purpose?** Recommendation: no. openSig receives only the digest; keeping statements of different purposes apart is the relying party's statement's job.

## Status

Proposed. One independent review has run, on the first draft at `aabc190`; this revision answers its findings. `tasks.md` 1.1 still schedules an adversarial reviewer over this revision before ratification. Ratification is pending, by Brett Heap.
