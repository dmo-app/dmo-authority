# Ferramentas — Valores Técnicos

## Purpose

Some technical values belong to a canonical Tool and are reusable by specialized workflows, but they are not required by the normal Ferramentas registry/list read.

They live in an optional 1:1 Tool extension.

## Identity

The extension has **no independent identity**.

```text
tool_id
  └── tool_technical_values
```

`tool_id` is both the relationship and the identity key of the extension.

Do not introduce:

- `technical_values_id`;
- a second UUID;
- an independent lifecycle for the extension.

A separate physical table does not create a separate domain identity.

## Current technical values

The confirmed reusable technical values currently include:

- `volume_marisa`;
- `volume_puncao`;
- `diametro_gargalo`.

These values are Tool-owned technical facts.

A missing extension row means the applicable optional technical values are not registered. The system must not invent substitute values.

## Consumption by workflows

The normal Ferramentas list/search path remains light and does not load the extension.

A consuming workflow requests only the technical values it actually needs.

For Peso-related use through a CM production context:

```text
cm_id
→ tool_id
→ tool_technical_values
```

Peso uses the applicable Tool technical values without becoming their owner.

Pegamentos and Comparação may likewise consume `diametro_gargalo` through the real Tool/context relations when that value is required by their workflow.

The value remains owned by Ferramentas/Tool and must not be duplicated into consumer-module persistence merely because several workflows use it.

## Boundary

`tool_technical_values` must contain reusable, relatively stable technical facts of the Tool.

It must not become storage for:

- production-specific configuration;
- measurement results;
- module-specific workflow state;
- unrelated historical events.

The exact field set grows only when a real current-scope workflow requires another reusable Tool-owned technical fact.

## Current implementation association

`dmo-app/dmo-app-beta` currently implements this through:

- `src/DMO.Application/Tools/ToolTechnicalValuesReadModel.cs`
- `src/DMO.Infrastructure/Persistence/ToolJobOn/DmoToolTechnicalValuesRead.cs`
- `src/DMO.Infrastructure/Persistence/ToolJobOn/Entities/ToolTechnicalValuesEntity.cs`
- migration `20260927114825_011_ToolTechnicalValues`

This implementation association is descriptive. The rules above are the canonical behavior.
