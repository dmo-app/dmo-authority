# Peso — Historical Difference

**Status:** FUNCTIONALLY DEFINED — IMPLEMENTATION SLICE

**Owner:** Controlo Create → Peso

## Objective

The normal Peso workflow must let the operator select an eligible historical Peso and show the difference against the current production.

This is normal Peso behavior.

It is not the separate Controlo Create → Comparação workflow and it does not allocate or use `comparacao_id`.

## Operation anchor

The read begins from the current Peso / CM production context and follows truthful persisted identities:

```text
current peso_id / cm_id
→ current CM tool_id
→ historical CM contexts for that same tool_id
→ eligible historical Job On / Peso candidates
→ explicit user selection of one historical Peso
→ valid corresponding CM measurements
→ historical difference read model
```

The frontend carries the current persisted context. The backend resolves the eligible historical Peso candidates through the real relations and returns only the candidate/history packet needed by the Peso surface. After explicit user selection, the selected persisted Peso identity anchors the comparison read.

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

For `202603`, the eligible history includes both `202602 / C1` and `202601 / B1`.

The backend must return the eligible candidates across the Tool's compatible machines. The UI may order them by production/date descending so the nearest history is shown first.

The ordering is not selection.

```text
eligible candidates
→ ordered newest/nearest first
→ user explicitly selects one
→ selected historical peso_id becomes the comparison context
```

The application must never auto-associate a historical Peso merely because it is the nearest, first, or only candidate.

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
Peso historical difference
= normal Peso read/support behavior
= current production vs explicitly selected eligible historical Peso
= no comparacao_id

Comparação
= separate re-measurement event after a decided Peso
= new comparacao_id
= per-CM Manter / Colocar de parte
```

The two workflows may both display calculated differences, but they do not share identity or lifecycle.

## Acceptance evidence

Tests must prove that:

- a Tool compatible with B1 and C1 exposes eligible historical Pesos from both machines;
- with `202601/B1 → 202602/C1 → 202603/B1`, both `202602/C1` and `202601/B1` remain selectable for `202603`;
- candidates may be ordered newest/nearest first without being auto-selected;
- current-machine equality is not a hidden history filter;
- 4 current measurements can be compared against 5, 6 or 3 previous measurements without a same-count validation error;
- only valid corresponding CMs contribute to the displayed difference/average;
- unmatched CMs are excluded;
- no valid counterpart produces an explicit refusal;
- no `comparacao_id` is created by this normal Peso behavior;
- the user explicitly selects the historical Peso;
- no candidate is automatically associated, including when only one candidate remains.
