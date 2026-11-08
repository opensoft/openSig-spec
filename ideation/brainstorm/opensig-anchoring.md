# Optional Blockchain Anchoring — Brainstorm

Status: brainstorm
Kind: architecture
Summary: Optional ledger adapters anchor privacy-preserving commitments to completed PDF versions without blocking signing.
Topics: opensig, blockchain-anchoring, privacy
Repository context: openSig; external proof adapters
Captured: 2026-11-08

## Possible feats

- **Independent inclusion proof** — verify a document commitment without requiring the openSig service.

## Commitment model

After all intended signatures and timestamps, verify and archive exact PDF bytes. Compute their digest, then a versioned domain-separated commitment containing algorithm identifiers, digest and a cryptographically random nonce. Define canonical encoding and test vectors. Optional batching uses a specified Merkle tree with domain-separated leaf/node encoding, ordering and odd-leaf behavior.

Publish only a commitment or root. Never publish PDFs, signature images, names, patient or tenant identifiers. Raw predictable-document hashes can leak equality or permit guessing; nonces and batching reduce exposure but do not eliminate transaction metadata. Retain nonce and inclusion path in the private portable proof package.

Receipt includes schema/algorithm versions, artifact digest, nonce, optional Merkle proof, network, transaction ID, block reference, finality status and verification instructions. Verify successful transaction payload and inclusion, not transaction ID existence alone. Handle reorganization, provider disagreement and confirmation thresholds explicitly.

## Artifact boundary and failure behavior

Receipt remains external to the PDF so it does not change anchored bytes. Later signatures or archival-validation-data changes create new artifact versions/digests and optional new anchors. A ledger proves inclusion of a commitment, not signer identity, authority, legal validity or exact real-world signing time.

Provide disabled and fake adapters plus submit/observe/verify interfaces. Enforce idempotency, fee/spend limits and dedicated wallet custody. Ledger outages or failures never erase a successful signing result. No network/provider/token is selected or transaction expenditure authorized here.

## Alternatives and open questions

Assess no ledger, trusted timestamping, public ledger and permissioned ledger against the actual proof need. Choose governance, finality, cost, receipt retention and proof availability only when anchoring is scheduled.

## Relationships

[Signing evidence](opensig-signing-evidence.md); [trust synthesis](opensig-synthesis-trust.md).
