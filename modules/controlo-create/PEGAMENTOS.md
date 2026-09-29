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
