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

## Previous-production difference is part of normal Peso

The normal Peso workflow compares the current production against the previous eligible production so the operator can see the difference.

This is part of Peso itself. It is **not** the separate `Comparação` workflow and it does not create a `comparacao_id`.

Conceptually:

```text
current Peso / current CM context
→ current cm_id
→ tool_id
→ previous eligible production context(s) for that same canonical CM Tool
→ previous Peso
→ compare the valid corresponding CM measurements
→ show the difference in the normal Peso flow
```

Historical lookup follows the canonical CM Tool identity and the machines on which that Tool is allowed to work.

The comparison filter must **not** be limited to the machine of the current production. It must consider the previous use of the same canonical `tool_id` across the Tool's compatible machines.

Example:

```text
tool_id = X
compatible machines = B1, C1

202601 → B1
202602 → C1
202603 → B1  ← current production
```

For `202603`, the previous production for the normal Peso difference is `202602 / C1`.

The system must not filter history to `B1` merely because the current production is on B1 and therefore skip back to `202601 / B1`.

Machine remains useful context/display information, but current-machine equality is not the rule that determines the previous production.

If current and previous productions contain different numbers of CM measurements, the difference view remains valid:

```text
current:    CM1 CM2 CM3 CM4
previous:   CM1 CM2 CM3 CM4 CM5

CM1 ↔ CM1
CM2 ↔ CM2
CM3 ↔ CM3
CM4 ↔ CM4
CM5 → no current counterpart → excluded
```

Only valid corresponding CMs participate in the historical difference and its average. Unmatched rows are excluded rather than fabricated or used to block the operation.

The operation is refused only when no valid CM counterpart exists to compare.

The normal Peso flow resolves the immediately previous eligible production for that canonical CM Tool across its compatible machines. This is not a free historical-selection workflow.

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

## Tool technical values

`volume_marisa` and `volume_puncao` are not re-entered manually in Peso.

They are resolved through the real production context:

```text
cm_id
→ tool_id
→ tool_technical_values
```

The Tool remains the canonical owner of those technical values. Missing values must not be invented.
