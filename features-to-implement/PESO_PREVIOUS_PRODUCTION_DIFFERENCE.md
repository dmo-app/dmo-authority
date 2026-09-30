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

Machine is useful context and display information, but machine equality must not exclude a valid previous production when the same canonical CM Tool was used on another compatible machine.

If more than one valid historical Peso candidate exists, the user explicitly selects the intended one. The frontend must not silently choose between ambiguous candidates.

## Unequal CM counts

The current and previous productions do not need the same number of CM measurement rows.

Example:

```text
current:    CM1 CM2 CM3 CM4
previous:   CM1 CM2 CM3 CM4 CM5
```

Only valid corresponding CMs participate:

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

- a valid previous Peso from another compatible machine remains eligible when the canonical CM `tool_id` matches;
- machine equality is not a hidden history filter;
- unequal CM counts do not block the historical difference;
- only valid corresponding CMs contribute to the displayed difference/average;
- unmatched CMs are excluded;
- no valid counterpart produces an explicit refusal;
- no `comparacao_id` is created by this normal Peso behavior;
- multiple valid historical candidates require explicit selection.
