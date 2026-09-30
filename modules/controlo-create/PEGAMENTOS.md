# Controlo Create — Pegamentos

Pegamentos belongs to Controlo Create.

It has no separate approval capability or workflow and may legitimately be absent for a production.

## Measurement rules

- Costura = axis 0°.
- Contra costura = axis 90°.
- Ovalização = Costura - Contra costura.
- Média = (Costura + Contra costura) / 2 when both axes are measurable.
- Tolerance = nominal ± 0.20 unless the configured tolerance value is changed.

Tolerance is evaluated against the measurement value used for that component.

For the normal two-axis case:

- Costura and Contra costura are the source measurements;
- Ovalização is derived and preserves its positive/negative sign;
- Média is the value compared against the component nominal/tolerance corridor;
- reaching either tolerance boundary already raises an alert;
- going outside either boundary also raises an alert.

Tolerance alerts are informative and non-blocking. They do not decide, approve, reject or stop production automatically.

### When Contra costura cannot be measured

Some CM geometries do not allow a valid Contra costura / 90° measurement.

This must not block Pegamentos.

In that case:

- record Costura only;
- Contra costura is treated as not applicable / not measurable, not as an error;
- Ovalização is not calculated;
- Média = Costura, because it is the only measurable value available for that CM;
- tolerance evaluation uses that single measurable value.

The workflow must not require a fabricated second-axis value merely to complete the record.

## Context

Pegamentos uses the real production component identities:
- `cm_id`;
- `mf_id`;
- `bq_id`.

Known technical inputs are resolved from the relevant production context and canonical Tool data. The frontend must not invent them or become a second source of calculation truth.

The component Tools are already established by the active Job On context. Pegamentos does not reselect CM, MF or BQ merely to perform the measurement.

The relevant throat-diameter nominal values are Tool technical values resolved through the applicable component context.

## Testing-stage assisted entry

Consider an assisted-entry behavior for Tool throat diameters:

- when one CM/BQ/MF diameter is entered first, suggest the related expected diameters using the configured dimensional difference;
- apply the same relation regardless of which of the three components is entered first;
- treat the generated values as suggestions only;
- never overwrite an already registered canonical Tool technical value automatically.

This is an optional testing-stage usability exploration. It is not part of the required core Pegamentos behavior unless later adopted explicitly.
