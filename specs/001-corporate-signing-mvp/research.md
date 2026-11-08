# MVP Design Decisions and Qualification Record

Status: proposed implementation targets; no package or engine is qualified by this document.

| Decision | Rationale | Alternatives and disposition |
|---|---|---|
| Standalone TypeScript/Node 22 service | Shared browser/PDF integration with framework-neutral consumers and a small dependency surface | Frappe core deferred; Frappe consumer adapters remain possible |
| @signpdf/pdf-lib candidate | Focused MIT PDF composition/signing building blocks | PDFBox may be evaluated if this candidate fails; replacement needs recorded design revision |
| Independent verifier boundary | Signing success does not establish trust or full revision coverage | A fake verifier cannot satisfy US2 acceptance; choose implementation only after tamper/trust tests |
| SQLite plus immutable blob adapter for one-instance pilot | Simple transactional workflow/outbox with explicit recovery semantics | External database/object store is later scaling work; driver/native dependencies still gated |
| Operator OIDC plus recipient invitation/challenge | Separate service authority from human mailbox-control approval | No claims of legal identity from email; stronger identity proofing later |
| One seal after sequential approvals | Delivers visible autograph plus protected output without personal certificate enrollment | Parallel approval/per-person PKI deferred |
| Disabled/fake anchoring only | Stable future boundary with no wallets/fees or chain decisions | Live ledger adapter is a later governed feature |

Primary component references inspected 2026-11-08:

- https://github.com/vbuch/node-signpdf
- https://github.com/mozilla/pdf.js
- https://github.com/szimek/signature_pad
- https://github.com/Hopding/pdf-lib

Top-level declarations are candidates, not a full SBOM result. T002 must record exact versions, direct/transitive licenses, bundled fonts/assets, native dependencies and proposed container/runtime contents. The literal MIT/Apache constraint may exclude common operating-system or native components. Do not hide them or reinterpret the policy; fail qualification and seek an explicit design/policy decision if no eligible distribution exists. Development host use does not certify a release artifact.

Resolved scope defaults: corporate synthetic pilot, at most two sequential recipients, drawn/typed signature, single PDF, no signature-image reuse, no real healthcare deployment, no live anchoring. Operator issuer/audience/tenant mapping, SMTP sender, retention configuration and production certificate/key provider are operational inputs that must be validated before a real pilot. No credentials or provider choices are manufactured here.

No external capacity, certificate trust, PDF interoperability, legal validity or live integration has been demonstrated. Those are measurable implementation gates in tasks.md and quickstart.md.
