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
- Peso may consume Controlo-context facts without becoming their authority.

## Backend-owned calculation

The frontend must not calculate the industrial result.

Water density:
- authoritative table;
- valid temperature range: 5–35 °C;
- use rounded whole-degree temperature;
- no interpolation.

Glass density:
- configured in Controlo Definições by process where applicable;
- frozen when consumed by the Peso record.

Formula:

```text
capacity = water weight / water density
glass weight =
(capacity + volume_marisa - volume_puncao) * glass density
```

Tampão/calote does not enter this main Peso formula.

## Tool technical values

`volume_marisa` and `volume_puncao` are not re-entered manually in Peso.

They are resolved through the real production context:

```text
cm_id
→ tool_id
→ tool_technical_values
```

The Tool remains the authority for those technical values. Missing values must not be invented.
