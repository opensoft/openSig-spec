## Context

Drafted on 2026-11-15 by Claude (Opus 5.5), on Brett Heap's ruling 153, as a draft for his review, and revised the same day after an independent review, a confirmation review, a final check with an automated review, and his rulings 208, 209 and 214. **Nothing here is ratified.** The owner's rulings quoted below are his decisions; every other choice in this change is a design call, marked PROPOSED where it is made and listed in "Design calls" below, so that it can be confirmed or overturned one by one.

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

**Rulings 208, 209 and 214.** On 2026-11-15 he answered the decisions numbered 195 to 214 with the words "accept your recs for 195-214". Each became a ruling in the words of the recommendation as last presented to him. The rows that bind this change, question and ruling verbatim:

| No. | Question | Ruling |
|---|---|---|
| 208 | How does a practitioner sign: with a smart-card style key, or with a passkey or security key? The second needs one more openWallet change, a proof format for them. | A passkey or security key. Draft that change now. A smart card cannot be used from a phone. |
| 209 | May it be a passkey that syncs through the practitioner's Apple or Google account? | Yes, but only on a device the practitioner has enrolled, as ruling 134 allows for approvals. A security key always counts. Controlled substances stay out (ruling 83) and would need stronger keys. |
| 214 | Should a sign-off hold up even against whoever runs the practice's server? | Yes, against forgery: the verification names the practitioner's own signing keys and is signed by the independent verifier. It does not stop that person deleting records or back-dating; outside timestamps can close that later. |

What they rule here, and nothing more: a practitioner signs with a passkey or a security key, and openWallet's change giving them a proof format is drafted now (208; openWallet's `add-webauthn-proof-profile`), so openSig attests proofs in that profile as well as in the ratified one (W2, W3, W6, W8); which keys count for openChart is openChart's (209); and the threat model's aim is ruled in its own words (214), stated in the seam. Everything beyond their words is PROPOSED.

## Terms

This change uses these words in these senses, and no other (PROPOSED, WA-1).

| Term | Meaning here | Not to be confused with |
|---|---|---|
| wallet | An openWallet wallet: a signing key, or a declared set of signing keys, held by a holder, as openWallet's `openxwallet` capability defines it. | The ledger wallet that pays for optional anchoring in openSig's foundation documents. That wallet is never attested, never an attestation key and never consulted here. |
| key binding, registry | The trusted record of a wallet key's current binding (wallet, key, public key, custody reference, revision, validity) that openWallet's possession-only profile verifies a proof against, and the registry that holds it. | openSig's envelope records. |
| held wallet record | The relying party's copy of a practitioner's wallet record, taken from the practitioner, from which the custody each key declares is read. | The wallet itself, which stays with its holder. |
| proof | A proof of possession made under one of openWallet's possession profiles: the ratified `wallet-challenge-proof`, or the passkey and security-key profile `wallet-webauthn-proof` (openWallet's draft `add-webauthn-proof-profile`). | A PDF signature, an organizational seal, an approval event. |
| wallet-bound attestation | openSig's signed record that a given proof over a given digest did or did not verify against a given wallet key's binding at a given time. | openChart's clinical attestation statement, which is the practice's coded wording of what a signer attests; feature 001's organizational seal; an anchoring receipt. |
| relying party | The authenticated caller that issued the challenge and asks for the attestation: openChart, for a practitioner's sign-off. | A signer or recipient in feature 001. |
| attester | The openSig component that makes and signs wallet-bound attestations, running inside the relying party's deployment. | Feature 001's signer worker. |

## What this change stands on

| Change or record | Standing | How this change uses it |
|---|---|---|
| openWallet `add-challenge-proof-runtime` (capability `wallet-challenge-proof`) | ratified 2026-11-10 for its possession-only wire profile, not yet archived; runtime acceptance pending | One of the two proofs openSig verifies, with openWallet's own verification (W2), within the limits its ratification states (W4). |
| openWallet `add-webauthn-proof-profile` (capability `wallet-webauthn-proof`) | proposed, drafted under ruling 208 | The proof a practitioner signs with, with a passkey or a security key; openSig verifies it with openWallet's own verification of that profile (W2). |
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
- **a second check, and replay refusal.** The attester runs openWallet's verification itself, and refuses a request that repeats a challenge reference or a digest it has attested and differs in anything else; an identical retry gets the stored attestation (W8).

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

openSig receives a challenge reference, a digest and a proof from an authenticated relying party. It resolves the proof's binding from the key binding registry the deployment holds, and runs openWallet's verification of the profile the proof names, `wallet-challenge-proof` or `wallet-webauthn-proof`, against it, with the submitted digest as the expected challenge digest. It does not implement a second verification of either profile: one profile, one verification, so the two cannot drift. Under `wallet-webauthn-proof`, openWallet's verification also checks that the binding is current, unrevoked and time valid, the client data's parsing, type, challenge, origin, `crossOrigin` and `topOrigin`, the relying-party identifier hash, user presence and verification on every use, the attested-data flag and the backup flags; it returns the four flags and a flag where the signature counter did not increase, as the registry recorded it for the proof's derived challenge, and, with a refusal, `signature_verified`, which says whether the signature verified under the binding's public key, checked whenever a binding was found and the proof decoded, and false where it was not checked. openSig never treats a refusal that reports a verified signature as evidence of possession; it uses the report only to decide what it seals. openSig maps each refusal to a reason of W6 and records the flags under the names openWallet gives them. It then checks that the digest it was given is the digest the proof was made over, and compares the binding's custody reference with the custody the held wallet record declares for that key (W6). It records the result.

The profile already refuses a changed challenge digest, binding, signature or profile; duplicate JSON keys; unknown fields; noncanonical signatures; unsupported algorithms; inline public keys; a proof whose references differ from its binding's; and a binding that is revoked, expired or revised. openSig maps each refusal to a `not_verified` reason (W6), and adds none of its own about the proof's cryptography.

**What openSig cannot check, and says so.** It never sees the statement, so it cannot check that the challenge reference it was given is the one inside the statement behind the digest. It records the challenge reference as the relying party gave it, and refuses a different request that repeats either the reference or the digest (W8). The relying party, which issued the challenge and wrote the statement, checks that the sealed challenge reference is its statement's, and stays the challenge's owning issuer: the profile says "The owning issuer enforces freshness, caller binding and one-use."

### W3 — What an attestation states, and the shape of a negative one (PROPOSED, WA-4)

The fields, at the level the requirement binds; the record contract is realization work (`tasks.md` §2). They are of three kinds, kept apart so that nothing the relying party submitted can be read as something openSig resolved: openSig's own; what it was given, as given; and what it resolved for itself.

| Field | Kind | What it carries |
|---|---|---|
| `attestation_id` | own | The record's identity, a random value. |
| `profile` | own | `opensig.wallet-bound-attestation.v1`. |
| `relying_party_ref` | own | The authenticated caller that asked (W12). |
| `challenge_ref` | given | The relying party's single-use challenge reference, as it gave it: 32 random bytes in unpadded base64url, which carries no meaning (W4). |
| `challenge_digest` | given | The digest the relying party submitted: `sha256:` and 64 lowercase hex digits. |
| `proof` | given | openWallet's proof, exactly as received, sealed only where it passes the profile's strict decoding (at most 8192 bytes, a JSON object with no repeated key and no constant, exactly the fields of the profile it names, a supported profile and algorithm, and a signature in its strict encoding; for a passkey proof, its authenticator data, client data and signature as the authenticator and client produced them), openSig found its binding, every reference in it (`binding_ref`, `wallet_ref`, `key_ref`, `subject_ref`, `custody_ref`) equals that binding's, and, for a passkey proof, its `credential_id` and `binding_revision` also equal the binding's and openWallet's verification reported `signature_verified`. A passkey proof's `credential_id` enters only its derived challenge, so a caller could otherwise rewrite it after signing, with up to 1023 bytes of its own text, and the signature would still verify. Its references then repeat what openSig resolved, and it holds references and signed bytes, never key material or content. Always present in a `verified` attestation. |
| `proof_sha256` | given | In place of `proof` in every other case: the SHA-256 of the bytes received, and nothing else of them. Each of the five references is a caller-chosen string of up to 256 characters, so a proof sealed whole when its references matched nothing could carry over a kilobyte of caller text, and a passkey proof's authenticator data and client data are bytes a caller chooses until its signature binds them; this keeps both out of openSig's records. Present only where the result is `not_verified`. |
| `references_differing` | own | Where openSig found a binding and some reference of the proof differs from the binding's, the names of those references, from `binding_ref`, `wallet_ref`, `key_ref`, `subject_ref`, `custody_ref`, and for a passkey proof `credential_id` and `binding_revision`, never their values. Present only where the result is `not_verified`. |
| `binding` | resolved | The binding the proof was checked against, as the registry returned it: binding reference and revision, wallet, key, subject and custody references, public key, validity, and when and from which registry it was resolved; for a passkey binding, also its relying-party identifier, allowed origins, credential identifier, algorithm and the backup eligibility recorded at registration. |
| `key_fingerprint` | resolved | Recomputed by openSig from the binding's public key, in openWallet's spelling: `sha256:` and the lowercase hex SHA-256 of the raw public key, which for a P-256 key is its 33-byte compressed point, as openWallet's passkey profile fixes it. |
| `authenticator_flags` | given | For a passkey proof sealed whole, an object of four booleans, `user_present`, `user_verified`, `backup_eligible` and `backup_state`, as the authenticator signed them in its authenticator data and openWallet's verification returned them. Absent otherwise. |
| `sign_count_not_increasing` | resolved | For a passkey proof sealed whole, a boolean: true where the registry, comparing the counter once for the proof's derived challenge, found that it did not increase, as recorded beside the binding. Where openSig holds only a read-only replica it reports the flag recorded for that challenge, having the registry make the first comparison where none is recorded yet; a later verification reports the recorded flag. It is a signal for the relying party, never a refusal. Absent otherwise. |
| `custody` | resolved | The custody model the key declares in the held wallet record, from openWallet's closed registry, with the registry version, and `custody_basis`: `attested` where the passkey binding records that the relying party's attestation policy accepted attestation evidence for the key, and `declared` in every other case. `attested` is the relying party's own claim until a check verifies the attestation object the binding keeps against an attestation root it trusts; openSig records it as the binding states it, and does not check it. |
| `wallet_state`, `key_state` | resolved | The wallet's and the key's declared state when checked. |
| `unresolved` | own | Each resolved field openSig did not obtain, with why: `not_found` (the registry had no binding for the reference the proof names), `not_declared` (the held wallet record does not declare the binding's key), or `not_attempted` (nothing could be looked up, because the proof did not decode or no binding was found). Always present: an empty list for `verified`, and wherever openSig obtained every resolved field. |
| `result`, `reason` | own | `verified`, with no `reason`; or `not_verified` with a `reason` from the closed list of W6. `reason` is present only where the result is `not_verified`. |
| `verification` | own | Which openWallet verification ran: the profile constant, `openwallet.challenge-proof.v0` or `openwallet.webauthn-proof.v0`, and the openWallet release the deployment pins. |
| `attested_at` | own | openSig's time, from the deployment's clock. |
| `attester` | own | The attester and the attestation key that signed. |
| `signature` | own | Over the canonical form of every other field (W7). |

The proof's `subject_ref` is the relying party's own reference for the binding's subject; for openChart, its reference for that account's binding of that key. openSig records it as part of the proof and never resolves it.

**Present only on a negative attestation.** `proof_sha256`, `references_differing` and `reason` appear only where the result is `not_verified`; a `verified` attestation carries `proof` and none of the three, and has no other place for them. `unresolved` is always present.

**A passkey proof is sealed whole only once its signature has verified.** Its authenticator data and client data are bytes a caller chooses until the signature binds them. So openSig seals a passkey proof whole only where, besides every reference, its `credential_id` and its `binding_revision` matching the binding it found, openWallet's verification reported `signature_verified`, which that verification reports with any refusal; otherwise it seals `proof_sha256`. Where it found a binding, openSig runs that verification even when an earlier check has failed, to learn this, and still records the first failure as the reason. In the table below, every row whose Given column is `proof` gives `proof_sha256` instead for a passkey proof whose signature did not verify, and `signature_invalid` for a passkey proof always does.

**No resolved value is ever filled from the proof.** openSig looks the binding up by the reference the proof names, which is a lookup and not a resolution of the proof's claims; it looks the held wallet record up by the binding's own wallet and key, never by the proof's; and it records what it found, or names under `unresolved` what it did not.

**The order of the checks, and what each negative attestation can carry.** openSig decodes the proof; finds the binding; reads the held wallet record for the binding's key; then runs openWallet's verification, which checks the binding's currency before the digest, the references and the signature; and compares the custody references. It resolves everything it can before the verification runs, and where more than one check fails it records the first, in that order, carrying every value it resolved. Checked reason by reason:

| Result and reason | Given | Resolved | `unresolved` |
|---|---|---|---|
| `verified` | `proof` | all | none |
| `not_verified`: `proof_malformed`, `profile_unsupported` | `proof_sha256` only | none | every resolved field, `not_attempted` |
| `not_verified`: `binding_not_found` | `proof_sha256` only | none | `binding` and `key_fingerprint`, `not_found`; `custody` and both states, `not_attempted` |
| `not_verified`: `key_not_declared` | `proof` | `binding`, `key_fingerprint`, `wallet_state` | `custody` and `key_state`, `not_declared` |
| `not_verified`: `binding_not_current` because the binding names another key or wallet, or, for a passkey proof, another credential or revision | `proof_sha256` and `references_differing` | all; `custody` and `key_state` are `not_declared` where the held record does not declare the binding's key | none, or as stated |
| `not_verified`: `binding_not_current` because the binding is revoked, expired or, for a proof under the ratified profile, revised, `key_retired`, `wallet_not_active`, `custody_reference_mismatch`, `signature_invalid`, `digest_mismatch`, and for a passkey proof `client_data_invalid`, `cross_origin_frame`, `top_origin_present`, `relying_party_mismatch`, `user_not_verified`, `attested_data_present`, `backup_eligibility_changed`, `backup_state_invalid`, `algorithm_mismatch` | `proof`, every reference having matched the binding's (for a passkey proof, its credential identifier and revision too, and only where its signature verified) | all | none |

The five `refused` reasons (`caller_not_authenticated`, `request_shape_invalid`, `unexpected_field`, `challenge_already_attested`, `digest_already_attested`) and the four `unavailable` reasons produce no attestation, so none needs a shape, and none stores what was sent. A repeat refused as `challenge_already_attested` or `digest_already_attested` leaves the earlier attestation as it was. What the relying party may check of each result is W5's.

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

**At acceptance and when verifying again,** the relying party needs items 4 and 5 and two records of its own: its bindings of keys to the signer's account, and the licence verification it relies on, with the held verifier key and revocation list it checks it against (openWallet's draft). It needs nothing from the attester beyond the attestation.

**Verifying, then validating.** Verifying an attestation is checking openSig's signature over it, under the attestation public key that signed it (item 4). That alone establishes what openSig recorded, for that digest and challenge reference, at that time, whatever the result. Validating the recorded result is a second step, and what it checks depends on the result:

- **`verified`.** The sealed proof must verify with openWallet's verification, under the sealed binding's public key, over the sealed digest. A `verified` attestation whose sealed proof does not verify is invalid, though its signature verifies: it points to a faulty or forged attester.
- **`not_verified`.** The sealed proof is evidence of what was submitted, and nothing requires it to verify: for `signature_invalid` or `digest_mismatch` it cannot, and for `binding_not_found` or a proof that did not decode there is no binding or public key to verify it under (W3). The attestation's signature, its reason and its `unresolved` list are what the relying party reads.

**At acceptance** the relying party accepts only a `verified` result. It verifies openSig's signature; verifies the sealed proof itself, over the digest it recomputes from its own statement, which must equal the sealed digest; checks that the sealed fingerprint is a key it bound and that a licence verification names; and checks that the sealed challenge reference is its statement's. A `not_verified` attestation, kept as evidence of a failed attempt (W6), is verified by its signature alone. Verifying again later establishes what was true at `attested_at`, and says so: it never claims the binding is still current.

REJECTED:

- **A hosted openSig service that every practice calls.** The chart would wait on it, which ruling 156 forbids.
- **Verifying again by asking the registry.** The registry answers for the current binding only, and an old proof refuses once the binding moves.

### W6 — Four results, fail closed (PROPOSED, WA-7)

Every request gets exactly one result:

| Result | Attestation | When | Reasons (closed, PROPOSED) |
|---|---|---|---|
| `refused` | none, and no check is made | The request is not one openSig accepts. | `caller_not_authenticated`, `request_shape_invalid`, `unexpected_field`, `challenge_already_attested`, `digest_already_attested` |
| `verified` | yes | openWallet's verification passed, the digests agree and the custody references agree. | none |
| `not_verified` | yes, recording the failure | The check was made and failed. | `digest_mismatch`, `signature_invalid`, `proof_malformed`, `profile_unsupported`, `binding_not_found`, `binding_not_current`, `key_not_declared`, `key_retired`, `wallet_not_active`, `custody_reference_mismatch`; for a passkey proof also `client_data_invalid` (the client data does not parse, has a repeated member, or has the wrong type, challenge or origin), `cross_origin_frame` (`crossOrigin` is set), `top_origin_present` (`topOrigin` is present), `relying_party_mismatch`, `user_not_verified` (presence or verification missing), `attested_data_present` (the attested credential data flag is set), `backup_eligibility_changed`, `backup_state_invalid` (the backup state flag is set while backup eligibility is clear) and `algorithm_mismatch` (the proof's algorithm differs from the one its binding recorded) |
| `unavailable` | none | The check could not be completed. | `binding_registry_unreachable`, `wallet_record_unavailable`, `attester_key_unavailable`, `clock_untrusted` |

**Every refusal has one reason, and the list is closed.** For a passkey proof, authenticator data that does not parse, and an algorithm the profile does not admit, are `proof_malformed`; a credential identifier other than the binding's is `binding_not_current`; a binding that is revoked, expired or revised is `binding_not_current`. No existing reason is renamed, because openChart maps each by name. A refusal openWallet's verification makes that none of these names would be a defect of this list, to be fixed here, never mapped to a nearby reason.

**`custody_reference_mismatch`** (closes the gap between an opaque reference and the closed registry). The ratified profile treats `custody_ref` as opaque, and a proof whose custody reference differs from its binding's is already refused by the profile. openWallet's draft defines, for a binding used for a sign-off, the form `openxwallet-custody:<registry_version>:<model>`. `custody_reference_mismatch` means the binding's custody reference is not in that form, or names a model or registry version other than the one the held wallet record declares for that key.

**`clock_untrusted`** means the attester's clock reports itself unsynchronized, or reads earlier than the newest `attested_at` the attester has issued. Both are facts the attester can test locally.

openSig never turns `unavailable` into another result, and treats any doubt it cannot classify as `not_verified`.

**Why "could not complete" and "did not verify" stay distinct.** openChart's AN-14 treats both as failures the signer can cure by trying again, and its closed list has a code for each (`signing_service_unavailable`, `attestation_not_verified`). They mean different things for an operator: an outage is retried; a proof that does not verify over its digest may be a forgery or a replay. A signed `not_verified` attestation is evidence of that attempt, for openChart's trail of failed sign-off attempts (its CA-08); it carries what W3 says that reason can carry, and is verified by its signature alone (W5). What openChart does with each is openChart's to decide (Open question 11).

### W7 — openSig's attestation key and signature (PROPOSED, WA-8)

**A key of the deployment,** one per deployment, used for wallet-bound attestations and nothing else: never feature 001's organizational seal, never an anchoring key, never a holder's key. It is not declared in an openWallet wallet: ruling 154's exception covers a practitioner's sign-off and the binding it needs, not the deployment's own keys. openSig's foundation documents already keep signing keys separate by purpose.

**The signature** is Ed25519 over the domain separator `openSig.wallet-bound-attestation.v1` and a NUL byte, then the RFC 8785 canonical UTF-8 bytes of every field but `signature`. That is the construction of openWallet's ratified profile with a separator of its own, so an attestation can never verify as a wallet proof, nor a wallet proof as an attestation.

**What the key evidences.** The attester signs automatically, with no person authorizing each signature. In openWallet's custody terms such a key evidences at best that the attester's environment acted (`isolated_invocable`, where the attester's own application process cannot read it), never a person. That is all an attestation claims.

**Rotation keeps history.** A retired attestation key's public half stays with the relying party, so an attestation it signed still verifies.

### W8 — One attestation per challenge reference, and one per digest (PROPOSED, WA-9)

For each relying party, openSig keeps the challenge references and the challenge digests it has attested, with the attestation each produced. A request identical to one already attested, from the same relying party, gets the stored attestation back, unchanged, and no second one is made: if an answer is lost on the way, the retry does not make the practitioner sign again. openWallet's ratified profile design already says the same of its own receipts: "Receipt retries retrieve unchanged historical evidence."

**What "identical" compares.** The challenge reference and the digest are compared as given; each has one fixed form. The proof is compared in the RFC 8785 canonical form of the decoded proof. That is the form openWallet's ratified profile signs ("RFC 8785 canonical UTF-8 bytes of every unsigned proof field"), and the profile admits one encoding of the signature ("strict unpadded base64url of exactly 64 bytes"), so two serializations of one proof, with members in another order or other whitespace, compare equal. Under openWallet's passkey profile the same canonical form is compared, but an ES256 signature can be rewritten into another valid one, so a rewritten proof is a different request: it is refused as a repeat of the reference and the digest, never attested twice, and the practitioner's retry of the unchanged proof still gets the stored attestation. The ratified text defines no canonical form for the proof as a whole; this is the one that follows from it, and openWallet's reference implementation encodes a proof that way (`encode_proof` in `openwallet_runtime/proof.py`, openWallet-code at `bb0e9da`). A proof that does not pass the profile's strict decoding has no canonical form; it is recorded and compared by the SHA-256 of the bytes received (W3). A request that repeats the reference or the digest and differs in anything else is refused: `challenge_already_attested` or `digest_already_attested`. A `refused` or `unavailable` answer attests nothing, so a request after an outage is checked afresh. The digest is the key that matters, because the proof binds it and openSig cannot check the reference against the statement: without it, the same proof and digest submitted with a fresh reference would get a second `verified` attestation. The relying party stays the challenge's owning issuer and enforces one use itself (openChart's CA-04 and CA-08 do); openSig's refusal is a second line, local to the deployment.

### W9 — "Wallet" is never the anchoring wallet (PROPOSED, WA-10)

openSig's foundation documents give optional anchoring "dedicated wallet custody" for fees, and feature 001 excludes "live blockchain submission, wallets, fees and network selection". That wallet is a ledger account. This capability's wallet is an openWallet wallet. The requirement "A wallet is an openWallet wallet, never an anchoring wallet" makes the separation testable: an anchoring wallet's key has no openWallet binding, so a proof from it never verifies, and producing, verifying or relying on an attestation never depends on anchoring. Anchoring the digests of attestations, for outside evidence of time, is a possible successor (W1, option b); it would sit beside the attestation path and never in it.

### W10 — Fit with feature 001 and openSig's project rules (PROPOSED, WA-11)

This change touches nothing in `add-corporate-signing-mvp` or feature 001. It is consistent with what they say:

| Feature 001 or project rule | This change |
|---|---|
| Non-goal: "personal PKI enrollment" | Enrolls no key. Keys are openWallet's; bindings are the relying party's registry. |
| Non-goal: "patient/clinical production workflows" | Receives no patient data or clinical content, and attests no clinical fact. Production use would need its own feature and release gates. |
| "Keep clinical attestation and amendments in openChart" | Kept: ruling 155, and W4. |
| "human personal approval outside Hermes service credentials" | openSig approves nothing. Where the relying party asks for per-use authorization, it is the holder's, on the device that holds their key (the seam, "Which custody a sign-off needs"). |
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

- openSig code leg: an attester and a verifier for wallet-bound attestations, deployable inside a relying party's deployment, depending on openWallet's verification of `wallet-challenge-proof` and of `wallet-webauthn-proof` at a pinned openWallet assembly root; independently generated known-answer vectors, on openWallet feature 016's precedent;
- an attestation record contract, placed where openSig's governance places contracts;
- no change to feature 001's HTTP contract.

**The relying party's credential to the attester.** openSig authenticates the relying party that asks, and records it. PROPOSED: a credential internal to the deployment, such as mutual TLS with the relying party's client certificate pinned by fingerprint, as openWallet's reference service does, issued when the attester is installed. The relying party holds it; nothing outside the deployment is involved.

Names that would become machine keys once released, all PROPOSED and cheapest to change now: the capability `wallet-bound-attestation`; the profile `opensig.wallet-bound-attestation.v1`; the signing domain `openSig.wallet-bound-attestation.v1`; the four results; every reason code in W6; and the attestation's field names in W3, `authenticator_flags` with its four members, `sign_count_not_increasing`, `custody_basis`, `unresolved`, `proof_sha256` and `references_differing` among them, the flag names following openWallet's passkey profile and the names openChart's draft already takes.

### W13 — Non-goals and named successors (PROPOSED, WA-14)

Each is a scope choice the owner may overturn:

1. **A sign-off made in another system.** Ruling 186: openChart only, in version 1; it counts later, once openWallet and openSig can prove it to the chart. That needs a challenge the chart can trust from another system, and its own change.
2. **Anything about the licence.** openWallet's licence verification, checked by the relying party.
3. **Evidence that a key's custody is what its wallet declares.** A successor in openWallet; openSig records the declaration.
4. **Attesting proof profiles other than openWallet's two.** The passkey and security-key profile, drafted under ruling 208 as `add-webauthn-proof-profile`, is no longer a non-goal: openSig attests it (W2, W3, W6). Any further profile needs its own change.
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

**The aim, ruled (ruling 214).** "Yes, against forgery: the verification names
the practitioner's own signing keys and is signed by the independent verifier.
It does not stop that person deleting records or back-dating; outside
timestamps can close that later." That person is whoever runs the practice's
server. Read for this seam (PROPOSED): a forgery is a sign-off in a
practitioner's name made without that practitioner's key; the practitioner is
the licence holder the relied-on verification names; and an honest check is
one run on a copy of the records with the verifier keys from a release of the
relying system. The aim holds only where the verifier identified the licensee
itself, by its own means and independently of the deployment, in a session it
began with the practitioner's client itself, which the deployment neither began
nor can join, and in which each key the verification names proved possession; a
proof relayed through the deployment cannot show that, so no verification names
a key on one. Limits
beyond the ruling's words (PROPOSED): whoever runs the deployment can also hide
records, withhold revocation lists, run altered software that skips the checks,
refuse service, show the account a decision is filed under as someone else's,
and show the practitioner one statement while the key signs another, because no
key in either proof profile shows its holder what it signs; and whoever
administers or can recover the device, the browser, an extension in it, or the
passkey provider that the practitioner uses to register a key or to complete the
verifier's session, a synced passkey's account among them, can act without the
practitioner: it can answer the verifier with a credential it registered, or
sign with a synced passkey, and neither profile can see it. The practitioner
declares that these are theirs and not administered by the practice, and the
declaration is recorded; it is their word.

| Question | The answer both drafts give |
|---|---|
| What identifies a wallet | The `wallet_id` of its openWallet wallet record. It is `wallet_ref` in a proof and in an attestation, and `openwallet_id` in openChart's sign-off evidence. It is attestation, never the identifier of a person or a patient. |
| Who the signer is | The licence holder whose licence the relied-on verification names, for a key it names. Which account the relying party shows a decision under is its own record, which whoever runs the deployment can alter. An honest check reports the signer from the verification's licence fields, never from the account, and the relying party raises any difference between that licence and its own record of the account's licence. |
| What identifies a key | The pair (`did`, `key_id`) under which the wallet declares it, and its `key_fingerprint`: `sha256:` and the lowercase hex SHA-256 of the raw public key, openWallet's one spelling; for a passkey's P-256 key, openWallet's draft passkey profile fixes the raw form. It is `key_ref` in a proof, written as the DID URL `<did>#<key_id>`. An attestation records the reference as the proof gave it and, where openSig found a binding, the fingerprint recomputed from the public key that binding named; it never fills from the proof a value it did not resolve. |
| Which keys may make a sign-off | Only a key that the relying party bound to the practitioner's account individually, by that key's own proof of possession in the practitioner's own session, and whose fingerprint a licence verification in force names. The relying party takes its held copy of the wallet record only from the practitioner, and no administrator adds a key to it. Each bound key has its own binding; with a registry that keeps one current binding per subject, the subject is that account's binding of that key. Replacing a key is a new binding and needs a verification that names the new key. |
| What custody a key declares | One model of openWallet's closed custody registry (`openxwallet-custody`, registry version 1), declared for that key in its wallet record: `holder_readable`, `isolated_invocable` or `isolated_per_use_authorized`. In a binding used for a sign-off, the custody reference is `openxwallet-custody:<registry_version>:<model>` and equals the model the held wallet record declares for that key; openSig's `custody_reference_mismatch` compares those two. Under the registry, as ratified, only `isolated_per_use_authorized` evidences that the holder acted: each use needs an authorization the holder's execution context cannot itself supply, for which the registry names "a hardware presence check, an external policy approval, a separate custodian". A wallet may declare such a key only if its own custody is the same model, because no declared key may outrank its wallet. A declaration is not proof: neither openWallet's proof nor openSig's attestation shows that a key's custody is what the wallet declares; a custody-acceptance record could show that for a key, and openWallet's draft passkey profile records whether a declaration rests on attestation evidence or on the holder's word, keeping the attestation object; until a check verifies that object against a root it trusts, `attested` is the relying party's own claim. How each use was authorized is shown only under `wallet-webauthn-proof`, whose user-verification flag shows that verification happened on the authenticator, as far as the authenticator is trusted; under the ratified profile nothing shows it. |
| Which custody a sign-off needs | The relying party decides, and states which custody it accepts for a practitioner's sign-off. What more it asks of a key, such as each use authorized by the practitioner on the device that holds the key and never by a code typed into the computer that asks for the signature, is an acceptance rule of its own, which leaves the registry's statement as it is. For openChart, ruling 209: "Yes, but only on a device the practitioner has enrolled, as ruling 134 allows for approvals. A security key always counts. Controlled substances stay out (ruling 83) and would need stronger keys." Enrolling a device is the relying party's. No model of the registry as written describes a synced passkey, so openWallet's draft passkey profile binds none until one does, and the yes for a synced passkey waits on that (its Open question 1). openSig records the model the key declares, and for a passkey proof the authenticator's flags as signed, and never raises either. |
| What the proof is made over | In openChart's version 1, a passkey or a security key (ruling 208), under openWallet's draft profile `wallet-webauthn-proof` (change `add-webauthn-proof-profile`): the authenticator signs its authenticator data followed by the SHA-256 of the client data, and the client data's challenge is derived from the proof's unsigned binding fields, so the proof binds what the ratified profile binds and also the relying party, by its relying-party identifier hash and origin. openWallet's ratified possession-only profile `wallet-challenge-proof` stays as ratified: profile constant `openwallet.challenge-proof.v0`; Ed25519 over the domain separator `openWallet.challenge-proof.v0` and a NUL byte, then the RFC 8785 canonical form of every unsigned proof field. In either profile the `challenge_digest` is `sha256:` and the SHA-256 of the relying party's RFC 8785 canonical statement, which carries the relying party's single-use challenge. For openChart that statement is the sign-off statement, and the digest is its `statement_sha256`. The challenge reference is an opaque random value, 32 bytes drawn at random by the relying party's own server and used once, in unpadded base64url, that carries no meaning. The proof also binds the wallet, the key, the subject, the custody reference, the binding and its revision. openSig is given the digest and the challenge reference, never the statement. |
| Who owns consent | The relying party. A sign-off is consent to an exact request, and openWallet's ratified profile does "not assert MFA, human presence, explicit human consent, workload composition, delegation or domain permission". The relying party evidences what the signer was shown, refusing a signature over a digest that differs from it; re-authentication at signing; and, through the custody it accepts, per-use authorization at the key. Neither the proof nor the attestation is consent. |
| Who verifies what | openWallet's verification, in the proof's own profile, checks a proof against the key's binding, which must be current, unrevoked and time valid. openSig runs that verification, never a second implementation of it, and seals the result with what it was given, what it resolved (the binding the proof rested on, where it found one) and its own time. Verifying an attestation is checking openSig's signature over it; validating its result is a second step, and only a `verified` result's sealed proof must verify. A `not_verified` attestation is evidence of a failed attempt, verified by its signature alone; its sealed proof need not verify, and it may carry no binding. At acceptance the relying party accepts only a `verified` result: it verifies the attestation under openSig's attestation key; verifies the sealed proof itself under the sealed public key over its own recomputed statement digest; checks that the sealed fingerprint is a key it bound and that the licence verification names; checks the sealed challenge reference against its statement; and checks the licence verification under the verifier key it holds, with each proof of possession it records, as the proof's own profile defines the check of a recorded proof. |
| Where it is checked | Inside the relying party's own deployment (ruling 156). The key binding registry, the held wallet records, the openSig attester and its attestation keys, the held verifier keys, each for the licensing authorities it is held for, every revocation received from each verifier, kept as permanent, and every licence verification the relying party has received, with the supersession index it keeps of them, are held there. The practitioner's key stays in the practitioner's custody. The check contacts and waits on nothing outside the deployment. The deployment fetches the verifiers' revocation lists when it can, apart from any check, and a revocation list older than the relying party's freshness bound flags the reliance and never blocks it. |
| What neither states | The content behind the digest, the practitioner's authority, their consent, whether they were present beyond what the declared custody evidences, and whether the sign-off counts. openWallet's licence verification states the licence; openSig's attestation states nothing about it. |

## Design calls

Every call below goes beyond the owner's words and is PROPOSED, and each is put to the owner.

| # | Design call | Where |
|---|---|---|
| WA-1 | One capability separate from feature 001, with defined terms. | Terms; W1 |
| WA-2 | What the attestation adds (a sealed binding and time, a single-purpose key in a separate process, a second check, replay refusal) and what it does not (independence from the operator, outside evidence of time, a way to skip verifying the proof); options (a), (b) and (c). | W1 |
| WA-3 | openSig runs openWallet's verification of the profile the proof names, `wallet-challenge-proof` or `wallet-webauthn-proof`, never a second; compares custody references; records the challenge reference as given, and a passkey proof's signed flags under openWallet's names. | W2 |
| WA-4 | The fields of an attestation, kept apart as openSig's own, as given and as resolved, including the sealed proof and binding, the recomputed fingerprint, `custody_basis`, and for a passkey proof `authenticator_flags` (`user_present`, `user_verified`, `backup_eligible`, `backup_state`) and `sign_count_not_increasing`; the whole proof sealed only where openSig found its binding and every reference matched it, and a passkey proof only where its credential identifier and revision matched too and `signature_verified` was reported, and otherwise `proof_sha256`, with `references_differing` where a reference differs; `proof_sha256`, `references_differing` and `reason` only in a `not_verified` attestation; `unresolved` always present, naming what openSig did not obtain; no resolved value filled from the proof; the order of the checks, and what each negative attestation can carry. | W3 |
| WA-5 | openSig receives only the challenge reference, in a fixed opaque form, the digest and the proof, and states nothing about content, licence, authority, consent, presence or whether a sign-off counts. | W4 |
| WA-6 | The attester, the binding registry, the held wallet records, the attestation keys and the attestations are held inside the deployment; verifying an attestation is checking its signature, and validating its result is a second step in which only a `verified` result's sealed proof must verify; a relying party accepts a sign-off only on a `verified` result (a SHALL in the delta) and verifies the sealed proof itself; verifying again never claims currency. | W5 |
| WA-7 | Four results with closed reasons, with the reasons openWallet's passkey profile adds, each of its refusals named, `backup_state_invalid` among them, and the mappings to `proof_malformed` and `binding_not_current` stated; the list closed; `not_verified` is signed, `refused` and `unavailable` are not; what `custody_reference_mismatch` and `clock_untrusted` test. | W6 |
| WA-8 | The attestation key is a key of the deployment, not of a wallet, evidencing at best the attester's environment; Ed25519 over RFC 8785 with its own signing domain; retired keys kept. | W7 |
| WA-9 | One attestation per relying party's challenge reference and one per challenge digest; an identical retry, compared in the RFC 8785 canonical form of the decoded proof, gets the stored attestation, and any other repeat is refused; a passkey proof whose ES256 signature was rewritten is a different request, refused, never attested twice. | W8 |
| WA-10 | "Wallet" is an openWallet wallet, never the anchoring wallet; anchoring digests stays beside the attestation path. | W9 |
| WA-11 | Consistent with feature 001 and the project rules, and changes nothing in them. | W10 |
| WA-12 | Stacked on pull request #2. | W11 |
| WA-13 | The code surface, the relying party's credential to the attester, and every name that becomes a machine key. | W12 |
| WA-14 | The non-goals and named successors; openWallet's passkey profile is attested, no longer a non-goal. | W13 |
| WA-15 | The seam with openWallet, in the words both designs share. | "The seam between openWallet and openSig" |

## Tensions

- **T1. The attester runs under the practice's operator.** Ruling 156 puts it inside the deployment, so it adds no independence from whoever runs it, and no outside evidence of time (W1; Open question 1).
- **T2. openSig's scope widens.** openSig is a document-signing platform, and this capability signs no document. It fits openSig's role as the place signing evidence is made, and it attests no clinical fact, but it is a second kind of thing in the project.
- **T3. openSig cannot see what the challenge reference belongs to.** It records the reference as given and refuses a different request that repeats the reference or the digest (W2, W8); only the relying party can check it against the statement.
- **T4. One more component in a small practice's deployment.** Ruling 156 requires it to run there (Open question 10).
- **T5. Verifying again does not restore currency.** It shows what was true when the attestation was made, which is what an audit needs, and not that the key is still bound (W5).
- **T6. The pull request is stacked on two unmerged pull requests** (W11).

## Open questions

Each is a judgment the author made, or a choice the owner must make, with a recommendation. None is ruled. The first two are the ones the rest hang on.

1. **What is the attestation worth, given that it runs under the practice's operator?** The options are W1's: (a) as specified; (b) the same, with attestation digests optionally anchored, as a successor, for outside evidence against back-dating, never blocking signing; (c) the same, with the attester under a separate administrator. Recommendation: (a) now, with (b) as a named successor. The attestation is a sealed record of the binding and the time under a single-purpose key in a separate process, which ruling 151's "the openSig will attest" asks for; it is not protection against the operator, and openWallet's draft states that threat model's aim and limits.
2. **Who evidences exact-request consent?** Recommendation: the relying party, as openWallet's ratified profile's record says; openSig's attestation claims none, and says so (W4).
3. **Refuse repeats of both the challenge reference and the digest?** Recommendation: yes, for any repeat that differs in anything; the digest is the one the proof binds. An identical retry gets the stored attestation, so a lost answer never makes the practitioner sign again (W8).
4. **How is the attestation key held?** Recommendation: a key of the deployment, used for nothing else, that the attester's own application process cannot read; it evidences the attester's environment at best and is never declared in an openWallet wallet (W7); retired public keys kept by the relying party.
5. **How long are attestations, and the attester's record of attested references and digests, kept?** Recommendation: for as long as the records they support, under whatever retention rule counsel sets for those records.
6. **Sign `not_verified` results?** Recommendation: yes, as evidence of failed attempts, in the shape of W3 and verified by their signature alone (W5); never `refused` or `unavailable`, where there is nothing to attest.
7. **The form of the challenge reference.** Recommendation: 32 random bytes in unpadded base64url, refused in any other form (W4).
8. **The relying party's credential to the attester.** Recommendation: mutual TLS inside the deployment, with the client certificate pinned by fingerprint (W12).
9. **Placement.** Recommendation: stay stacked on pull request #2, and retarget to `main` once #1 and #2 merge.
10. **How does a small practice run the attester?** Recommendation: as a small service deployed beside openChart, under the practice's operator; never a hosted openSig. Option (c) of Open question 1 would change who administers it.
11. **How should openChart use the four results?** Recommendation, as openChart's own decision: record which result and reason each failed attempt had; treat `unavailable` and `not_verified` as AN-14 already does; and raise a `not_verified` with reason `signature_invalid` or `digest_mismatch`, and any `digest_already_attested`, to the medical director as a possible forgery or replay, and never to the signer alone (openWallet's draft names a second recipient where the signer is the medical director).
12. **The names that become machine keys** (W12). Recommendation: confirm or change them now.
13. **Should an attestation carry the relying party's statement type or purpose?** Recommendation: no. openSig receives only the digest; keeping statements of different purposes apart is the relying party's statement's job.

## Status

Proposed. Six reviews have run: an independent review of the first draft at `aabc190`, a confirmation review of the revision at `34eed5e`, a final check of `25aae29` together with an automated review, a confirmation of `b7d37a5`, an independent review of `9100b3c` together with openWallet's draft passkey profile, and a confirmation of `92b097e`; this revision answers all six and records rulings 208, 209 and 214, which are rulings and not ratification. It also fixes, for openChart, the names of the authenticator-flag fields, that `unresolved` is always present, and that `proof_sha256`, `references_differing` and `reason` appear only in a `not_verified` attestation. `tasks.md` 1.1 still schedules an adversarial reviewer over this revision before ratification. Ratification is pending, by Brett Heap.
