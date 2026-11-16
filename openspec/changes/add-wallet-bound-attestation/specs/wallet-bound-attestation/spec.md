## ADDED Requirements

### Requirement: Attest that a proof came from a wallet's declared key
When a relying party that openSig authenticates gives it a single-use challenge reference, the digest a proof was made over, and a proof made under one of openWallet's possession profiles, the ratified `wallet-challenge-proof` or the passkey and security-key profile `wallet-webauthn-proof` (openWallet's draft `add-webauthn-proof-profile`), openSig SHALL verify the proof with openWallet's own verification of the profile the proof names against the current binding of the wallet's key, SHALL compare the binding's custody reference with the custody the held wallet record declares for that key, and SHALL record the result as a wallet-bound attestation signed with openSig's attestation key. openSig SHALL NOT implement a second verification of either profile. An attestation SHALL state, apart, what openSig was given and what openSig resolved for itself. What it was given, as the relying party gave it: the relying party, its challenge reference, the digest, and the proof. openSig SHALL seal the proof itself only where it found a binding and every reference in the proof equals that binding's; otherwise it SHALL seal only the SHA-256 of the proof bytes received, with, where it found a binding, the names of the references that differ from it, so that no reference a caller chose enters its records unless a binding openSig found confirms it. What openSig resolved: the binding and revision the proof was checked against, with the public key it named and that key's fingerprint, which openSig recomputes; and the custody model and the states the held wallet record declares for that key. It SHALL also state the time, the result, and the openWallet verification used. A `verified` attestation SHALL carry every resolved value. A `not_verified` attestation SHALL carry each value openSig resolved before the check failed, and SHALL name each one it did not obtain, with why. No resolved value SHALL ever be filled from the proof: the references inside a proof are only what the proof claims. For a `verified` result the attestation SHALL carry everything a relying party needs to verify the sealed proof itself, under the sealed public key, over a digest the relying party recomputes.

#### Scenario: A proof verifies
- **WHEN** a relying party submits a challenge reference, a digest and a proof made over that digest by a key that the wallet's current binding names
- **THEN** openSig returns an attestation with result `verified`, signed with its attestation key, stating the wallet, the key and its fingerprint, the declared custody, the binding and its revision with its public key, the proof, the digest, the relying party, the challenge reference, the time and the verification used

#### Scenario: A proof made over another digest
- **WHEN** the proof's challenge digest differs from the digest the relying party submitted
- **THEN** the result is `not_verified` with reason `digest_mismatch`, and no `verified` attestation exists for that digest and challenge reference

#### Scenario: The key is not currently bound
- **WHEN** the binding the proof names is revoked, expired or revised, or names another key or another wallet
- **THEN** the result is `not_verified` with a reason naming which, and the attestation records the binding as the registry returned it

#### Scenario: No binding is found
- **WHEN** the key binding registry holds no binding for the reference the proof names
- **THEN** the result is `not_verified` with reason `binding_not_found`, and the attestation records only the SHA-256 of the proof bytes received and names the binding, the key's fingerprint, the declared custody and the states as not obtained
- **AND** none of them is filled from the references inside the proof

#### Scenario: A proof whose references differ from its binding's is recorded by its digest
- **WHEN** the binding the proof names is found, but the proof's key or wallet reference differs from the binding's
- **THEN** the result is `not_verified` with reason `binding_not_current`, and the attestation records the SHA-256 of the proof bytes received and the names of the references that differ, and not the proof itself

#### Scenario: A proof that does not decode is recorded by its digest
- **WHEN** the proof does not pass the profile's strict decoding, for instance because it repeats a key or is not a JSON object
- **THEN** the result is `not_verified` with reason `proof_malformed`, the attestation records only the SHA-256 of the bytes received, and openSig resolves nothing for it

#### Scenario: The binding's custody differs from the wallet record's
- **WHEN** the binding's custody reference names a model or registry version other than the one the held wallet record declares for that key
- **THEN** the result is `not_verified` with reason `custody_reference_mismatch`

#### Scenario: No second verification of the profile
- **WHEN** openSig verifies a proof
- **THEN** it uses openWallet's verification of the profile the proof names, at the openWallet release the deployment pins, and the attestation names that verification

#### Scenario: A passkey proof is attested
- **WHEN** a proof under `wallet-webauthn-proof` verifies against the current binding of the key that made it
- **THEN** the attestation is `verified` and seals the proof, with its authenticator data, client data and signature, and the binding, with its relying-party identifier, allowed origins, credential identifier, public key and algorithm, so that the relying party can verify the sealed proof itself
- **AND** the user presence, user verification and backup flags the authenticator signed are recorded as signed

### Requirement: State nothing about content, licence, authority, consent or whether a sign-off counts
openSig SHALL accept from a relying party only the challenge reference, the digest and the proof, and SHALL refuse a request that carries anything else, such as the statement behind the digest, clinical content or a patient identifier. It SHALL accept a challenge reference only as an opaque random value of the fixed form the relying party and openSig agree, 32 random bytes in unpadded base64url, and SHALL refuse any other. An attestation SHALL NOT state what the digest is a digest of, the signer's licence, the signer's authority, the signer's consent, whether the holder was present, or whether the act behind the digest counts. The custody an attestation records SHALL be the custody the key declares, never more, and the relying party decides what follows from it. A wallet-bound attestation is not a clinical attestation, and openSig SHALL NOT present it as one.

#### Scenario: A request carrying content is refused
- **WHEN** a relying party submits the statement, clinical content or a patient identifier with a proof
- **THEN** openSig refuses the request, makes no check, issues no attestation and stores none of what was sent

#### Scenario: A challenge reference that is not opaque is refused
- **WHEN** a relying party submits a challenge reference that is not 32 bytes in unpadded base64url
- **THEN** openSig refuses the request with reason `request_shape_invalid`, and stores none of it

#### Scenario: Declared custody is recorded and never raised
- **WHEN** the key that made a proof declares custody `isolated_invocable`
- **THEN** the attestation records `isolated_invocable` as the declared custody, and states nothing about whether the holder acted

#### Scenario: A verified attestation is not a decision
- **WHEN** a relying party reads an attestation with result `verified`
- **THEN** it learns only that the named key made that proof over that digest under the binding then current, and the signer's licence, authority, consent and whether the sign-off counts remain the relying party's to decide

### Requirement: Produce and verify attestations again inside the relying party's deployment
openSig SHALL produce a wallet-bound attestation inside the relying party's own deployment, contacting and waiting on no service outside it, and SHALL make every attestation verifiable again there from what the relying party holds: the attestation, the proof and the binding it seals, and openSig's attestation public keys, obtained ahead of time. For this the deployment SHALL hold an openSig attester whose attestation key is in custody there, the key binding registry that proofs are checked against or a current replica of it, the held records of the wallets bound there, and openSig's attestation public keys, retired ones included. Verifying an attestation SHALL mean verifying openSig's signature over it under openSig's attestation public key; that establishes that openSig recorded that result, for that digest and challenge reference, at that time, whatever the result. Validating the recorded result is a second step. For a `verified` result the sealed proof SHALL verify under the sealed public key over the sealed digest, and an attestation whose result is `verified` but whose sealed proof does not verify SHALL be treated as invalid. For a `not_verified` result the sealed proof, where one is sealed, is evidence of what was submitted, and no check SHALL require it to verify. Neither step SHALL claim that the binding is still current. A relying party SHALL accept a sign-off only on an attestation whose result is `verified`, whatever a negative attestation's sealed proof shows.

#### Scenario: Attestation with no outside network
- **WHEN** a relying party asks for an attestation while no network outside its deployment is reachable
- **THEN** openSig produces the same result it would produce with the network reachable, and contacts and waits on no service outside the deployment

#### Scenario: An old attestation verifies after a key rotation
- **WHEN** a `verified` attestation made years earlier is verified again after openSig's attestation key and the wallet's key have both been retired
- **THEN** its signature verifies against the retired attestation public key the relying party holds, and, its result being `verified`, its sealed proof verifies under its sealed public key

#### Scenario: A negative attestation verifies by its signature
- **WHEN** a `not_verified` attestation with reason `digest_mismatch`, `signature_invalid` or `binding_not_found` is verified again
- **THEN** its signature verifies, its result and reason are read as recorded, and no check requires its sealed proof to verify or a binding to be present

#### Scenario: A negative attestation is never accepted
- **WHEN** a relying party holds a `not_verified` attestation with reason `key_retired`, `wallet_not_active` or `custody_reference_mismatch`, whose sealed proof does verify under its sealed public key
- **THEN** the relying party does not accept the sign-off, because the result is not `verified`

#### Scenario: A verified result whose sealed proof does not verify
- **WHEN** an attestation whose result is `verified` carries a sealed proof that does not verify under its sealed public key over its sealed digest
- **THEN** the relying party treats the attestation as invalid, though openSig's signature over it verifies

#### Scenario: Verifying again does not restore currency
- **WHEN** an attestation is verified again after the wallet's binding was revoked
- **THEN** verification states that the binding was current at the attestation's time, and states nothing about it now

#### Scenario: An altered attestation fails
- **WHEN** any field of a stored attestation, its sealed proof or its sealed binding is changed
- **THEN** verifying it again fails

### Requirement: Never stand in for the holder, and fail closed with distinct results
openSig SHALL NOT hold a wallet holder's private key, produce a proof for them, sign anything in their place or approve anything on their behalf. Its attestation key SHALL be a key of the deployment, used to sign wallet-bound attestations only, under a signing domain of its own, and never a document seal or any other signature. openSig SHALL fail closed on every doubt and SHALL answer every request with exactly one of: `refused`, with no check made and no attestation, for a request it does not accept; `verified`, with an attestation; `not_verified`, with an attestation of that result and a reason from a closed list; or `unavailable`, with no attestation and a reason, when it could not complete the check. It SHALL NOT turn `unavailable` into another result. For each relying party it SHALL attest at most once for a challenge reference and at most once for a challenge digest. A request from the same relying party that is identical to one openSig has already attested SHALL be answered with the stored attestation, unchanged, and SHALL NOT produce a second one. Two requests are identical when their challenge references and digests are equal as given and their proofs are equal in the RFC 8785 canonical form of the decoded proof, the form in which both of openWallet's profiles canonicalize their unsigned fields; so re-serializing a proof never turns a retry into a refusal. Where a signature can be rewritten into another valid one, as an ES256 signature under `wallet-webauthn-proof` can, the rewritten proof is a different request, and is refused, never attested twice. A proof that does not pass the profile's strict decoding has no canonical form, and is compared by the SHA-256 of the bytes received. A request that repeats either the challenge reference or the digest and differs in anything else SHALL be refused. A `refused` or `unavailable` answer attests nothing, so a request after one is checked afresh.

#### Scenario: The binding registry cannot be reached
- **WHEN** openSig cannot reach the key binding registry while checking a proof
- **THEN** the result is `unavailable` with reason `binding_registry_unreachable`, and no attestation is issued

#### Scenario: A doubtful proof does not verify
- **WHEN** a proof carries a duplicate key, an unknown field, a noncanonical signature or an unsupported profile
- **THEN** the result is `not_verified` with a reason naming the fault

#### Scenario: An identical retry returns the stored attestation
- **WHEN** a relying party submits again a request that openSig has already attested for it, because the first answer was lost, with the same challenge reference and digest and the same proof, though serialized with its members in another order or with other whitespace
- **THEN** openSig returns the stored attestation unchanged, issues no second attestation, and the practitioner need not sign again

#### Scenario: A rewritten signature is not an identical retry
- **WHEN** a relying party submits again a passkey proof openSig has attested for it, with its ES256 signature rewritten into another valid signature
- **THEN** the request is refused as a repeat of the challenge reference and the digest, and no second attestation is issued

#### Scenario: A challenge reference is attested once
- **WHEN** a relying party submits a challenge reference that openSig has already attested for it, with a different digest or proof
- **THEN** the request is `refused` with reason `challenge_already_attested`, and no second attestation is issued

#### Scenario: A digest is attested once, whatever the challenge reference
- **WHEN** a relying party submits a proof and a digest that openSig has already attested for it, with a new challenge reference
- **THEN** the request is `refused` with reason `digest_already_attested`, and no second attestation is issued

#### Scenario: A check that could not be completed is made afresh
- **WHEN** a relying party resubmits a request whose earlier answer was `unavailable`
- **THEN** openSig checks it afresh, because nothing was attested for it

#### Scenario: openSig never signs for the holder
- **WHEN** any caller asks openSig to produce a proof, or to sign a statement, in a wallet holder's place
- **THEN** openSig refuses, because it holds no holder key and its attestation key signs wallet-bound attestations only

### Requirement: A wallet is an openWallet wallet, never an anchoring wallet
In this capability "wallet" SHALL mean an openWallet wallet: a signing key, or a declared set of signing keys, held by a holder, as openWallet's `openxwallet` capability defines it. A ledger wallet that pays for optional anchoring SHALL NOT be attested, used as an attestation key or consulted by this capability. Producing, verifying or relying on a wallet-bound attestation SHALL NOT depend on anchoring, and attestation SHALL work with anchoring disabled.

#### Scenario: Anchoring disabled
- **WHEN** every anchoring adapter is disabled
- **THEN** wallet-bound attestations are produced and verified again exactly as before

#### Scenario: An anchoring wallet is not an openWallet wallet
- **WHEN** a proof is made with a key of a ledger wallet used for anchoring
- **THEN** the result is `not_verified`, because no openWallet key binding names that key, and the anchoring wallet's key is never used as an attestation key
