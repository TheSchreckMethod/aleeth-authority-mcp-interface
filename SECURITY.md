# Security policy

## Reporting

Report suspected vulnerabilities in the ALEETH Authority MCP service or this repository to
**security@aleeth.com**. We acknowledge reports and we do not pursue good-faith researchers.
No bounty program is currently offered; we say so rather than imply one.

## Scope

- The hosted service at `https://mcp.aleeth.com/mcp` and its well-known discovery endpoints.
- The published interface artifacts in this repository.

Protocol-conformance probing with read-only credentials is welcome (that is what the
credentials are for). Please do not run load tests or destructive testing against production.

## Authentication model

- OAuth 2.0 bearer tokens only. The authorization server is discovered per RFC 9728 at
  `https://mcp.aleeth.com/.well-known/oauth-protected-resource`.
- Anonymous requests receive 401. There are no unauthenticated tool paths.
- Credentials are scope-gated; read-only evaluation credentials cannot execute governed writes.

## Posture, stated plainly

- Fail closed: if governance cannot evaluate a call, or the receipt cannot be written, the call
  does not return a result.
- Every tool call is sealed with a signed Ed25519 receipt on a hash-chained ledger anchored to
  Bitcoin via OpenTimestamps. Receipts verify publicly at
  `https://imperium.aleeth.com/api/receipts/verify/<hash>` and offline via the trust anchor at
  `https://imperium.aleeth.com/.well-known/ica-trust-anchor.json`.
- Policy denials are uniform and carry stable error codes, by design.
- The implementation source is private; verification of the running service does not depend
  on it (see README, "Verify the running server yourself").
