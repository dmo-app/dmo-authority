# Ferramentas — Valores Técnicos

## Purpose

Some technical values belong to a canonical Tool and are reusable by specialized workflows, but they are not required by the normal Ferramentas registry/list read.

They live in an optional Tool extension.

## Identity

The extension has **no independent identity**.

```text
tool_id
  └── tool_technical_values
```

`tool_id` is both the relationship and the identity key of the extension.

Do not introduce `technical_values_id`.

## Current technical values

The current Beta implementation supports:

- `volume_marisa`
- `volume_puncao`
- `diametro_gargalo`
- `peso_nominal_novo`

A missing extension row means those optional Tool technical values are not registered. The system must not invent substitute values.

## Read path

The normal Ferramentas list/search path remains light and does not load the extension.

A consuming workflow requests the values only when needed.

For Peso-related use through a CM production context, the established read path is:

```text
cm_id
  → cm_contexts.tool_id
  → tool_technical_values
```

The context is used to resolve the real canonical Tool; the technical values remain Tool-owned.

## Current implementation association

`dmo-app/dmo-app-beta` currently implements this through:

- `src/DMO.Application/Tools/ToolTechnicalValuesReadModel.cs`
- `src/DMO.Infrastructure/Persistence/ToolJobOn/DmoToolTechnicalValuesRead.cs`
- `src/DMO.Infrastructure/Persistence/ToolJobOn/Entities/ToolTechnicalValuesEntity.cs`
- migration `20260927114825_011_ToolTechnicalValues`

This implementation association is descriptive. The rules above are the canonical behavior.
