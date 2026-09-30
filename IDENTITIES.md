# Canonical Identities

This file defines domain identities. A database table, read model, UI tab or document does not become an identity merely because it exists.

## 1. `tool_id` — canonical Tool

- Identifies one canonical physical Tool registered in Ferramentas.
- Applies to CM, MF and BQ.
- Consumer modules reference the Tool; they do not create replacement Tool identities.
- A different lot is a different canonical Tool and therefore a different `tool_id`.
- A lot is not mutable state on an existing Tool: new lot means new Tool and new `tool_id`.

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

## 7. `bq_repair_trace_id` — Boquilhas repair process

- Identifies one concrete Boquilhas repair process / movement trace.
- It exists so movements belonging to one repair process are grouped under their own durable identity instead of being attached as one flat lifetime movement list directly to `bq_id`.
- Before a production association exists, a repair trace may be anchored to the canonical BQ `tool_id`.
- When the production context becomes known, the same `bq_repair_trace_id` is explicitly associated with the applicable `bq_id`.
- Associating the trace to `bq_id` does not create a replacement trace, move its existing movements, or reset its discrepancy/history.
- A trace may continue to receive movements after the machine has moved to another production; late returns remain on the trace where that repair process originated.
- `bq_id` remains the BQ Tool-in-production context. It is not the identity of a repair process and must not become the direct parent for the complete Boquilhas movement history.
- `movement_id` identifies an individual event inside a repair trace.

The existing `boquilhas_id`-based implementation is a valid implementation base and its existing records must be preserved. Introducing the canonical repair-trace identity does not by itself require a destructive rename or loss of existing register data. Implementation must reconcile the existing register persistence with the repair-trace lifecycle while preserving the real operational history.

The unresolved cardinality question for multiple simultaneous pre-production traces of the same `tool_id` is tracked separately in `OPEN_DECISIONS.md`.

## 8. `movement_id` — Boquilhas movement

- Identifies one Boquilhas quantity movement/event.
- Movement type is exactly one of `saida`, `entrada`, or `entrada_sem_reparacao`.
- Each movement belongs to one `bq_repair_trace_id`.
- Movement identity is distinct from `bq_repair_trace_id`, `bq_id`, `tool_id`, the Boquilhas register identity, and any audit-entry identity.

## Structures and names that deliberately do not create canonical identities

### `resumo_id`

`resumo_id` does not exist.

Resumo is a read composition / derived dashboard-document surface inside Controlo. It is not a persisted parent, FK anchor or independent canonical identity.

### `tp_id` / `tampao_id`

TP/Tampão has no independent operational identity in the current process.

DMO preserves the applicable production value in the Job On context. It must not invent a `tp_id` or `tampao_id` merely because Peso reads that value.

### `tool_technical_values`

`tool_technical_values` is an optional 1:1 extension of a Tool and uses `tool_id` as its PK/FK.

There is no separate `technical_values_id`.

The normal Tool registry/search path stays light; specialized technical values are loaded only when a consuming workflow asks for them.
