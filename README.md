# ALEETH Authority MCP: public interface and provenance

This repository is the public interface definition, version history, and provenance record for
the remote MCP server **`com.aleeth/authority-mcp`**, hosted at `https://mcp.aleeth.com/mcp`.

The implementation is private by decision. This repository publishes everything a caller,
auditor, or registry can verify without source access: the registry manifest, the complete tool
manifest, the version history, and the security policy.

## What the server is

The ALEETH Authority MCP is the first MCP server governed by ICA (Institutional Control
Architecture). Every tool call is risk-checked before execution and sealed with a signed
Ed25519 receipt on a hash-chained ledger anchored to Bitcoin via OpenTimestamps. The server
fails closed: if governance cannot run or the receipt cannot be written, the call does not
return a result.

- 37 governed tools (see [`tools/manifest.json`](tools/manifest.json))
- Transport: MCP streamable HTTP, TLS only
- Authentication: OAuth 2.0 bearer tokens; discovery per RFC 9728 at
  `https://mcp.aleeth.com/.well-known/oauth-protected-resource`
- Anonymous calls are refused with 401

## What is in this repository

| File | What it is | How it is produced |
|---|---|---|
| [`server.json`](server.json) | The manifest published to the official MCP registry | Maintained by ALEETH, versioned here |
| [`tools/manifest.json`](tools/manifest.json) | All 37 tools: names, descriptions, input schemas | GENERATED from the live server by an export script; never hand-edited, so it cannot drift from what the server actually serves |
| [`CHANGELOG.md`](CHANGELOG.md) | Version history | Curated from the implementation repository's history |
| [`SECURITY.md`](SECURITY.md) | Disclosure contact and security posture | Maintained by ALEETH |

## Verify the running server yourself

None of the following requires permission from ALEETH or access to the source.

1. **Protocol and auth conformance (black-box):**
   `npx mcp-spec-check https://mcp.aleeth.com/mcp --bearer <token>`
   Read-only credentials are available on request: security@aleeth.com.
   Current independent grade: A (7 pass, 0 fail, 1 warn), last re-verified 2026-07-24.
2. **Anonymous access is refused:** `curl -s -o /dev/null -w "%{http_code}" -X POST https://mcp.aleeth.com/mcp`
   returns `401`, with authorization-server discovery served per RFC 9728.
3. **Receipts verify publicly:** any receipt hash from a governed call verifies at
   `https://imperium.aleeth.com/api/receipts/verify/<hash>`, and offline against the pinnable
   trust anchor at `https://imperium.aleeth.com/.well-known/ica-trust-anchor.json` (public keys,
   SHA-256 DER fingerprints, and an offline verification recipe: you verify with a key you hold,
   with zero calls to ALEETH).
4. **Measured latency, floor included:** see [`performance/`](performance/) for a live
   end-to-end run against production and a local server-overhead run, each published with its
   full run conditions and the enforced decision-timing floor disclosed.
5. **The full proof surface:** https://aleeth.com/proof

## Why the source is private

Publication is not a precondition for verification. The checks that matter for a hosted remote
server probe the running service: protocol conformance, authentication behavior, error
contracts, and the receipt ledger. A static scan of this repository can verify the interface
and the provenance; runtime governance claims are backed by the signed, Bitcoin-anchored
receipt chain, which is public.

Background reading: "Stateless Is Not Governed",
https://publications.aleeth.com/stateless-is-not-governed/

## Contact

- Security disclosures: security@aleeth.com (see [`SECURITY.md`](SECURITY.md))
- Read-only evaluation credentials: security@aleeth.com

ALEETH is a brand of The Schreck Group, Inc. AI has met its match. Truth.™
