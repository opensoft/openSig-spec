## ADDED Requirements

### Requirement: Attest that a proof came from a wallet's declared key
When a relying party that openSig authenticates gives it a single-use challenge reference, the digest a proof was made over, and a proof made under openWallet's possession-only profile `wallet-challenge-proof`, openSig SHALL verify the proof with openWallet's own verification of that profile against the current binding of the wallet's key, SHALL compare the binding's custody reference with the custody the held wallet record declares for that key, and SHALL record the result as a wallet-bound attestation signed with openSig's attestation key. openSig SHALL NOT implement a second verification of openWallet's profile. An attestation SHALL state: the wallet; the key, with its fingerprint; the custody model that key declares; the binding and revision the proof was checked against, with the public key it named; the proof itself; the digest the proof was made over; the relying party and its challenge reference, as the relying party gave them; the time; the result; and the openWallet verification used. It SHALL carry everything a relying party needs to verify the sealed proof itself, under the sealed public key, over a digest the relying party recomputes.

#### Scenario: A proof verifies
- **WHEN** a relying party submits a challenge reference, a digest and a proof made over that digest by a key that the wallet's current binding names
- **THEN** openSig returns an attestation with result `verified`, signed with its attestation key, stating the wallet, the key and its fingerprint, the declared custody, the binding and its revision with its public key, the proof, the digest, the relying party, the challenge reference, the time and the verification used

#### Scenario: A proof made over another digest
- **WHEN** the proof's challenge digest differs from the digest the relying party submitted
- **THEN** the result is `not_verified` with reason `digest_mismatch`, and no `verified` attestation exists for that digest and challenge reference

#### Scenario: The key is not currently bound
- **WHEN** the binding the proof names is revoked, expired or revised, or names another key or another wallet
- **THEN** the result is `not_verified` with a reason naming which, and the attestation records the binding as the registry returned it

#### Scenario: The binding's custody differs from the wallet record's
- **WHEN** the binding's custody reference names a model or registry version other than the one the held wallet record declares for that key
- **THEN** the result is `not_verified` with reason `custody_reference_mismatch`

#### Scenario: No second verification of the profile
- **WHEN** openSig verifies a proof
- **THEN** it uses openWallet's verification of `wallet-challenge-proof`, at the openWallet release the deployment pins, and the attestation names that verification

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
openSig SHALL produce a wallet-bound attestation inside the relying party's own deployment, contacting and waiting on no service outside it, and SHALL make every attestation verifiable again there from what the relying party holds: the attestation, the proof and the binding it seals, and openSig's attestation public keys, obtained ahead of time. For this the deployment SHALL hold an openSig attester whose attestation key is in custody there, the key binding registry that proofs are checked against or a current replica of it, the held records of the wallets bound there, and openSig's attestation public keys, retired ones included. Verifying again SHALL establish that openSig attested that result over that digest and that the sealed proof verifies under the sealed public key over that digest; it SHALL NOT claim that the binding is still current.

#### Scenario: Attestation with no outside network
- **WHEN** a relying party asks for an attestation while no network outside its deployment is reachable
- **THEN** openSig produces the same result it would produce with the network reachable, and contacts and waits on no service outside the deployment

#### Scenario: An old attestation verifies after a key rotation
- **WHEN** an attestation made years earlier is verified again after openSig's attestation key and the wallet's key have both been retired
- **THEN** it verifies against the retired attestation public key the relying party holds, and its sealed proof verifies under its sealed public key

#### Scenario: Verifying again does not restore currency
- **WHEN** an attestation is verified again after the wallet's binding was revoked
- **THEN** verification states that the binding was current at the attestation's time, and states nothing about it now

#### Scenario: An altered attestation fails
- **WHEN** any field of a stored attestation, its sealed proof or its sealed binding is changed
- **THEN** verifying it again fails

### Requirement: Never stand in for the holder, and fail closed with distinct results
openSig SHALL NOT hold a wallet holder's private key, produce a proof for them, sign anything in their place or approve anything on their behalf. Its attestation key SHALL be a key of the deployment, used to sign wallet-bound attestations only, under a signing domain of its own, and never a document seal or any other signature. openSig SHALL fail closed on every doubt and SHALL answer every request with exactly one of: `refused`, with no check made and no attestation, for a request it does not accept; `verified`, with an attestation; `not_verified`, with an attestation of that result and a reason from a closed list; or `unavailable`, with no attestation and a reason, when it could not complete the check. It SHALL NOT turn `unavailable` into another result. For each relying party it SHALL attest at most once for a challenge reference and at most once for a challenge digest, and SHALL refuse a request that repeats either.

#### Scenario: The binding registry cannot be reached
- **WHEN** openSig cannot reach the key binding registry while checking a proof
- **THEN** the result is `unavailable` with reason `binding_registry_unreachable`, and no attestation is issued

#### Scenario: A doubtful proof does not verify
- **WHEN** a proof carries a duplicate key, an unknown field, a noncanonical signature or an unsupported profile
- **THEN** the result is `not_verified` with a reason naming the fault

#### Scenario: A challenge reference is attested once
- **WHEN** a relying party submits a challenge reference that openSig has already attested for it
- **THEN** the request is `refused` with reason `challenge_already_attested`, and no second attestation is issued

#### Scenario: A digest is attested once, whatever the challenge reference
- **WHEN** a relying party submits a proof and a digest that openSig has already attested for it, with a new challenge reference
- **THEN** the request is `refused` with reason `digest_already_attested`, and no second attestation is issued

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
