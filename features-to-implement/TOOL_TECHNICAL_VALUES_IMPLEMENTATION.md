# Tool Technical Values Implementation

**Status:** CANONICAL FEATURE DEFINED — REQUIRED WHEN RECOVERING FROM THE OLDER APP BACKUP

**Type:** Ferramentas persistence / recovery implementation

## Why this feature is listed

The current blueprint defines Tool technical values as a real part of the DMO model.

The older application backup intended as the recovery base does **not** contain this structure.

Therefore recovery must not assume that restoring the backup is enough. The recovered application must add Tool technical values explicitly according to the current blueprint.

This is different from saying that every current implementation baseline lacks the feature. The recovery baseline is the important fact for this implementation brief.

## Canonical model

Some reusable technical facts belong to the canonical Tool.

They live in an optional 1:1 extension:

```text
tool_id
  └── tool_technical_values
```

The extension has no independent domain identity.

`tool_id` is the identity key and relationship.

Do not introduce:

- `technical_values_id`;
- a second UUID;
- an independent lifecycle for the extension.

A separate physical table does not create a separate business identity.

## Current confirmed values

The currently confirmed Tool-owned technical values are:

- `volume_marisa`;
- `volume_puncao`;
- `diametro_gargalo`.

They are reusable Tool facts, not production-specific configuration.

A missing value remains missing. Recovery/import must not invent or guess values merely to populate the new structure.

## Ownership boundary

Tool technical values belong to the canonical Tool.

Consumers reach them through the real production-context relation.

For Peso:

```text
cm_id
-> tool_id
-> tool_technical_values
```

Pegamentos and Comparação may likewise consume `diametro_gargalo` through the applicable Tool/context relation.

Consumer workflows do not become owners of these values merely because they use them.

## Recovery implementation work

Planning/Architect must design the recovery addition so that the older backup gains the current canonical structure without distorting existing operational data.

At minimum the work must determine:

1. the persistence representation for the optional 1:1 Tool extension;
2. creation/update behavior from Ferramentas where the product surface requires it;
3. read paths for consuming workflows;
4. migration behavior for existing Tools from the backup;
5. behavior when an existing Tool has no technical values yet;
6. tests proving that consumer workflows resolve the values through `tool_id` rather than duplicated module-specific fields.

Existing Tools from the backup must preserve their existing `tool_id`.

Adding technical values must enrich the existing Tool identity, not replace it.

## Migration/backfill rule

For an existing Tool from the backup:

```text
existing tool_id
-> add optional technical-values extension when known
```

Not:

```text
old Tool
-> create replacement Tool only to obtain technical values
```

If reliable source data for a technical value does not exist during recovery, leave it unregistered and let the normal workflow expose/handle the missing value according to the owning module rules.

## What must not be imported from the old backup as canon

The absence of Tool technical values in the older application is an implementation limitation of that baseline.

It does not mean:

- the values belong to Peso;
- the values belong to Job On;
- the values should be typed again on every production;
- a new Tool identity is required;
- the current blueprint rule should be removed to match the backup.

## Reviewer checks

Reject a recovery implementation that:

- creates a new identity for the technical-values extension;
- creates replacement Tools instead of enriching existing `tool_id` records;
- duplicates the same Tool facts into Peso/Pegamentos/Comparação persistence for convenience;
- moves production-specific facts into Tool technical values;
- invents missing values during migration;
- treats the older backup as evidence that the feature is unnecessary.
