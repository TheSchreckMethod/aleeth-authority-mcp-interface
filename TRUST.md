# Governed, and checkable

The ALEETH Authority MCP is the first MCP server governed by ICA. Every tool
call is risk-checked by Rail Guard before it runs and sealed with a signed
receipt on a hash-chained public ledger after it runs. Denied or halted calls
never run. That is the claim. This page is how you check it without asking us
for permission.

Each row states a claim, the command or path that checks it, and how far the
check reaches. Some claims a reader can verify from the outside today. Some
require read-only credentials. Two are attested with evidence pointers and are
not externally checkable yet, and we say so rather than dress them up.

| Claim | Verify it yourself | Checkability |
|---|---|---|
| Anonymous access is refused, with standard discovery | `curl -s -o /dev/null -w "%{http_code}" -X POST https://mcp.aleeth.com/mcp` returns 401; `curl -sL https://mcp.aleeth.com/.well-known/oauth-protected-resource` returns the RFC 9728 document | External, now |
| Protocol and auth conformance, independently graded | `npx mcp-spec-check https://mcp.aleeth.com/mcp` with a bearer token. Independent grade as of 2026-07-24: A, seven pass, zero fail, one warn | With read-only credentials |
| Every governed call produces a receipt on a public ledger | Take the `receipt_hash` from any tool response and verify it at the public verify endpoint listed in the README | External, now |
| Receipts chain and anchor to Bitcoin | The offline trust anchor at `/.well-known/ica-trust-anchor.json`, verifiable with a key the reader holds, zero calls to ALEETH | External, now |
| Denials are uniform, with stable error codes | With read-only credentials, call any write tool. The refusal carries a stable code such as `ICA-DENIED-SCOPE`, not a stack trace | With read-only credentials |
| Probing is budgeted and charged | The enforcing policy is content-hash-pinned; the digest is published in the tool manifest. We do not invite load tests of production, so this claim ships with its policy pin rather than an exercise script | Attested, policy pin published |
| Fail closed: no receipt, no response | Attested with evidence pointers in the implementation's test suite and the certification scorecard. Not externally checkable by a reader today | Attested |
| Stateless: no session affinity, horizontal by design | Attested. The server implements the stateless streamable HTTP profile; statelessness itself is an architecture property a reader cannot probe from one seat | Attested |

## What this page does not claim

This server does not hold the ICA-Certified MCP mark. The standard is 28
criteria; the current self-scorecard is 21 pass, 4 partial, 2 delegated, 1 not
applicable, 0 fail, and the mark is claimed at 28 of 28 only. We publish the
scorecard numbers rather than round them up.

No SLSA level is claimed. See `supply-chain/` for what is proven about
dependencies and what is not yet.

No third-party grade is claimed beyond what the graders have published. When a
grader rescans, we report the result verbatim, whichever way it goes.
