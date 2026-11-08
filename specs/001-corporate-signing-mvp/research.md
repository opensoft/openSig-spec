# MVP Design Decisions and Qualification Record

Status: decisions recorded 2026-11-08 by architect amendments A-001 (runtime) and A-002 (engine). License facts below were measured with `npm install --ignore-scripts` in a scratch directory on that date. No package is qualified for release by this document: T002 builds the inventory gate and T027 runs it against the real lockfile.

## Decisions

| Decision | Rationale | Alternatives and disposition |
|---|---|---|
| Standalone TypeScript service on **Node 24.22.2** (A-001; see plan.md) | One runtime for API, worker, browser build and tests. Node 22 reaches end of life on 2027-04-30 and its `node:sqlite` is experimental. | Node 22.23.4: verified but rejected. Host Node 24.21.0: predates the 2026-10-20 security release, never pinned. Frappe core: deferred. |
| **Original Apache-2.0 PDF/CMS engine on `node:crypto` and `node:zlib`** (A-002) | It is the only measured path that ships no third-party package. A Node-only path also adds no JVM platform to inventory. | See § Engine decision. pdf-lib/@signpdf, @libpdf/core and pdfjs-dist were rejected with evidence; PDFBox/JVM was not pursued. |
| **Product verifier is a separate module** (`src/verifier/`) with an enforced import boundary | FR-005 runs verification before completion and serves it from `/verification`, so a verifier ships. It must not share code with the signer. | A test-only verifier cannot satisfy FR-005. A verifier sharing `src/cryptograph/` or `src/pdf/` was rejected (F23, R2). |
| **Independent test oracle**: OpenSSL CLI plus a separately written structure checker (`tests/integrity/oracle/`) | Ground truth comes from a different code base, and the fixture corpus is produced by a different tool (`openssl cms -sign`). Both polarities are tested. | pyHanko: a Python platform, not provisioned. An external PDF reader such as Acrobat stays an OPEN release gate (`external-pdf-reader`). |
| **Review renderer: the browser's built-in PDF viewer, plus an original page-outline schematic and a download link of the exact frozen bytes** | Ships no third-party bytes. The approval binds to the SHA-256 of the exact bytes served. | pdfjs-dist 6.5.438 as a shipped renderer: deferred (§ Engine decision). signature_pad: not needed, because capture is original pointer-to-vector-stroke code. |
| **`node:sqlite` built-in** plus an immutable, tenant-namespaced blob adapter for the one-instance pilot | No npm driver and no native addon. Transactional workflow and outbox with explicit recovery semantics. | better-sqlite3 (not measured; it bundles a native build chain): not needed. An external database or object store is later scaling work. SQLite inside the Node binary is a platform component, inventoried separately under the owner decision of 2026-11-08. |
| **Operator OIDC with original JWT/JWKS validation**: pinned algorithms, JWKS from configuration only, exact issuer and audience, bounded clock skew. API bearer tokens only; **no operator browser session in MVP** | Separates service authority from human mailbox-control approval. With no operator cookie, there is no operator CSRF surface (R18). | `jose`/`openid-client`: not measured and not needed. An operator UI is deferred. |
| **Recipient invitation, then mailbox challenge, then bound session** | Mailbox control is recorded as assurance; it is not legal identity. | Stronger identity proofing comes later. |
| **One organization seal after sequential approvals** | Delivers a visible autograph plus protected output without personal certificate enrollment. | Parallel approval and per-person PKI deferred. |
| **Typed signatures and dates use standard-14 Helvetica, not embedded** | No font file ships (R20). Characters are limited to WinAnsiEncoding; anything else gets a 422 with code `unsupported_characters`. | Embedding a script font: rejected, because common script fonts are OFL, which is not on the allowlist. |
| **Disabled or fake anchoring only** | A stable future boundary with no wallets, fees or chain decisions. | A live ledger adapter is a later governed feature. |
| **Isolated local Hermes for SC-006** | Uses the production image digest, run with `--network none`, an ephemeral config and a scripted OpenAI-compatible endpoint (hermes.md). Production Hermes v0.23.0 is never touched. | A real-model run through a host-side adapter is a separately labeled, capability-gated supplement (T041). |

## Engine decision (A-002)

Requirements that hold regardless of engine:

- The signer and the shipped verifier are separate modules. The verifier imports only `node:*` and `src/verifier/**`, and an import-graph test enforces this.
- The oracle imports nothing from `src/`, and it is written in a different writer context from T013, T036, T037 and T014.
- Trusted and untrusted polarity are both tested.
- `revocation_status` is `not-checked` and `timestamp_status` is `absent` while those checks are not implemented.

| Candidate | Measured evidence (2026-11-08) | Disposition |
|---|---|---|
| pdf-lib 1.17.1 + @signpdf/* | `pdf-lib@1.17.1` pulls `tslib@1.14.1 "0BSD"` and `pako@1.0.11 "(MIT AND Zlib)"`. `@signpdf/signer-p12` has a peer dependency on `node-forge` "(BSD-3-Clause OR GPL-2.0)". | **Rejected.** Disallowed transitive licenses; no waiver. |
| @libpdf/core 0.5.3 (LibPDF-js/core, declares MIT) | Its lockfile: `pako 2.2.0 "(MIT AND Zlib)"`, `pkijs 3.4.3 "BSD-3-Clause"`, `asn1js 3.1.0 "BSD-3-Clause"`, `bytestreamjs 2.0.1 "BSD-3-Clause"`, `lru-cache 11.5.3 "BlueOak-1.0.0"`, `tslib 2.9.1 "0BSD"`. The other packages are MIT (`@noble/*`, `@scure/base`, `pvtsutils`, `pvutils`). It also has peer dependencies on `@google-cloud/kms` and `@google-cloud/secret-manager`. | **Rejected** on direct-dependency licenses alone. CMS goes through pkijs/asn1js (BSD-3-Clause). Its bundled assets and verifier independence were not examined further, since nothing would change the result. |
| pdfjs-dist 6.5.438 (Apache-2.0) as the shipped review renderer | Separately licensed assets: `standard_fonts/LICENSE_FOXIT` (PDFium BSD-style), `standard_fonts/LICENSE_LIBERATION` (Liberation font agreement), `cmaps/LICENSE` (Adobe), `wasm/LICENSE_OPENJPEG` (BSD-2-Clause), `wasm/LICENSE_JBIG2` (PDFium BSD-style), `wasm/LICENSE_QCMS` (MIT-style), `wasm/LICENSE_QUICKJS` (MIT-style), `iccs/LICENSE` (CC0). `build/pdf.worker.mjs` itself embeds glue modules (`qcms/qcms.js`, `qcms_bg.js`, `jbig2.js`, `jbig2_ccittFax.js`, `openjpeg/openjpeg.js`) whose per-file provenance is not provable as MIT/Apache from the package. | **Deferred.** It is not shipped in this feature. A follow-up could qualify a core build with the external modules removed and provenance proven. |
| PDFBox worker on a JVM | Not measured. It would add a mandatory JVM runtime: a full docs proposal (Triad Feature Amendments ¶2) plus the pending platform question. | **Not pursued**, because a Node-only eligible path exists. |
| **Original Apache-2.0 code on `node:crypto`/`node:zlib`** | No third-party application dependency, so the shipped npm closure is empty. `node:crypto` provides signing, `X509Certificate` parsing and verification. `node:zlib` provides inflate with `maxOutputLength` and `crc32`. | **Chosen.** The work splits into tasks T033, T013, T036, T037, T014 and T039. Main risk: parser robustness. Mitigation: the isolated parse service (T033) with hard limits and a supported-subset policy. |

### Shape of the chosen engine

- **`src/pdf/`.** A bounded hostile-input parser.
  - Handles: classic xref tables and xref streams; object streams; the page tree with inherited `/Rotate` and `/MediaBox`; detection of encryption and existing signatures; detection of JBIG2, JPX and JavaScript.
  - Limits: nesting depth, object count, a decompression ratio and a total inflated-byte cap.
  - Runs only inside the isolated parse service (T033). That is a child process with `--max-old-space-size`, a CPU and wall-time limit, and the Node permission model with no child-process, worker or fs-write grants. Network isolation comes from launching the worker in a user and network namespace (util-linux `unshare`) when available. Without it, production fails closed, and development runs unsandboxed and reports `network_sandbox: unavailable`. An import-boundary test ensures the parse code does not import `net`, `http`, `https`, `dns`, `tls` or `dgram`; that is defence in depth, not network isolation.
  - Upload validation (T007/T034) and composition (T036) both call this service.
- **`src/cryptograph/compose/`.** An incremental-update writer. It appends appearance streams: drawn signatures become vector path operators from stroke points, and typed text and dates use Helvetica with WinAnsiEncoding, not embedded. It also appends an AcroForm signature field, a `/Sig` dictionary (`/Filter /Adobe.PPKLite`, `/SubFilter /ETSI.CAdES.detached`, `/M` labeled unsigned server time), a fixed-size `/ByteRange` placeholder and a fixed-size `/Contents` placeholder.
- **`src/cryptograph/cms/`.**
  - **Encoding.** A DER encoder and a detached CMS SignedData builder. The signed attributes are contentType, messageDigest and signingCertificateV2; there is no signingTime attribute.
  - **Signing.** RSA-3072 with PKCS#1 v1.5 and SHA-256, via `crypto.sign`.
  - **Key adapter.** The file-backed development key adapter reads the private key from a 0600 file path or an inherited file descriptor. It never reads it from env or argv.
- **`src/cryptograph/worker/`.** An isolated sealing worker process. It owns key access, and its child environment carries no secrets.
- **`src/verifier/`.** It has its own minimal ByteRange locator and DER decoder, and parses certificates with `crypto.X509Certificate`. It checks that the ByteRange covers the whole file except `/Contents`, that the signed revision is the last one, that messageDigest matches the ByteRange bytes, and that the signature over the re-encoded signedAttrs SET verifies. It evaluates trust against configured anchors, so a self-signed certificate is `untrusted`. Its fields are `integrity`, `trust`, `revision_coverage`, `timestamp_status` (`absent`) and `revocation_status` (`not-checked`), plus the exact-artifact digest.
- **Development certificates.** Generated at test runtime by the OpenSSL CLI, in a `mkdtemp` directory with mode 0600. The OpenSSL CLI is a non-shipped test tool, listed in the platform inventory.
- **ZIP.** An original deterministic store-method writer using `zlib.crc32` (T039).

### Dev and test tools (non-shipped)

| Tool | Measured | Role |
|---|---|---|
| typescript 6.0.3 | Apache-2.0, no dependencies. `ThirdPartyNoticeText.txt` lists DefinitelyTyped (MIT), Unicode, W3C and CC-BY-4.0 material in its `lib` files | Type check only. Emits no runtime helpers into any distributable |
| @types/node 24.19.2 + undici-types 7.24.6 | MIT, MIT | Types only |
| playwright (exact pin chosen in T027, matching the cached Chromium revision) | Not measured. T027 measures it, including playwright-core's third-party notices | Browser tests only. The Chromium build is a test-time platform item |
| OpenSSL CLI 3.0.13 (host) | Platform tool (OpenSSL 3 is Apache-2.0); listed in the platform inventory | Test oracle and dev certificates |
| hermes-agent v0.23.0 image `sha256:adb78909…f0fc002` | MIT according to the Hermes investigation; test tooling only | SC-006 isolated runtime |

The inventory (T002) classifies every npm package as `shipped` (the production dependency closure plus every asset copied into `dist/`) or `non-shipped` (devDependencies). The strict MIT/Apache-2.0 gate applies to `shipped` and must pass. `non-shipped` packages are inventoried with their full license data and reported. Platform items (Node runtime and embedded components, OS libraries, browser, CLI tools, Hermes image) are inventoried separately with status `INVENTORIED`. Under the owner decision of 2026-11-08 ("MIT/Apache application and libraries; inventory platform licenses separately"), they are not held to the allowlist and never affect the application gate.

## References and scope

Primary component references inspected 2026-11-08: https://github.com/mozilla/pdf.js, https://github.com/LibPDF-js/core, https://github.com/Hopding/pdf-lib, https://github.com/vbuch/node-signpdf, https://nodejs.org/dist/v24.22.2/.

Resolved scope defaults:
- a corporate synthetic pilot;
- at most two sequential recipients;
- drawn or typed signatures;
- a single PDF;
- no signature-image reuse;
- no real healthcare deployment;
- no live anchoring.

Operational inputs that must be validated before a real pilot: operator issuer, audience and tenant mapping; SMTP sender; retention configuration; and the production certificate and key provider. No credentials or provider choices are manufactured here.

No external capacity, certificate trust, PDF interoperability, legal validity or live integration has been demonstrated. Those are measurable implementation gates in tasks.md and quickstart.md.
