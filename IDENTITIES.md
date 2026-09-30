# Canonical Identities

This file defines domain identities. A database table, read model, UI tab or document does not become an identity merely because it exists.

## 1. `tool_id` — canonical Tool

- Identifies one canonical physical Tool registered in Ferramentas.
- Applies to CM, MF and BQ.
- Consumer modules reference the Tool; they do not create replacement Tool identities.
- A different lot is a different canonical Tool and therefore a different `tool_id`.
- A lot is not mutable state on an existing Tool: new lot means new Tool and new `tool_id`.

## Physical Tool exclusivity

A canonical physical Tool cannot be treated as simultaneously in use in two different machine/production contexts at the same real production moment.

This is a physical-use invariant, not a Job On lifecycle status and not a rule about how many unresolved pre-production repair traces may exist.

## 2. `jobon_id` — production occurrence

- Identifies one concrete production occurrence.
- Job On owns this identity.
- It is not replaced by a generic `production_id`.
- Downstream workflows use the exact production/context identities they actually need instead of inventing a second production identity.

## 3. `cm_id`, `mf_id`, `bq_id` — Tool-in-production contexts

- Identify the CM, MF or BQ context used in one `jobon_id`.
- They reference a canonical `tool_id` but are not Tools themselves.
- They preserve the production-context snapshot required for historical truth.
- If the selected Tool changes inside the same `jobon_id`, the replacement receives a new `cm_id`, `mf_id` or `bq_id` as applicable and points to the new canonical `tool_id`.
- An existing component-context identity must never be retargeted from one canonical Tool to another.
- The previous context remains referencable by downstream records and history that already used it.
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

## 7. `bq_repair_trace_id` — Boquilhas trace for one production BQ context

- Identifies the durable Boquilhas movement trace for one BQ context in one production.
- Normal relationship: one `bq_id` has one `bq_repair_trace_id`, and that trace contains many `movement_id` events.
- The trace is **not** the identity of each individual repair trip/cycle. Several `saida` / `entrada` / `entrada_sem_reparacao` cycles may occur inside the same trace.
- `bq_id` remains the BQ Tool-in-production context. The movements belong to the trace, so `bq_id` does not become a direct undifferentiated lifetime container for Boquilhas movements.
- `tool_id` is the canonical Tool identity referenced by `bq_id` and is the stable identity used to correlate a pre-production repair trace with the later BQ production context.
- This correlation does not make `tool_id` and `bq_id` interchangeable. `tool_id` identifies the canonical Tool; `bq_id` identifies its use in one specific production context.
- A trace may therefore be created before Job On and initially reference only the canonical BQ `tool_id`, with `bq_id` unresolved.
- The same `tool_id` may have many repair traces across time because each production receives its own `bq_id` and trace.
- The allowed cardinality of simultaneous pre-production traces with `bq_id` unresolved for the same `tool_id` is **not yet decided**. This must not be inferred from physical Tool exclusivity or from an existing/provisional database index.
- There is no open/closed lifecycle for a trace. A pre-production trace is simply unresolved until it is associated to its production `bq_id`.
- When Job On later creates a `bq_id` for that same `tool_id`, automatic association is valid only when the intended pending trace is unambiguous under the product rules then in force.
- If no pre-production trace exists when the production context is created, that production uses its own new trace.
- If the trace starts after the production `bq_id` already exists, it is associated to that `bq_id` immediately.
- Association does not create a replacement trace, recreate movements, or reset trace history.
- A new production creates a new `bq_id` and therefore a new production trace, even when the canonical physical BQ `tool_id` is the same as in a previous production.
- Late returns remain movements of the original production trace where their repair activity originated; a later current production never steals or reassigns them.
- While a pre-production trace still has no `bq_id`, the UI exposes a persistent Job On association warning derived from the missing association rather than from a separate lifecycle state.

The existing `boquilhas_id`-based implementation is a valid implementation base and its real records/history must be preserved. The implementation must reconcile that persistence with this canonical trace relationship without destructive loss.

The implementation must not invent a cardinality rule for unresolved pre-production traces. If the current blueprint does not make the intended association unambiguous, that case remains blocked pending a product decision.

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
