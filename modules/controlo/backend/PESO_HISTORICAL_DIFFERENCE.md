# Peso — Historical Difference Regression Contract

This backend contract protects the normal Peso historical-difference behavior from a known destructive/blocking regression.

It is not the separate Comparação workflow and it never creates `comparacao_id`.

## Candidate selection

The candidate pool is historical Peso data for the same production reference.

```text
current jobon_id / peso_id
→ current production reference
→ historical Peso candidates for that reference
→ explicit user choice of historical peso_id
→ historical-difference read model
```

Same machine, relevant/compatible Tool context and recency may influence presentation order only.

They must never be exclusion rules.

A historical Peso on another machine or with a different Tool set remains selectable when it belongs to the same production-reference history and is otherwise valid.

No candidate is automatically associated because it is first, latest, nearest, same-machine, Tool-compatible or the only candidate.

## Unequal measurement counts are valid

Current and historical Peso records do not need equal row counts.

```text
current = 4
historical = 5 → valid
historical = 6 → valid
historical = 3 → valid
```

Only valid corresponding measurement rows participate.

`cm_id` equality across productions is not a matching rule; those IDs identify different production contexts.

A physical CM number/position used for measurement correspondence remains row data inside the Peso. It is not another canonical CM identity.

Example:

```text
current:    CM1 CM2 CM3 CM4
historical: CM1 CM2 CM3 CM4 CM5

CM1 ↔ CM1
CM2 ↔ CM2
CM3 ↔ CM3
CM4 ↔ CM4
CM5 → unmatched → excluded
```

Unmatched rows:

- do not block the comparison;
- are not fabricated;
- are excluded from the historical-difference average.

The operation is refused only when there are **no valid measurement-row counterparts**, with an explicit reason.

## Boundary with Comparação

```text
Peso historical difference
= normal Peso support/read behavior
= current Peso vs explicitly selected historical Peso
= no comparacao_id

Comparação
= separate verification event
= starts only from an approved Peso
= new comparacao_id
= Manter / Colocar de parte decision
```

## Required regression tests

Tests must prove that:

- historical Pesos remain selectable across different machines and Tool sets;
- ranking never becomes hidden eligibility;
- 4 current rows can compare with 5, 6 or 3 historical rows;
- only valid counterparts participate;
- matching is not based on equality of `cm_id` across productions;
- unmatched rows are excluded rather than blocking/deleting history;
- no-valid-counterpart returns an explicit refusal;
- this flow never creates `comparacao_id`;
- the user explicitly selects the historical Peso, even when only one candidate exists.
