## 1. Governance and handoff

- [ ] 1.1 One adversarial review of this revision of the proposal, design and delta (three reviews have already run: an independent review of the first draft at `aabc190`, a confirmation review of the revision at `34eed5e`, a final check of `25aae29` with an automated review, and a confirmation of `b7d37a5`), reading against openWallet's ratified `wallet-challenge-proof`, openWallet's draft `add-practitioner-profile-and-licence-verification`, openChart-spec#5's sign-off (CA-04, CA-08, AN-14) and `add-corporate-signing-mvp`. Record each finding and its disposition in `design.md`.
- [ ] 1.2 **Owner:** rule on the thirteen open questions in `design.md`, the first two first, and dispose of the design calls WA-1 to WA-15.
- [ ] 1.3 **Owner:** ratify or return. Brett Heap.
- [ ] 1.4 Before ratifying, compare `design.md`'s section "The seam between openWallet and openSig" with the same section of openWallet's draft at the head being ratified there. They must be identical; a difference is resolved in both drafts first.
- [ ] 1.5 On ratification, record the accepted baseline and hand off to exactly one Speckit feature, its number allocated then.

## 2. Implementation owner

The only executable implementation checklist will be that feature's `tasks.md`. Do not duplicate build tasks here. What it must cover, as named in `design.md` W12: the attester and verifier, the attestation record contract, known-answer vectors, and synthetic fixtures only.

## 3. Landing and archive

- [ ] 3.1 Before landing, retarget this change's pull request to `main` once pull requests #1 and #2 have merged (design W11).
- [ ] 3.2 After the Speckit feature and matching assembly pins land, reconcile as-built behavior and archive this change; record verification and landing references.
