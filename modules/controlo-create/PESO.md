# Controlo Create — Peso

Peso belongs to Controlo Create.

## Create-side flow

The user may:

1. measure;
2. create the Peso record;
3. save;
4. submit.

Approval actions do not belong to this surface.

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
