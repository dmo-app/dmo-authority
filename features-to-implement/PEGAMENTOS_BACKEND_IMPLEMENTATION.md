# Pegamentos Backend / Persistence Implementation

**Status:** FUNCTIONAL BLUEPRINT DEFINED — PRODUCTION BACKEND/PERSISTENCE MISSING IN INSPECTED BASELINE

**Type:** Controlo Create feature implementation

## Functional source

Implementation must follow `modules/controlo-create/PEGAMENTOS.md`.

Pegamentos belongs to Controlo Create and has no separate approval workflow.

It may legitimately be absent for a production.

## Core measurement behavior

Normal two-axis case:

- Costura = 0°;
- Contra costura = 90°;
- Ovalização = Costura - Contra costura;
- Média = (Costura + Contra costura) / 2;
- configured tolerance applies to the value used for that component;
- reaching or exceeding a tolerance boundary raises a non-blocking alert.

When Contra costura cannot be measured because of CM geometry:

- record Costura only;
- Contra costura is not applicable, not an error;
- do not calculate Ovalização;
- Média = Costura;
- tolerance evaluation uses the single measurable value.

## Context

Pegamentos consumes the real production component contexts:

- `cm_id`;
- `mf_id`;
- `bq_id`.

Known Tool technical values are resolved through those real contexts and canonical Tool data.

Pegamentos must not reselect CM/MF/BQ merely to perform the measurement.

## Initial implementation boundary

The testing-stage assisted-entry idea for throat diameters is optional usability exploration, not a requirement for the initial production path.

## Reviewer checks

Reject an implementation that:

- blocks a valid single-axis measurement because 90° is physically impossible;
- fabricates a second-axis value;
- makes tolerance alerts automatic approval/rejection decisions;
- creates private CM/MF/BQ identities inside Pegamentos;
- duplicates canonical Tool technical values solely because Pegamentos consumes them;
- imports prototype/browser-only persistence as backend canon.
