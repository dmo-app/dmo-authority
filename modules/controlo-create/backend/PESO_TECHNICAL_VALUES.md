# Peso / Tool Technical Values Alignment

**Status:** REQUIRED RECOVERY ALIGNMENT — DEPENDS ON TOOL TECHNICAL VALUES IMPLEMENTATION

**Type:** Controlo Create recovery alignment / regression prevention

## Purpose

Ensure the recovered Peso implementation follows the current blueprint after Tool technical-value ownership was clarified.

The older application backup does not contain the canonical Tool technical-values structure, so this work must be coordinated with:

- `TOOL_TECHNICAL_VALUES_IMPLEMENTATION.md`;
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

## Recovery implication

When restoring from the older backup, do not preserve an old input location merely because that is where an earlier implementation happened to obtain a value.

Recovery must move toward the current ownership model:

```text
stable reusable Tool fact
-> Tool technical values

production-specific fact
-> owning production/context record

Peso measurement/result
-> Peso
```

Any old data migration must preserve historical truth. It must not silently rewrite a historical Peso with today's Tool value if the value consumed at the time must remain historically frozen.

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

Planning/Architect must classify each consumed input as either:

- current stable owner fact that can be resolved through its real relation; or
- historically consumed/frozen fact that must remain attached to the Peso record to preserve what was actually used.

Do not duplicate stable Tool facts into Peso merely for convenience.

Do not overwrite truthful historical Peso inputs during recovery.

## Reviewer checks

Reject an implementation that:

- manually re-enters Tool technical values in Peso when they belong to the Tool;
- moves TP/Calote ownership into Tool technical values;
- invents missing technical values;
- merges physical weighing and the later technical formula into one misleading concept;
- duplicates owner facts without a historical-stability reason;
- overwrites historical consumed values with current Tool values during recovery;
- keeps an obsolete backup ownership model merely because it is easier to migrate.
