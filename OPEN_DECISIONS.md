# Open Decisions

This file contains unresolved authority questions only.

An item here is not canonical until the owner decides it and the resulting durable rule is promoted into the appropriate authority file.

`resumo_id` and `controlo_id` are not open decisions:
- `resumo_id` does not exist. Resumo is a read composition / derived document surface.
- `controlo_id` exists and is the persistent identity of a Controlo context in one production.

## OD-001 — Multiple pending Boquilhas repair traces before production association

**Question:** Before a repair trace is associated to a production `bq_id`, may the same canonical BQ `tool_id` have multiple pending `bq_repair_trace_id` traces, or at most one?

**Current safe authority:**
- A Boquilhas repair trace may begin before Job On.
- Before production association it is anchored to the canonical BQ `tool_id`.
- Association to `bq_id` is an explicit human action.
- The same `bq_repair_trace_id` survives that association.
- The trace may continue after the production.

**Not decided:**
- Cardinality of simultaneous pending traces for the same `tool_id` before association.

Do not infer this rule until the owner closes it.
