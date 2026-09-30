# Peso / Tool Technical Values Alignment

**Status:** VERIFY CURRENT IMPLEMENTATION — IMPLEMENT CHANGES WHERE MISSING

**Type:** Controlo Create alignment / regression prevention

## Purpose

Verify the current Peso implementation against the current blueprint after the Tool technical-value and ownership rules were clarified.

Related blueprint:

- `modules/controlo-create/PESO.md`;
- `modules/ferramentas/VALORES_TECNICOS.md`.

## Required model

Peso is normally anchored through the production `cm_id`.

Tool-owned technical values are resolved through the real relation:

```text
cm_id
-> tool_id
-> tool_technical_values
```

Confirmed reusable Tool-owned values include:

- `volume_marisa`;
- `volume_puncao`;
- `diametro_gargalo`.

Missing Tool technical values must not be invented.

## Measurement/calculation distinction

Physical measurement and technical calculation are separate concepts.

Physical weighing uses:

```text
CM + TP
```

TP/Calote belongs to the Job On production context.

The later technical calculation uses the applicable drawing/Tool technical values according to the Peso blueprint.

TP does not become a Tool-owned technical value merely because Peso consumes it.

## Historical stability

Planning/Architect must verify which inputs are stable owner facts and which facts must be frozen on the Peso record when consumed to preserve historical truth.

Do not duplicate stable Tool facts into Peso merely for convenience.

## Verification outcome

This task may end as:

- `VERIFIED_IMPLEMENTED` if the current implementation already matches;
- `IMPLEMENTED` if changes are required;
- `BLOCKED_BY_BLUEPRINT_GAP` if a real missing product decision is discovered.

## Reviewer checks

Reject an implementation that:

- manually re-enters Tool technical values in Peso when they already belong to the Tool;
- moves TP/Calote ownership into Tool technical values;
- invents missing technical values;
- merges physical weighing and the later technical formula into one misleading concept;
- duplicates owner facts without a historical-stability reason.
