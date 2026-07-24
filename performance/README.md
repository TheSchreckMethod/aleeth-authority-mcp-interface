# Measured performance, floor included

Latency numbers for the governed call path, published with their run conditions. Two modes,
never conflated. Both artifacts in this directory are the unedited JSON output of the
benchmark harness; every number below traces to them.

**Read this first: the server enforces a decision-timing floor.** Every governed call pays a
minimum decision time (120 ms in these runs, the production default) on allow and deny alike,
so response timing cannot be used as an oracle against the policy engine. This is a security
control, not overhead to be optimized away. A benchmark that hid it would be a fake number,
so it is disclosed in every artifact.

## LIVE: production, end to end

One hundred timed `tools/call` requests (tool `list_frameworks`) against the production
deployment at `https://mcp.aleeth.com/mcp`, over the public internet from a developer
workstation, OAuth bearer auth, concurrency 4, 10 warmup requests excluded. Every request
succeeded and every one of the 100 governed calls sealed a signed receipt on the public
ledger. Run: 2026-07-24T17:27:30Z, server version 1.1.0.

| p50 | p95 | p99 | min | max | ok | errors |
|---|---|---|---|---|---|---|
| 1076 ms | 1310 ms | 1365 ms | 933 ms | 1392 ms | 100/100 | none |

That end-to-end time includes: TLS and public network from the client, bearer-token
verification, the Rail Guard authorization round trip to the live control plane, the enforced
decision floor, the real upstream data fetch, and the Ed25519 receipt seal to the
hash-chained ledger. Artifact: [`2026-07-24-live.json`](2026-07-24-live.json).

## LOCAL: the server's own overhead, isolated

The real server assembled by the production factory, served on loopback, with the ICA control
plane replaced by an in-process stub whose latencies are injected and disclosed: 25 ms
authorize, 40 ms receipt, 30 ms data fetch. No auth, loopback only, stub plane. These are NOT
production numbers; they isolate what the server itself adds around the plane. 200 requests,
concurrency 8, 20 warmup excluded. Run: 2026-07-24T17:26:44Z, commit `b01f494`.

| p50 | p95 | p99 | ok | errors |
|---|---|---|---|---|
| 201 ms | 205 ms | 214 ms | 200/200 | none |

With 95 ms of injected plane latency and the 120 ms floor over the decision phase, an
end-to-end p50 of 201 ms means the server's own framework overhead is roughly 10 ms per
governed call at 8-way concurrency. The artifact includes the server-side phase split
(rail guard, tool, receipt) delta-read from the server's own Prometheus histograms:
[`2026-07-24-local.json`](2026-07-24-local.json).

## Reproduce it

The harness lives in the private implementation repository, but the live measurement needs
nothing from it: time JSON-RPC `tools/call` requests over MCP streamable HTTP with any HTTP
client, using read-only credentials (available on request: security@aleeth.com). Then take
any `receipt_hash` from a response and verify it at the public ledger endpoint listed in the
top-level [README](../README.md). The receipts are the proof that the timed calls were real
governed calls, not a stripped-down fast path.

## What these numbers do not claim

No load-test ceiling, no throughput claim, no comparison to ungoverned servers. One tool was
measured on one day from one client location; other tools differ with their upstream calls.
When we re-measure, the new artifact lands next to these with its own date, whichever way
the numbers move.
