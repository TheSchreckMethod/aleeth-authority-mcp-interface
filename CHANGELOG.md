# Changelog: ALEETH Authority MCP

Registry entry: `com.aleeth/authority-mcp`. Dates are UTC.

## 1.0.0 (2026-07-24)

- Registry entry republished with `websiteUrl` and `repository` provenance; this public
  interface repository is published alongside it.
- Tool manifest (36 tools) exported from the live server and committed at
  `tools/manifest.json`.
- Versioning note, for honesty: the registry entry line is normalized at 1.0.0 as of this
  date. The server internally reports a 2.0 beta build string for its stateless architecture
  line; the registry version and the internal build string are different identifiers. The
  manifest records the server's self-reported version verbatim.

## Unreleased registry versions (server evolution between 0.1.0 and 1.0.0)

- 2026-07-23: hardening for stateless hosted workers: durable receipt spool with reconciler,
  bounded timeouts and operational metrics, OTLP tracing, hash-pinned policy manifest with
  per-tool sensitivity, uniform policy denials with stable error codes (ICA-DENIED-POLICY,
  ICA-DENIED-SCOPE), provenance canaries riding to the ledger, a decision-timing floor applied
  to both allow and deny, an integer-overflow guard on the receipt step sequence, and a pinned
  dependency set.
- 2026-07-22: server state externalized for stateless hosting; complete tool catalog.
- 2026-07-19: seven governed Academy tools added (four reads; three writes: equip, graduate,
  intake). Tool count 29 to 36.
- 2026-07-14: published to the official MCP registry as `com.aleeth/authority-mcp` version
  0.1.0.
- 2026-07-13: initial release. First MCP governed by ICA; migrated to the stateless MCP
  specification (2026-06-18 protocol line); tool arsenal grown from 7 to 29 across five
  batches; production host fixes.
