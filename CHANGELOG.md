# Changelog: ALEETH Authority MCP

Registry entry: `com.aleeth/authority-mcp`. Dates are UTC.

## 1.1.0 (2026-09-02 live re-export)

- Production catalog re-exported from `https://mcp.aleeth.com/mcp` after Railway
  SUCCESS on `db85935` (Payments Assurance Fabric hardening). `tool_count` is 41.
  New control: `execute_governed_payment` (scope `payment:execute`, irreversible,
  default charge weight 8). Source remains `"production"`. Product version is
  still 1.1.0; the digest of the built-in policy did not move.

## 1.1.0 (2026-07-24, deployed and live)

- Wire identity fixed: the server now reports the product version (1.1.0) in
  `serverInfo` and the discovery documents, closing the 1.0.0 note below about the
  internal 2.0 beta build string leaking through. One source of truth, pinned by tests.
- Tool 37: `get_atc_overview` reads engine health, sensor coverage, recent incidents,
  and recent raw signals concurrently in one governed call (one Rail Guard authorize,
  one receipt). A failed section is named in the response and in the sealed receipt
  summary, never silently dropped.
- Every tool registration now carries protocol `annotations` (read-only, destructive,
  open-world hints; idempotency is never claimed because it is not verified) and an
  `aleeth/governance` metadata block (rail-guarded, receipted, reversibility, scope,
  cost class, default charge weight). `tools/list` carries a public five-minute
  freshness hint so clients can skip re-introspection.
- New governed resource `ica://ops/tool-stats`: live per-tool latency quantiles
  (bucket-derived; null over fabricated), outcome counts, dependency health, and the
  cost catalog. The read is charged, authorized, and receipted like every other, and
  aggregates per tool only, never per caller.
- Per-tool latency histograms and outcome counters on the metrics endpoint. Rail Guard
  decision timing is exported post-floor only, so the metrics surface carries no raw
  verdict timing.
- Bounded retries with full jitter and a retry budget, scoped by each upstream's
  side-effect contract: the Rail Guard authorize retries only requests that provably
  never reached the server; receipt issuance retries under its idempotency key; a
  denial is never retried. Retries can only turn an outage into a success, never a
  deny into an allow.
- Optional governed read cache (off by default): on a hit only the upstream fetch is
  elided; the call is still budget-charged, authorized, and receipted, and the receipt
  states `data_source: cache`. Only slow-moving catalog reads are cacheable; halt
  state, receipt verification, health, and every write are always computed live.
  Values are sealed at rest; tenant is a key dimension.
- Container build metadata corrected: an interim commit labeled an echoed JSON file
  "SLSA Level 3" attestation and also broke the image build; both are reverted. The
  image now records honest build traceability (git sha), and real SLSA provenance is
  named as future work rather than claimed.
- Configuration matrix expanded (timeouts, token cache, spool, JWKS/leeway with a
  clamp, retry and cache knobs), each failing toward its default on a bad value.
- The committed `tools/manifest.json` for this version is exported from the live
  production server after the gated deploy and marked `"source": "production"`,
  reporting version 1.1.0.
- Measured latency published under `performance/`: a live end-to-end run against
  production and a local server-overhead run, each with its full run conditions
  and the enforced decision-timing floor disclosed.

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
