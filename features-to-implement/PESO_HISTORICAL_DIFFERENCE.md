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
current jobon_id / peso_id
→ current production reference
→ historical Job On / Peso candidates for that reference
→ rank useful candidates near the top
→ explicit user selection of one historical peso_id
→ historical difference read model
```

The frontend carries the current persisted context. The backend resolves the eligible historical Peso candidates through the real relations and returns only the candidate/history packet needed by the Peso surface. After explicit user selection, the selected persisted Peso identity anchors the comparison read.

## Historical lookup and ranking

The candidate pool is historical Peso data for the same production reference.

The application must not use current-machine equality or Tool equality as exclusion rules. A production on another machine, or with a different Tool set, may still be the comparison the operator needs.

Machine and Tool context are ranking/display assistance only:

```text
historical candidates for same reference
→ same machine and/or compatible/relevant Tool context may rank higher
→ newer candidates may rank before older candidates within that assistance
→ all candidates remain selectable
→ user explicitly selects one peso_id
```

The selector shows enough saved context to make the choice deliberately, including production/date, machine and relevant Tool context.

No candidate is automatically associated because it is first, nearest, on the same machine, has compatible Tools, or is the only candidate.

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

- historical Pesos for the same production reference remain selectable across different machines and Tool sets;
- same-machine and/or compatible/relevant-Tool candidates may be ranked near the top without excluding the rest;
- candidates may be ordered by recency inside the ranking assistance without being auto-selected;
- machine equality and Tool equality are not hidden eligibility filters;
- 4 current measurements can be compared against 5, 6 or 3 previous measurements without a same-count validation error;
- only valid corresponding CMs contribute to the displayed difference/average;
- unmatched CMs are excluded;
- no valid counterpart produces an explicit refusal;
- no `comparacao_id` is created by this normal Peso behavior;
- the user explicitly selects the historical Peso;
- no candidate is automatically associated, including when only one candidate remains.
