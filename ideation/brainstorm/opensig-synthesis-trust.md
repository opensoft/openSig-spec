# Synthesis: Approval Protection and External Proof — Brainstorm

Status: brainstorm
Kind: architecture
Summary: Human approval, protected PDF revisions and ledger inclusion supply complementary evidence with separate claims and failure states.
Topics: opensig, trust-evidence, synthesis
Repository context: openSig; approval-to-proof lifecycle
Captured: 2026-11-08

## Possible feats

- **Portable trust chain** — inspect each evidence layer without conflating its guarantees.

## Members and their joints

Members: [signing evidence](opensig-signing-evidence.md), [workflow/adapters](opensig-workflow-adapters.md), [anchoring](opensig-anchoring.md).

### Approval to finalization

Freeze revision and field inputs before approval. Bind each human action to those inputs; compose only approved values. The organization seal protects the output, while the approval evidence identifies the person and role. A personal certificate is a distinct signing mode. Reject stale approval rather than regenerate changed content silently.

### Durable completion to external proof

Verify and store the artifact before publishing completion. An independent anchoring job consumes its immutable digest and writes an external receipt. Outbox/idempotency bridges recoverable transactions; chain outages cannot mutate the PDF or reverse successful signing. Later PDF changes receive new artifact IDs/digests and receipts.

## Emergent behavior

An independent verifier can assess who approved a version under which authentication, whether signed content/revisions changed, and whether a commitment appears in a ledger. None substitutes for the others. Report integrity, trust, identity evidence, revision coverage, timestamp and anchoring separately.

## Tensions and open questions

Parallel human approval simplifies scheduling but complicates composition and personal-certificate ordering. Permanent ledgers conflict with privacy/deletion expectations; keep sensitive data off-chain and anchoring optional. Offline evidence may preserve historical trust material while current revocation checks need network access. Define exact verifier wording and time/trust policy in phase 0.
