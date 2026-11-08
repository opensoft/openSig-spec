# Security and Release Readiness Checklist

Feature: [spec.md](../spec.md). All items below remain unexecuted implementation/release gates.

- [ ] Complete SBOM/notice/allowlist qualification including assets, native/runtime and image contents.
- [ ] Prove sender/recipient/service authority, CSRF, TTL/attempt limits and tenant isolation.
- [ ] Prove exact-version consent, sequential approval and no signature from link viewing.
- [ ] Qualify supported PDF corpus and parser isolation/resource limits.
- [ ] Prove independent seal verification, tampering detection and honest trust/revocation/timestamp status.
- [ ] Prove idempotency, durable evidence, crash/race recovery and backup/restore.
- [ ] Prove replay-safe callbacks, SSRF controls, redaction and version-bound consumer attachment.
- [ ] Demonstrate actual Hermes tool round trip; distinguish synthetic healthcare harnesses from real app integration.
- [ ] Prove disabled/fake anchoring creates no wallet/network transaction and cannot block completion.
- [ ] Complete accessibility, timing and ten-flow acceptance with source-bound receipts.
- [ ] Resolve and authorize real pilot identity/SMTP, managed keys, trust, retention and environment.
- [ ] Land reviewed spec/code and matching assembly pins before archive.
