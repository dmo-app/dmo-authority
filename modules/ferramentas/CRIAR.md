# Ferramentas — Criar

## Goal

Create a missing canonical Tool when the required Tool is not already present in the Ferramentas registry.

## Rule

**Create Tool** is an action inside Ferramentas. It is not the primary identity of the Ferramentas surface.

Creating a Tool creates a new canonical `tool_id`.

Lot is Tool metadata and part of the Tool's real-world distinction. A different lot represents a different Tool and therefore receives a different `tool_id`.

The searchable attributes of a Tool do not form a derived identity key. The new canonical identity is the backend-issued `tool_id`.

## Registry-first creation surface

Opening Ferramentas shows the existing registered Tools in a table.

From that same registry surface, the user can invoke **Create Tool** when the required Tool is missing.

The creation flow captures the Tool facts already defined by the Ferramentas registry, including the applicable:

- Tool type;
- canonical reference;
- lot;
- quantity of physical tools represented by that Tool/lot;
- compatible machines/lines;
- process;
- state;
- optional CM → MF-reference association where applicable.

`quantity` is Tool-owned data associated with the canonical `tool_id`. It does not create one Tool identity per physical piece.

Example:

```text
BQ
lot = 4
quantity = 120
```

This means that the created BQ Tool/lot carries an accounted quantity of 120 physical BQ tools.

For BQ Tools, Boquilhas later consumes this quantity as the lot total used together with repair movements to derive operational quantities. Boquilhas consumes the value but does not own or duplicate it.

For **CM Tools**, the manufacturing process is a required Tool fact. The current selectable CM process values are:

```text
NNPB
PS
```

The selected process is persisted on the canonical CM Tool and can later be used for consultation/filtering. Peso consumes this process through the selected CM production context; the process is not re-entered or independently owned by Peso.

The process does not determine or derive `tool_id`.

Specialized `tool_technical_values` remain the optional Tool-owned extension defined in `VALORES_TECNICOS.md`; their existence must not make the normal registry table heavy.

## Beta access

In the current Beta there is no need to restrict Tool creation by a separate Ferramentas capability.

Any user who has access to the DMO application may create a Tool.

The same Beta access rule applies to the Tool delete action so test data can be created and removed during validation of the application.

This is a Beta access decision. It does not create a permanent full-application permission model for Ferramentas.

## Optional CM → MF-reference association

Most CM Tools require no extra association beyond their own canonical reference.

When a CM's real reference differs from the MF/production reference under which operators normally need to find it, Tool create/edit may expose an explicit optional action to associate that CM with the relevant MF reference.

For example:

```text
CM canonical reference = 5809
optional MF-reference association = 5810
```

This does not make `5810` a second CM reference. The CM remains reference `5809`.

The association is optional:
- it must not be required for every newly created CM;
- it may be registered during creation when already known;
- it may be added or corrected later from the Tool detail/edit flow;
- absence of an association does not create a replacement identity or alter the CM's canonical reference.

Its only purpose is to help consuming workflows discover relevant CM candidates.

## Contextual creation

A workflow such as Job On or Boquilhas may enter Ferramentas because the required Tool does not yet exist.

The originating context must be preserved while the Tool is created.

After successful creation, the resulting canonical `tool_id` can be returned to the originating workflow.

Creation does not mint `jobon_id`, `cm_id`, `mf_id`, or `bq_id`; those identities remain owned by their respective workflows.

## Presentation boundary

Whether creation is rendered as a modal, panel, or another interaction pattern is a visual-design decision owned by `dmo-app/dmo-design`, not by this document.
