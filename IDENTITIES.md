# Canonical Identities

This file defines domain identities. A database table, read model, UI tab or document does not become an identity merely because it exists.

## 1. `tool_id` — canonical Tool

- Identifies one canonical physical Tool registered in Ferramentas.
- Applies to CM, MF and BQ.
- Consumer modules reference the Tool; they do not create replacement Tool identities.
- A different lot is a different canonical Tool and therefore a different `tool_id`.

## 2. `jobon_id` — production occurrence

- Identifies one concrete production occurrence.
- Job On owns this identity.
- It is not replaced by a generic `production_id`.
- Downstream workflows use the exact production/context identities they actually need instead of inventing a second production identity.

## 3. `cm_id`, `mf_id`, `bq_id` — Tool-in-production contexts

- Identify the CM, MF or BQ context used in one `jobon_id`.
- They reference a canonical `tool_id` but are not Tools themselves.
- They preserve the production-context snapshot required for historical truth.
- Clients never mint these identities.

## 4. `controlo_id` — Controlo context in a production

- Identifies the persistent Controlo context for one production.
- It is the truthful home for facts that belong to Controlo as a production context rather than to Job On, a Tool, or one specific Controlo function.
- Confirmed example: the applicable tampão/calote value for that production.
- It does not replace `jobon_id`, `cm_id`, `mf_id`, `bq_id`, `peso_id` or `comparacao_id`.
- It is not a generic god-parent for every Controlo record.
- UI grouping under Controlo does not imply persistence ownership under `controlo_id`.

The exact technical representation may be designed during implementation, but the functional identity itself is canonical.

## 5. `peso_id` — Peso record

- Identifies one specific Peso control/result record.
- It persists through create, submit, approval, rejection and reopen lifecycle.
- There is no approval copy.
- It is not a production identity, Tool identity, Job On identity, CM identity or revision.
- The normal production path anchors Peso through `cm_id`.

## 6. `comparacao_id` — Peso Comparação event

- Identifies one Comparação event started for an existing `peso_id`.
- A Peso without a Comparação is valid.
- Multiple Comparação events may exist for the same Peso.
- Comparação reuses existing `cm_id`; it does not create a new CM.
- It does not alter the original Peso and does not create `previous_peso_id`.

## 7. `bq_repair_trace_id` — Boquilhas repair trace

- Identifies one durable Boquilhas repair trace.
- A trace may begin before Job On and is initially anchored to the canonical BQ `tool_id`.
- Later association to the production `bq_id` is an explicit human action.
- The same `bq_repair_trace_id` survives that association.
- The trace may continue after the production.
- It is not a start/stop/close/reopen state machine.

The allowed cardinality of simultaneous pending traces for the same `tool_id` before association remains an open decision.

## 8. `movement_id` — Boquilhas movement

- Identifies one Boquilhas quantity movement/event.
- Movement type is exactly one of `saida`, `entrada`, or `entrada_sem_reparacao`.
- Movement identity is distinct from `bq_repair_trace_id`, `bq_id`, `tool_id`, and any audit-entry identity.

## Structures and names that deliberately do not create canonical identities

### `resumo_id`

`resumo_id` does not exist.

Resumo is a read composition / derived dashboard-document surface inside Controlo. It is not a persisted parent, FK anchor or independent canonical identity.

### `boquilhas_id`

`boquilhas_id` may exist as implementation naming, but it is not a competing product identity. Canonical Boquilhas identities are `tool_id`, `bq_id`, `bq_repair_trace_id` and `movement_id`.

### `tool_technical_values`

`tool_technical_values` is an optional 1:1 extension of a Tool and uses `tool_id` as its PK/FK.

There is no separate `technical_values_id`.

The normal Tool registry/search path stays light; specialized technical values are loaded only when a consuming workflow asks for them.
