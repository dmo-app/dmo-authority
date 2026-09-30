# Controlo Create — Pegamentos

Pegamentos belongs to Controlo Create.

It has no separate approval capability or workflow and may legitimately be absent for a production.

## Measurement rules

- Costura = axis 0°.
- Contra costura = axis 90°.
- Ovalização = Costura - Contra costura.
- Média = (Costura + Contra costura) / 2.
- Tolerance = nominal ± 0.20.

Tolerance alerts are informative and non-blocking. They do not decide, approve, reject or stop production automatically.

## Context

Pegamentos uses the real production component identities:
- `cm_id`;
- `mf_id`;
- `bq_id`.

Known technical inputs are resolved from the relevant context and canonical Tool data. The frontend must not invent them or become a second calculation authority.

## Implementation during testing

Consider an assisted-entry behavior for Tool throat diameters:

- when one CM/BQ/MF diameter is entered first, suggest the related expected diameters using the configured dimensional difference;
- apply the same relation regardless of which of the three components is entered first;
- treat the generated values as suggestions only;
- never overwrite an already registered canonical Tool technical value automatically.

This is a testing-stage usability enhancement, not a requirement for the initial development path.
