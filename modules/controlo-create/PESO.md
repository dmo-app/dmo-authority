# Controlo Create — Peso

Peso belongs to Controlo Create.

## Create-side flow

The user may:

1. measure;
2. create the Peso record;
3. save;
4. submit.

Approval actions do not belong to this surface.

## Save and submit are different operations

Peso may be saved before it is submitted for approval.

```text
Save
→ persists the current Peso work
→ remains editable in Controlo Create
→ does not place the Peso in Controlo Approve

Submit
→ marks the Peso as por_aprovar
→ makes it available to Controlo Approve
→ prevents normal Create-side editing while it is awaiting a decision
```

A saved Peso that has never been submitted is not a separate `draft` status. It is simply a persisted Peso that has not yet entered the approval decision cycle.

Therefore:

```text
saved
!=
submitted
```

Save may happen repeatedly so work is not lost before submission.

## Historical difference is part of normal Peso

The normal Peso workflow lets the operator compare the current production against an eligible historical Peso so the difference can be seen.

This is part of Peso itself. It is **not** the separate `Comparação` workflow and it does not create a `comparacao_id`.

Conceptually:

```text
current production / Peso
→ current production reference
→ historical Pesos for that same production reference
→ user explicitly chooses which historical Peso to compare

selection assistance only:
→ same machine and/or compatible/relevant Tool context may appear first
→ within that assistance, newer productions may appear before older ones
→ all remaining historical Pesos stay visible and selectable

after the user's choice:
→ compare the valid corresponding measurements
→ show the difference in the normal Peso flow
```

The historical list must give the operator freedom to choose. It must not remove a production merely because it ran on another machine or used a different Tool set.

The governing rule is the user's explicit choice. Machine and Tool context may be used only to help present the list after that rule is established: historical Pesos from the same machine and/or with compatible/relevant Tool context may appear near the top, and newer productions may appear before older ones within that assistance.

This ranking is assistance, not eligibility and not selection:

```text
same machine / compatible Tool context
→ may rank higher

different machine / different Tool context
→ remains visible and selectable

first result
!=
selected result
```

The application must never automatically associate a historical Peso. The user always chooses the production that makes operational sense.

The number of Peso measurement rows does **not** have to be equal between the current and previous productions.

A same-count check is forbidden as a precondition for the historical difference.

Examples:

```text
current Peso = 4 measurements

previous Peso = 5 measurements  → valid
previous Peso = 6 measurements  → valid
previous Peso = 3 measurements  → valid
```

Only the measurement rows/CMs that have a valid counterpart participate in the difference.

Example:

```text
current:    CM1 CM2 CM3 CM4
previous:   CM1 CM2 CM3 CM4 CM5

CM1 ↔ CM1
CM2 ↔ CM2
CM3 ↔ CM3
CM4 ↔ CM4
CM5 → no current counterpart → excluded
```

The same principle applies when the previous Peso has fewer rows: only the valid corresponding rows participate.

Unmatched rows are excluded rather than fabricated or used to block the operation. The historical difference average uses only the participating matched rows.

The operation is refused only when no valid CM counterpart exists to compare.

The normal Peso flow presents historical Peso candidates for the same production reference.

The user explicitly selects which historical Peso to compare. Sorting by nearest/latest date is presentation assistance only.

```text
candidate order
!=
automatic association
```

Even when only one eligible historical Peso remains, the system must not silently associate it; the user confirms the selection.

## Identity and lifecycle

- `peso_id` is the durable Peso identity.
- The same `peso_id` persists through the later decision lifecycle.
- Approval does not create a copy.
- Peso is normally anchored through the production `cm_id`.
- Peso may consume production/context facts without taking ownership of them.

## Physical measurement vs technical calculation

These are two different stages and must not be merged conceptually.

### Physical measurement

The physical Peso measurement is performed with:

```text
CM + TP
```

TP/Tampão is physically present and adds mass to the observed measurement.

The applicable TP/Calote value is defined in the Job On production context.

Knowing that value allows Peso to account for the contribution added by TP when interpreting/correcting the observed measurement, particularly to obtain a more appropriate visual/support margin for wear analysis.

This use of TP is not the same as making TP a term in the main Peso formula.

### Technical calculation

After the physical measurement, the technical calculation uses the applicable values specified in the drawing, including the technical volumes associated with BQ and PU.

BQ and PU are therefore part of the later calculation through their technical/drawing values; they are not physically part of the CM + TP weighing step.

The frontend must not calculate the industrial result.

Water density:
- canonical table;
- valid temperature range: 5–35 °C;
- use rounded whole-degree temperature;
- no interpolation.

Glass density:
- configured in Controlo Definições by process where applicable;
- frozen when consumed by the Peso record.

Current main formula:

```text
capacity = water weight / water density
glass weight =
(capacity + volume_marisa - volume_puncao) * glass density
```

TP/Tampão does not enter this main formula that produces the Peso value sent to production.

## Production-specific CM usage percentage

Peso may display the CM usage percentage established for the production.

The value is not entered or calculated in Peso:

```text
Job On
→ manual % de uso from SAP
→ saved with the production/CM context
→ Peso reads the saved value
```

Peso does not take ownership of that value and must not create a second editable copy.

## Tool technical values

`volume_marisa` and `volume_puncao` are not re-entered manually in Peso.

They are resolved through the real production context:

```text
cm_id
→ tool_id
→ tool_technical_values
```

The Tool remains the canonical owner of those technical values. Missing values must not be invented.

## Peso PDF

The final production PDF for an approved Peso follows the dedicated field/source contract in [PESO_PDF.md](./PESO_PDF.md).

The PDF is generated from backend-resolved facts and calculated results. The frontend/document renderer does not independently reconstruct production context, Tool technical values, historical-control choice or the Peso formula.
