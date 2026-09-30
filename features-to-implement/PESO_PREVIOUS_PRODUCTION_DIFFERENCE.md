# Peso — Previous-Production Difference

**Status:** FUNCTIONALLY DEFINED — IMPLEMENTATION SLICE

**Owner:** Controlo Create → Peso

## Objective

The normal Peso workflow must show the difference between the current production and the previous eligible production.

This is normal Peso behavior.

It is not the separate Controlo Create → Comparação workflow and it does not allocate or use `comparacao_id`.

## Operation anchor

The read begins from the current Peso / CM production context and follows truthful persisted identities:

```text
current peso_id / cm_id
→ current CM tool_id
→ historical CM contexts for that same tool_id
→ previous eligible Job On / Peso
→ valid corresponding CM measurements
→ historical difference read model
```

The frontend carries the current persisted context. The backend resolves the previous eligible Peso through the real relations and returns only the historical comparison packet needed by the Peso surface.

## Historical lookup rules

The canonical CM `tool_id` is the history anchor.

The lookup must consider the machines on which the canonical CM Tool is allowed to work.

It must **not** constrain history to `historical.machine == current.machine`.

Example:

```text
tool_id = X
compatible machines = B1, C1

202601 → B1
202602 → C1
202603 → B1  ← current
```

The previous eligible production for `202603` is `202602 / C1`.

The backend therefore resolves the immediately previous eligible production for the same canonical `tool_id` across its compatible machines. It must not skip `202602` simply because that production ran on C1 and the current production runs on B1.

This is deterministic previous-production resolution inside normal Peso, not a UI for freely choosing among arbitrary historical Pesos.

## Unequal Peso measurement counts

The current and previous Peso records do not need the same number of measurement rows.

The backend must not reject the historical difference merely because the counts differ.

```text
current Peso = 4 measurements

previous Peso = 5 measurements  → valid
previous Peso = 6 measurements  → valid
previous Peso = 3 measurements  → valid
```

Only valid corresponding measurement rows/CMs participate.

Example:

```text
CM1 ↔ CM1
CM2 ↔ CM2
CM3 ↔ CM3
CM4 ↔ CM4
CM5 → unmatched → excluded
```

Unmatched CM rows:

- do not block the normal Peso flow merely because the counts differ;
- are not fabricated into missing counterparts;
- are excluded from the historical difference average.

The operation is refused only when there are no valid CM counterparts to compare, with an explicit reason.

## Boundary with Comparação

Do not implement this slice through `modules/controlo-create/COMPARACAO.md`.

```text
Peso previous-production difference
= normal Peso read/support behavior
= current production vs previous eligible production
= no comparacao_id

Comparação
= separate re-measurement event after a decided Peso
= new comparacao_id
= per-CM Manter / Colocar de parte
```

The two workflows may both display calculated differences, but they do not share identity or lifecycle.

## Acceptance evidence

Tests must prove that:

- a Tool compatible with B1 and C1 can resolve its immediately previous eligible production across either machine;
- with `202601/B1 → 202602/C1 → 202603/B1`, the previous production for `202603` is `202602/C1`;
- current-machine equality is not a hidden history filter;
- 4 current measurements can be compared against 5, 6 or 3 previous measurements without a same-count validation error;
- only valid corresponding CMs contribute to the displayed difference/average;
- unmatched CMs are excluded;
- no valid counterpart produces an explicit refusal;
- no `comparacao_id` is created by this normal Peso behavior;
- the flow resolves the immediately previous eligible production rather than asking the user to choose an arbitrary historical Peso.
