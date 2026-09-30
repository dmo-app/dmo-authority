# Open Decisions

This file contains unresolved authority questions only.

An item here is not canonical until the owner decides it and the resulting durable rule is promoted into the appropriate authority file.

`bq_repair_trace_id` itself is not an open decision.

Canonical Boquilhas structure is:

```text
jobon_id
→ bq_id
→ one bq_repair_trace_id
→ many movement_id
```

The trace belongs to the BQ context of one production, not to each individual repair cycle. A pre-production trace may exist temporarily from `tool_id` with `bq_id = null`, then retain the same identity when associated to the later production context.

`resumo_id` and `controlo_id` are not open decisions:
- `resumo_id` does not exist. Resumo is a read composition / derived document surface.
- `controlo_id` exists and is the persistent identity of a Controlo context in one production.

## OD-001 — Multiple pending Boquilhas traces before production association

**Question:** Before production association, may the same canonical BQ `tool_id` have more than one pending `bq_repair_trace_id` with `bq_id = null` at the same time?

**Current canonical behavior:**
- a pending trace is anchored to the canonical BQ `tool_id`;
- `bq_id` is a distinct production-context identity that references that same `tool_id`;
- when Job On creates the corresponding `bq_id`, a single unambiguous pending trace for that `tool_id` is associated automatically;
- the same `bq_repair_trace_id` and all existing movements are preserved;
- if no pending trace exists, the production uses its own new production trace;
- late movements continue to belong to their original trace.

**Still open:**
- whether several simultaneous pending traces may exist for one `tool_id`;
- if they may, which additional fact disambiguates the correct future production association.

Until that cardinality is decided, automatic association is only canonical when the pending match is unambiguous.
