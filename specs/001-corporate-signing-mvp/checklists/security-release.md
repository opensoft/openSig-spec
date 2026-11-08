# Security and Release Readiness Checklist

Feature: [spec.md](../spec.md). All items below remain unexecuted implementation/release gates.

- [ ] Complete SBOM, notice and strict MIT/Apache allowlist qualification of application artifacts: distributed code, libraries, bundled assets and transitive application dependencies. Record the separate platform inventory: Node runtime and embedded components, OS/base image, browser, JVM. Platforms are not held to the allowlist (owner decision 2026-11-08).
- [ ] Prove sender/recipient/service authority, CSRF, TTL/attempt limits and tenant isolation.
- [ ] Prove exact-version consent, sequential approval and no signature from link viewing.
- [ ] Qualify the supported PDF corpus and the parser's resource limits (memory, CPU, wall time, decompression, nesting, page count) and its import boundary (defence in depth only).
- [ ] Prove OS-level network isolation of the PDF parse worker in the deployment environment: the user/network namespace sandbox is active there (gate `parser-network-sandbox`). Local evidence records only the capability observed (`netns-enforced (local)` or `unavailable`) and does not close this item.
- [ ] Prove independent seal verification, tampering detection and honest trust/revocation/timestamp status.
- [ ] Prove idempotency, durable evidence, crash/race recovery and backup/restore.
- [ ] Prove replay-safe callbacks, SSRF controls, redaction and version-bound consumer attachment.
- [ ] Demonstrate actual Hermes tool round trip; distinguish synthetic healthcare harnesses from real app integration.
- [ ] Prove disabled/fake anchoring creates no wallet/network transaction and cannot block completion.
- [ ] Complete accessibility, timing and ten-flow acceptance with source-bound receipts.
- [ ] Resolve and authorize real pilot identity/SMTP, managed keys, trust, retention and environment.
- [ ] Land reviewed spec/code and matching assembly pins before archive.
