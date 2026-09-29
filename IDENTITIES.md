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

## 7. Boquilhas register / repair-trace identity

The existing Boquilhas implementation based on `boquilhas_id` is a valid implementation base and must not be treated as an error or replaced automatically.

The current product requirement is to preserve the working Boquilhas register and movement flow while adapting the movement balance/discrepancy behavior defined in `modules/boquilhas/MOVIMENTOS.md`.

`bq_repair_trace_id` is **not a required canonical identity at this time**.

It must not replace or complement `boquilhas_id` merely because it appeared in a later authority model. A separate repair-trace identity may only be introduced if a concrete product requirement or explicit owner decision demonstrates that the existing register identity cannot represent the required behavior.

Therefore:

- preserve the existing Boquilhas register model where it works;
- adapt the existing movement system for the required saldo/discrepancy behavior;
- do not perform an identity migration from `boquilhas_id` to `bq_repair_trace_id` without a separately justified decision.

## 8. `movement_id` — Boquilhas movement

- Identifies one Boquilhas quantity movement/event.
- Movement type is exactly one of `saida`, `entrada`, or `entrada_sem_reparacao`.
- Movement identity is distinct from `bq_id`, `tool_id`, the Boquilhas register identity, and any audit-entry identity.

## Structures and names that deliberately do not create canonical identities

### `resumo_id`

`resumo_id` does not exist.

Resumo is a read composition / derived dashboard-document surface inside Controlo. It is not a persisted parent, FK anchor or independent canonical identity.

### `bq_repair_trace_id`

`bq_repair_trace_id` is not currently a required product identity.

Its prior appearance in authority must not be interpreted as a requirement to replace the existing `boquilhas_id` model. It remains a possible future design only if an explicit product need justifies a separate identity.

### `tool_technical_values`

`tool_technical_values` is an optional 1:1 extension of a Tool and uses `tool_id` as its PK/FK.

There is no separate `technical_values_id`.

The normal Tool registry/search path stays light; specialized technical values are loaded only when a consuming workflow asks for them.
