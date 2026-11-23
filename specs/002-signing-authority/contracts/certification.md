# Signing Authority and Verification Contract

## Request and authorization

- An envelope declares **Autographed** or **Fully certified**. A legacy
  envelope without the field behaves as autograph-only. The mode is visible
  to the sender and recipient and is bound to the signing revision.
- A certified request uses verified standing authority or a verified
  resolution from the competent body when one is required. Record decision
  scope, limits and required parties. A missing or adverse registry result,
  disputed governing record, or unresolved authority keeps the request
  pending.
- Verify the recipient's identity and authority using the human ceremony
  when they sign. An OpenSig operator session, a Teams token, an import,
  a service credential or an approval to send grants no recipient approval.
- Require a fresh request and fresh consent for a mode change or substantive
  revision after send. Preserve prior evidence and retire earlier invitation
  access. An unresolved certified request cannot complete by implicit
  downgrade.

## Completion and verification

| Situation | Recipient-visible result |
| --- | --- |
| Ordinary signing completed | **Autographed**. Use the existing seal verification rules and show no signing authority badge. |
| Certified request still pending | Show signing progress to authorized participants. The sender can see a safe missing-check reason. No completed certified document, final certification package or badge is issued. |
| Every required signature qualifies | Publish the exact completed revision and its versioned certification evidence together. Show the blue checkmark and readable **Signing authority verified** text only after verifying that binding. |
| Artifact or evidence altered or binding cannot be verified | Report failed certification verification and suppress the authority badge on the affected result. |

Keep PDF integrity, certificate trust and signing authority as distinct
verification results. Establish the evidence binding inside the existing
fenced completion flow so a stale finalization attempt cannot publish a
badge. Use a new versioned certification representation, with a recipient-safe
summary and private evidence references. Existing autograph package readers and
ordinary sealing behavior retain compatibility.
