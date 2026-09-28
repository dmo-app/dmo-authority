# Canonical Identities

This file defines domain identities. A database table, read model, UI tab or document does not become an identity merely because it exists.

## 1. `tool_id` — canonical Tool

- Identifies one canonical physical Tool registered in Ferramentas.
- Applies to CM, MF and BQ.
- Consumer modules reference the Tool; they do not create replacement Tool identities.
- A different lot is a different canonical Tool and therefore a different `tool_id`.

**Implementation:** present in `dmo-app/dmo-app-beta`.

## 2. `jobon_id` — production occurrence

- Identifies one concrete production occurrence.
- Job On owns this identity.
- It is not replaced by a generic `production_id`.
- Downstream workflows use the exact production/context identities they actually need instead of inventing a second production identity.

**Implementation:** present in `dmo-app/dmo-app-beta`.

## 3. `cm_id`, `mf_id`, `bq_id` — Tool-in-production contexts

- Identify the CM, MF or BQ context used in one `jobon_id`.
- They reference a canonical `tool_id` but are not Tools themselves.
- They preserve the production-context snapshot required for historical truth.
- Clients never mint these identities.

**Implementation:** present in `dmo-app/dmo-app-beta`.

## 4. `peso_id` — Peso record

- Identifies one specific Peso control/result record.
- It is not a production identity, Tool identity, Job On identity, CM identity, approval copy or revision.
- The normal production path anchors Peso through `cm_id`.

**Implementation:** present in `dmo-app/dmo-app-beta`.

## 5. `comparacao_id` — optional Peso Comparação

- Identifies one optional Comparação started for an existing `peso_id`.
- A Peso without a Comparação is valid.
- Comparação does not create a replacement production or Tool identity.

**Implementation:** present in `dmo-app/dmo-app-beta`.

## 6. `boquilhas_id` — Boquilhas register

- Identifies one Boquilhas movement register.
- In the production-linked state the register resolves through `bq_id` to the real Job On/BQ context.
- A provisional pre-JobOn register may be anchored on the canonical BQ `tool_id` until an explicit association to the matching `bq_id` is confirmed.
- The same `boquilhas_id` survives that association.
- It is not a physical BQ-piece identity and it has no close/reopen lifecycle.

**Implementation:** present in `dmo-app/dmo-app-beta`.

## 7. `movement_id` — Boquilhas movement

- Identifies one quantity movement/event inside one Boquilhas register.
- Movement identity is distinct from the register identity and from any audit-entry identity.
- Movement facts remain facts even if the register later becomes associated from provisional `tool_id` to `bq_id`.

**Implementation:** present in `dmo-app/dmo-app-beta`.

## Structures that deliberately do not create new identities

### `tool_technical_values`

`tool_technical_values` is an optional 1:1 extension of a Tool and uses `tool_id` as its PK/FK.

There is no separate `technical_values_id`.

The normal Tool registry/search path stays light; specialized technical values are loaded only when a consuming workflow asks for them.
