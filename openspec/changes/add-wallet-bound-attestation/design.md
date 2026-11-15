## Context

Drafted on 2026-11-15 by Claude (Opus 5.5), on Brett Heap's ruling 153, as a draft for his review. **Nothing here is ratified.** The owner's rulings quoted below are his decisions; every other choice in this change is a design call, marked PROPOSED where it is made and listed in "Design calls" below, so that it can be confirmed or overturned one by one.

The consumer is openChart's draft `define-clinical-acts-foundation` (openChart-spec#5, read at `e9ba497`). Its practitioner's sign-off checks, among other things, that "the openSig attestation verifies against that wallet, over the SHA-256 of the sign-off statement" (its CA-04). Nothing in openSig provides that today, so every sign-off fails closed and drafts stay drafts (its AN-14; ruling 152). This change specifies the attestation. openWallet's draft `add-practitioner-profile-and-licence-verification` specifies the licence verification and what a sign-off's proof is. The two share one seam, stated in the same words in both designs.

This change says what openSig provides. It does not restate or change openChart's rules; where it checks its design against openChart's needs, it cites them.

**Read for this change**, at these heads:

- `opensoft/openSig-spec`: `main` at `23ebbfe`; pull request #1 (`docs/opensig-foundation`) at `8668f30`; pull request #2 (`001-corporate-signing-mvp`) at `a05fcee`, including `add-corporate-signing-mvp` and the agent instructions both pull requests add;
- `opensoft/openSig` (assembly root): `main` at `d6aca3f` and its pull request #1;
- `opensoft/openWallet-spec` `main` at `a2d554d`: the canonical `openxwallet`, the ratified possession-only profile in `add-challenge-proof-runtime` with feature 016, and `add-reference-registry-custody`; and `opensoft/openWallet-code` at `bb0e9da`, read only, for the closed custody registry and the record schema;
- openChart-spec#5 at `e9ba497`: Decision 4, Decision 19, Open Questions 10 and 11, the requirements AN-14, CA-04, CA-08 and CA-14, and the content schema's sign-off evidence.

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
| proof | A proof of possession made under openWallet's ratified `wallet-challenge-proof` profile. | A PDF signature, an organizational seal, an approval event. |
| wallet-bound attestation | openSig's signed record that a given proof over a given digest did or did not verify against a given wallet key's binding at a given time. | openChart's clinical attestation statement, which is the practice's coded wording of what a signer attests; feature 001's organizational seal; an anchoring receipt. |
| relying party | The authenticated caller that issued the challenge and asks for the attestation: openChart, for a practitioner's sign-off. | A signer or recipient in feature 001. |
| attester | The openSig component that makes and signs wallet-bound attestations, running inside the relying party's deployment. | Feature 001's signer worker. |

## What this change stands on

| Change or record | Standing | How this change uses it |
|---|---|---|
| openWallet `add-challenge-proof-runtime` (capability `wallet-challenge-proof`) | ratified 2026-11-10 for its possession-only wire profile, not yet archived; runtime acceptance pending | The proof openSig verifies, with openWallet's own verification (W2). |
| openWallet's closed custody registry, version 1 | ratified | The custody models an attestation records (W3). |
| openWallet `add-reference-registry-custody` | commissioned implementation, not a ratified contract | One way to run the key binding registry inside a deployment (W5). |
| openWallet draft `add-practitioner-profile-and-licence-verification` | proposed | Says what a sign-off's proof is made over, and states the seam in the same words (W2; "The seam"). |
| openSig pull requests #1 and #2, `add-corporate-signing-mvp` | open, not merged, not ratified | This change uses #2's OpenSpec instance and changes nothing either owns (W10, W11). |

## Decisions

### W1 — One new capability, separate from feature 001 (PROPOSED, WA-1)

`wallet-bound-attestation` is a capability of its own. It adds nothing to `corporate-signing-mvp` and removes nothing from it, and it shares no record with feature 001's envelopes. It defines its terms (above) so that "wallet" cannot be read as the anchoring wallet and "attestation" cannot be read as a clinical attestation.

**Why openSig, and not openWallet's proof alone.** openChart's Open Question 11 offered the owner two readings: an openSig change, or openWallet's own proof as the attestation. Ruling 153 chose an openSig draft. This design gives openSig something openWallet's proof alone does not provide. openWallet's profile verifies a proof against the CURRENT binding, and "an old proof refuses" once a binding is revoked, expired or revised. A chart must still be able to show, years later, what a sign-off rested on. A wallet-bound attestation seals the proof with the binding it was checked against, signed by a party other than the chart, so it can be verified again after the practitioner's key has rotated. It also gives a relying party one small check, a signature by a key it holds, instead of an implementation of the wallet profile of its own. The cost is one more component inside the deployment (Open question 1).

REJECTED:

- **A mode of feature 001's envelope workflow.** An envelope binds a PDF and its approvals; a sign-off attestation binds a digest the relying party computed. Folding one into the other would make openSig's envelope records carry clinical sign-off traffic, which feature 001's non-goals exclude.
- **openSig verifying the relying party's statement itself.** It would need the statement, which carries the patient and the act (W4).

### W2 — What openSig verifies, and with whose verification (PROPOSED, WA-2)

openSig receives a challenge reference, a digest and a proof from an authenticated relying party. It resolves the proof's binding from the key binding registry the deployment holds, and runs openWallet's verification of `wallet-challenge-proof` against it, with the submitted digest as the expected challenge digest. It does not implement a second verification of the profile: one profile, one verification, so the two cannot drift. It then checks that the digest it was given is the digest the proof was made over, and records the result.

The profile already refuses a changed challenge digest, binding, signature or profile; duplicate JSON keys; unknown fields; noncanonical signatures; unsupported algorithms; inline public keys; and a binding that is revoked, expired or revised. openSig maps each refusal to a `not_verified` reason (W6), and adds none of its own about the proof.

**What openSig cannot check, and says so.** It never sees the statement, so it cannot check that the challenge reference it was given is the one inside the statement behind the digest. It records the challenge reference as the relying party gave it. The relying party, which issued the challenge and wrote the statement, checks that the two agree, and stays the challenge's owning issuer: the profile says "The owning issuer enforces freshness, caller binding and one-use."

### W3 — What an attestation states (PROPOSED, WA-3)

The fields, at the level the requirement binds; the record contract is realization work (`tasks.md` §2).

| Field | What it carries |
|---|---|
| `attestation_id` | The record's identity. |
| `profile` | `opensig.wallet-bound-attestation.v1`. |
| `relying_party_ref` | The authenticated caller that asked. |
| `challenge_ref` | The relying party's single-use challenge reference, as it gave it (W2). |
| `challenge_digest` | The digest the proof was made over: `sha256:` and 64 lowercase hex digits. |
| `proof` | openWallet's proof, exactly as received. It holds references and a signature, never key material or content. |
| `binding` | The binding the proof was checked against, as the registry returned it: binding reference and revision, wallet, key, subject and custody references, public key, validity, and when and from which registry it was resolved. |
| `key_fingerprint` | Recomputed by openSig from the binding's public key, in openWallet's spelling: `sha256:` and the lowercase hex SHA-256 of the raw public key. |
| `custody` | The custody model the key declares, from openWallet's closed registry, with the registry version, and `basis: declared`. |
| `wallet_state`, `key_state` | The wallet's and the key's declared state when checked. |
| `result`, `reason` | `verified`, or `not_verified` with a reason from the closed list of W6. |
| `verification` | Which openWallet verification ran: the profile `openwallet.challenge-proof.v0` and the openWallet release the deployment pins. |
| `attested_at` | openSig's time, from the deployment's trusted clock. |
| `attester` | The attester and the attestation key that signed. |
| `signature` | Over the canonical form of every other field (W7). |

The proof's `subject_ref` is the relying party's own reference for the binding's subject; for openChart, an account reference. openSig records it as part of the proof and never resolves it.

### W4 — What an attestation never states, and what openSig never receives (ruling 155) (PROPOSED, WA-4)

Ruling 155: "openChart decides whether a sign-off counts; openSig only attests that the signature came from that wallet." So an attestation states nothing about:

- **the content.** openSig never receives the statement, the act or the patient; only the digest. A request that carries anything besides the challenge reference, the digest and the proof is refused, and nothing it carried is stored. openChart's sign-off statement binds the patient, the act and the content digests (its CD-53); keeping it out of openSig keeps patient data out of openSig, which feature 001 and openSig's project rules already require;
- **the signer's licence or authority.** That is openWallet's licence verification, checked by the relying party, and the relying party's own grants;
- **whether the holder was present.** The attestation records the custody the key declares and never raises it. Only `isolated_per_use_authorized` evidences that the holder acted, and even that is a declaration: openWallet's ratified profile "does not attest that the referenced custody or agent composition is actually isolated";
- **whether the sign-off counts.** openChart decides.

A wallet-bound attestation is therefore not clinical attestation, which openSig's project rules keep in openChart, and openSig never presents it as one.

### W5 — Inside the relying party's deployment (ruling 156) (PROPOSED, WA-5)

Ruling 156 puts the check "Inside the practice's own deployment, so the chart never waits on an outside service to accept a decision." For an attestation to be produced there, and verified again there later, the deployment holds:

1. **an openSig attester**, running inside the deployment under the practice's operator, with its attestation key in custody there;
2. **the key binding registry** that proofs are checked against, or a current replica of it. openWallet's draft has the relying party run or hold it, populated, for openChart, by the account-to-wallet binding of its AU-07;
3. **the wallet records** of the wallets bound there, for the custody and state each key declares;
4. **openSig's attestation public keys**, retired ones included, held by the relying party;
5. **every attestation, with the proof and binding it seals**, kept by the relying party with the record it supports.

Nothing else is contacted: no hosted openSig, no openWallet service outside the deployment, no directory and no ledger. The practitioner's key stays in the practitioner's custody; producing the proof needs it, checking the proof does not.

**Verifying again** needs only items 4 and 5. It checks openSig's signature over the attestation, then openWallet's verification of the sealed proof against the sealed binding's public key over the sealed digest, then that the digest equals the relying party's own recomputed statement digest. It establishes what was true at `attested_at`, and it says so: it never claims the binding is still current.

REJECTED:

- **A hosted openSig service that every practice calls.** The chart would wait on it, which ruling 156 forbids.
- **Verifying again by asking the registry.** The registry answers for the current binding only, and an old proof refuses once the binding moves.

### W6 — Four results, fail closed (PROPOSED, WA-6)

Every request gets exactly one result:

| Result | Attestation | When | Reasons (closed, PROPOSED) |
|---|---|---|---|
| `refused` | none, and no check is made | The request is not one openSig accepts. | `caller_not_authenticated`, `request_shape_invalid`, `unexpected_field`, `challenge_already_attested` |
| `verified` | yes | openWallet's verification passed and the digests agree. | none |
| `not_verified` | yes, recording the failure | The check was made and failed. | `digest_mismatch`, `signature_invalid`, `proof_malformed`, `profile_unsupported`, `binding_not_found`, `binding_not_current`, `key_not_declared`, `key_retired`, `wallet_not_active`, `custody_reference_mismatch` |
| `unavailable` | none | The check could not be completed. | `binding_registry_unreachable`, `wallet_record_unavailable`, `attester_key_unavailable`, `clock_untrusted` |

openSig never turns `unavailable` into another result, and treats any doubt it cannot classify as `not_verified`.

**Why "could not reach" and "did not verify" stay distinct.** openChart's AN-14 treats both as failures the signer can cure by trying again, and its closed list has a code for each (`signing_service_unavailable`, `attestation_not_verified`). They mean different things for an operator: an outage is retried; a proof that does not verify over its digest may be a forgery or a replay. A signed `not_verified` attestation is evidence of that attempt, for openChart's trail of failed sign-off attempts (its CA-08). What openChart does with each is openChart's to decide (Open question 9).

### W7 — openSig's attestation key and signature (PROPOSED, WA-7)

**A dedicated key per deployment,** used for wallet-bound attestations and nothing else: never feature 001's organizational seal, never an anchoring key, never a holder's key. openSig's foundation documents already keep signing keys separate by purpose.

**The signature** is Ed25519 over the domain separator `openSig.wallet-bound-attestation.v1` and a NUL byte, then the RFC 8785 canonical UTF-8 bytes of every field but `signature`. That is the construction of openWallet's ratified profile with a separator of its own, so an attestation can never verify as a wallet proof, nor a wallet proof as an attestation.

**Its custody.** The attester signs automatically, with no person authorizing each signature, so its key evidences that the attester acted, never a person. That is all an attestation claims. The key is held so that the attester's own application process cannot read it (Open question 3).

**Rotation keeps history.** A retired attestation key's public half stays with the relying party, so an attestation it signed still verifies.

### W8 — One attestation per challenge (PROPOSED, WA-8)

openSig keeps, for each relying party, the challenge references it has attested, and refuses a repeat with `challenge_already_attested`. The relying party stays the challenge's owning issuer and enforces one use itself (openChart's CA-04 and CA-08 do); openSig's refusal is a second line, local to the deployment.

### W9 — "Wallet" is never the anchoring wallet (PROPOSED, WA-9)

openSig's foundation documents give optional anchoring "dedicated wallet custody" for fees, and feature 001 excludes "live blockchain submission, wallets, fees and network selection". That wallet is a ledger account. This capability's wallet is an openWallet wallet. The requirement "A wallet is an openWallet wallet, never an anchoring wallet" makes the separation testable: an anchoring wallet's key has no openWallet binding, so a proof from it never verifies, and anchoring never sits in the attestation path.

### W10 — Fit with feature 001 and openSig's project rules (PROPOSED, WA-10)

This change touches nothing in `add-corporate-signing-mvp` or feature 001. It is consistent with what they say:

| Feature 001 or project rule | This change |
|---|---|
| Non-goal: "personal PKI enrollment" | Enrolls no key. Keys are openWallet's; bindings are the relying party's registry. |
| Non-goal: "patient/clinical production workflows" | Receives no patient data or clinical content, and attests no clinical fact. Production use would need its own feature and release gates. |
| "Keep clinical attestation and amendments in openChart" | Kept: ruling 155, and W4. |
| "human personal approval outside Hermes service credentials" | openSig approves nothing. A holder's per-use authorization happens in the holder's custody. |
| "Never commit real patient data, signature images, credentials, private keys or wallet secrets. Use synthetic fixtures." | This change commits no key of any kind; its realization uses synthetic fixtures only. |
| "Optional anchoring publishes commitments only and does not block ordinary signing" | Attestation works with anchoring disabled, and no anchoring adapter touches it (W9). |
| "This is not multiple personal certificate signatures." | Adds no personal certificate signature to a PDF; an attestation is not a PDF signature. |
| A consumer "cannot independently attest a clinical record" | Unchanged; nothing here attests a clinical record. |
| The MIT/Apache allowlist | Applies to the realization. Its one new dependency is openWallet's verification, which is Apache-2.0 and pinned through openWallet's assembly root. |

### W11 — Placement (PROPOSED, WA-11)

openSig-spec's `main` has no OpenSpec instance; pull request #2 adds it (`openspec/config.yaml`), stacked on pull request #1. The assembly root's pull request #1 says that while the foundation pull requests are unmerged, "the intended base for first-feature creation is docs/opensig-foundation". This change is an OpenSpec change, not a feature, and it needs an OpenSpec instance to validate. So it branches from `001-corporate-signing-mvp` at `a05fcee`, adds only its own directory, and its pull request targets that branch. It changes nothing either pull request owns. It can land only after #2, and if #2's branch is rewritten this branch is rebased onto it (Open question 5).

REJECTED: branching from `main` and adding `openspec/config.yaml`, which would create a file pull request #2 owns.

### W12 — Code surface and names (PROPOSED, WA-12)

Named here and performed by nothing in this change:

- openSig code leg: an attester and a verifier for wallet-bound attestations, deployable inside a relying party's deployment, depending on openWallet's verification of `wallet-challenge-proof` at a pinned openWallet assembly root; independently generated known-answer vectors, on openWallet feature 016's precedent;
- an attestation record contract, placed where openSig's governance places contracts;
- no change to feature 001's HTTP contract.

Names that would become machine keys once released, all PROPOSED and cheapest to change now: the capability `wallet-bound-attestation`; the profile `opensig.wallet-bound-attestation.v1`; the signing domain `openSig.wallet-bound-attestation.v1`; the four results; and every reason code in W6.

### W13 — Non-goals and named successors

1. **A sign-off made in another system.** Ruling 186: openChart only, in version 1; it counts later, once openWallet and openSig can prove it to the chart. That needs a challenge the chart can trust from another system, and its own change.
2. **Anything about the licence.** openWallet's licence verification, checked by the relying party.
3. **Evidence that a key's custody is what its wallet declares.** A successor in openWallet; openSig records the declaration.
4. **Attesting other proof profiles**, such as a WebAuthn assertion profile, until openWallet ratifies one.
5. **Anchoring attestations.** Not needed for ruling 156, and anchoring is optional by openSig's own rule.
6. **Multi-party or threshold attestations.**

## The seam between openWallet and openSig

This section is written in the same words in openWallet's
`add-practitioner-profile-and-licence-verification` and in openSig's
`add-wallet-bound-attestation`. A change to it in one draft is a change to it
in both. Every line of it is PROPOSED.

**The split.** openWallet owns the keys, the proof of possession and the
licence verification. openSig owns the durable attestation that a given
signature over a given digest came from a given wallet. The relying party
(openChart, for a practitioner's sign-off of a medical decision) issues the
single-use challenge, writes the statement, checks the attestation, checks the
licence verification, checks its own account's binding to the wallet, and alone
decides whether the sign-off counts. Neither openWallet nor openSig decides
clinical meaning. openWallet's draft calls the relying party the relying domain,
after its core's "consuming domain"; the two words name one party.

| Question | The answer both drafts give |
|---|---|
| What identifies a wallet | The `wallet_id` of its openWallet wallet record. It is `wallet_ref` in a proof and in an attestation, and `openwallet_id` in openChart's sign-off evidence. It is attestation, never the identifier of a person or a patient. |
| What identifies a key | The pair (`did`, `key_id`) under which the wallet declares it, and its `key_fingerprint`: `sha256:` and the lowercase hex SHA-256 of the raw public key, openWallet's one spelling. It is `key_ref` in a proof, written as the DID URL `<did>#<key_id>`. An attestation records the reference and the fingerprint recomputed from the public key that the binding named. |
| What custody a key declares | One model of openWallet's closed custody registry (`openxwallet-custody`, registry version 1), declared for that key in its wallet record: `holder_readable`, `isolated_invocable` or `isolated_per_use_authorized`. openWallet states what each evidences, and only `isolated_per_use_authorized` evidences that the holder acted. A declaration is not proof: neither openWallet's proof nor openSig's attestation shows that the key's custody is what the wallet declares. |
| Which custody a sign-off needs | The relying party decides. openChart accepts only `isolated_per_use_authorized` for a practitioner's sign-off. openSig records the model the key declares and never raises it. |
| What the proof is made over | openWallet's ratified possession-only profile, `wallet-challenge-proof` (`openwallet.challenge-proof.v0`): Ed25519 over its domain separator and the RFC 8785 canonical form of every unsigned proof field. Its `challenge_digest` is `sha256:` and the SHA-256 of the relying party's RFC 8785 canonical statement, which carries the relying party's single-use challenge. For openChart that statement is the sign-off statement, and the digest is its `statement_sha256`. The proof also binds the wallet, the key, the subject, the custody reference, the binding and its revision. openSig is given the digest, never the statement. |
| Who verifies what | openWallet's verification checks the proof against the key's current binding. openSig runs that verification, never a second implementation of it, and seals its result together with the proof and the binding it rested on. The relying party verifies openSig's attestation against openSig's attestation key, checks the licence verification against its verifier's key, and checks its own account's binding to the wallet. |
| Where it is checked | Inside the relying party's own deployment (ruling 156). The key binding registry, the openSig attester, openSig's attestation keys and the licence verifiers' keys are held there. The practitioner's key stays in the practitioner's custody, and the check contacts nothing outside the deployment. |
| What neither states | The content behind the digest, the practitioner's authority, whether the holder was present beyond what the declared custody evidences, and whether the sign-off counts. openWallet's licence verification states the licence; openSig's attestation states nothing about it. |

## Design calls

Every call below goes beyond the owner's words and is PROPOSED.

| # | Design call | Where |
|---|---|---|
| WA-1 | One capability separate from feature 001, with defined terms; openSig's value is a sealed, re-verifiable record beyond the current binding. | Terms; W1 |
| WA-2 | openSig runs openWallet's verification, never a second; records the challenge reference as given. | W2 |
| WA-3 | The fields of an attestation, including the sealed proof and binding, the recomputed fingerprint and `basis: declared`. | W3 |
| WA-4 | openSig receives only the challenge reference, the digest and the proof, and states nothing about content, licence, authority, presence or whether a sign-off counts. | W4 |
| WA-5 | The attester, the binding registry, the wallet records, the attestation keys and the attestations are all held inside the deployment; verifying again needs only the keys and the attestations, and never claims currency. | W5 |
| WA-6 | Four results with closed reasons; `not_verified` is signed, `refused` and `unavailable` are not. | W6 |
| WA-7 | A dedicated attestation key per deployment, Ed25519 over RFC 8785 with its own signing domain; retired keys kept. | W7 |
| WA-8 | One attestation per relying party's challenge reference. | W8 |
| WA-9 | "Wallet" is an openWallet wallet, never the anchoring wallet. | W9 |
| WA-10 | Consistent with feature 001 and the project rules, and changes nothing in them. | W10 |
| WA-11 | Stacked on pull request #2. | W11 |
| WA-12 | The code surface and every name that becomes a machine key. | W12 |
| WA-13 | The seam with openWallet, in the words both designs share. | "The seam between openWallet and openSig" |

## Tensions

- **T1. openSig's scope widens.** openSig is a document-signing platform, and this capability signs no document. It fits openSig's role as the place signing evidence is made, and it attests no clinical fact, but it is a second kind of thing in the project.
- **T2. openSig cannot see what the challenge reference belongs to.** It records the reference as given (W2); only the relying party can check it against the statement.
- **T3. One more component in a small practice's deployment.** Ruling 156 requires it to run there (Open question 6).
- **T4. Verifying again does not restore currency.** It shows what was true when the attestation was made, which is what an audit needs, and not that the key is still bound (W5).
- **T5. The pull request is stacked on two unmerged pull requests** (W11).

## Open questions

Each is a judgment the author made, or a choice the owner must make, with a recommendation. None is ruled.

1. **Is a separate attestation worth its component, when openChart could keep the proof and a binding snapshot itself?** Recommendation: keep openSig's attestation. A snapshot kept by the chart is the chart vouching for its own evidence; an attestation by a separate component, signed with a key the chart holds but does not control, can be verified again after the practitioner's key rotates, and other relying parties can reuse it.
2. **Sign `not_verified` results?** Recommendation: yes, as evidence of failed attempts; never `refused` or `unavailable`, where there is nothing to attest.
3. **How is the attestation key held and declared?** Recommendation: a dedicated key that the attester's own process cannot read, declared in an openWallet wallet of the practice's operator, of class `organisation`, so that its custody and retirement follow openWallet's rules; retired public keys kept by the relying party.
4. **Keep replay state in openSig?** Recommendation: yes, keyed by relying party and challenge reference, kept for as long as the relying party keeps its records.
5. **Placement.** Recommendation: stay stacked on pull request #2, and retarget to `main` once #1 and #2 merge.
6. **How does a small practice run the attester?** Recommendation: as a small service or library deployed beside openChart, under the practice's operator; never a hosted openSig.
7. **The names that become machine keys** (W12). Recommendation: confirm or change them now.
8. **Should an attestation carry the relying party's statement type or purpose?** Recommendation: no. openSig receives only the digest; keeping statements of different purposes apart is the relying party's statement's job. openChart's sign-off statement would do it by naming its type and its deployment.
9. **How should openChart use the four results?** Recommendation, as openChart's own decision: record which result and reason each failed attempt had; treat `unavailable` and `not_verified` as AN-14 already does; and raise a `not_verified` with reason `signature_invalid` or `digest_mismatch` to the medical director as a possible forgery or replay.

## Status

Proposed. No review pass has run yet; `tasks.md` 1.1 schedules one adversarial reviewer before ratification. Ratification is pending, by Brett Heap.
