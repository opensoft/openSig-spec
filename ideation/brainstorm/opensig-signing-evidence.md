# Human Approval and PDF Signature Evidence — Brainstorm

Status: brainstorm
Kind: architecture
Summary: openAutograph binds approval to exact document inputs and openCryptograph protects the resulting PDF with independently verifiable signatures.
Topics: opensig, signing-evidence, consent, digital-signatures
Repository context: openSig; human approval and cryptographic worker
Captured: 2026-11-08

## Possible feats

- **Portable evidence package** — verify approval, protected PDF revisions and certificate trust as separate claims.

## openAutograph

Present the exact PDF, recipient fields and consent text. Support drawn, typed or uploaded visible signatures, initials and dates. A signature image is a visual asset, not identity proof or permission to sign future documents. Saved appearances require access controls and a new authorized approval for each document.

Record tenant, envelope, signer/role, authentication assurance, revision digest, approved field values, consent-text version and server timestamp. Links contain expiring opaque tokens, never patient identifiers or document contents. Recheck authority on final approval; expired, revoked, consumed or cross-tenant sessions fail closed. Email/OTP alone does not prove legal identity or representative authority.

Sequential signers review the current revision. Parallel approvals bind to one frozen base and each person's field values; deterministic composition records exactly how approved inputs became the final PDF. Personal certificate signatures need their own incremental signing order. Changes to approved inputs require renewed approval.

## openCryptograph

Consume immutable approved inputs, compose visible fields, sign the resulting PDF, verify it independently and archive exact bytes plus evidence. A cryptographic signature makes changes detectable; it does not make a PDF impossible to edit. Report protected revision coverage and classify later changes rather than accepting any old valid signature as proof of the whole current file.

Distinguish organizational seal from personal certificate signature. The organizational certificate identifies the sealing organization; human identity and authority come from the approval evidence. Do not claim a corporate certificate is Brett's personal certificate.

Compare Apache PDFBox (Apache-2.0) and node-signpdf/@signpdf (MIT) first. Evaluate LibPDF and pyHanko as alternatives, subject to current maturity, verification capabilities and full dependency licensing. No engine is selected. Define desired PAdES profile before claiming conformance; qualify with independent readers/verifiers. Do not implement cryptographic primitives from scratch.

## Keys and trust

Use isolated workers, scoped identity and a qualified Key Vault/HSM adapter, preferably with non-exportable keys. Prove remote signing algorithms and certificate/key correspondence. No private keys in Hermes, browsers, prompts, source or logs. Keep signing keys separate from blockchain wallets and AI credential vaults. Rotation must preserve historical certificate/evidence material.

Select certificate issuer, timestamp authority, revocation policy and long-term-validation scope explicitly. Self-signed certificates do not automatically receive public-reader trust. Granular verification results include integrity, certificate trust, revision coverage, timestamp and identity-evidence status. Failure to verify prevents completion.

## Alternatives and open questions

Choose sequential versus parallel v1 scope, authentication assurance, accessibility/localization, certificate provider, engine and timestamp policy. No automatic legal notarization, clinical attestation or universal validity is claimed.

## Relationships

[Trust synthesis](opensig-synthesis-trust.md); [anchoring](opensig-anchoring.md).

## Primary technical references

- https://pdfbox.apache.org/
- https://github.com/vbuch/node-signpdf
- https://github.com/LibPDF-js/core
- https://github.com/MatthiasValvekens/pyHanko
