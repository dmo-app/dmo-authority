# Ferramentas — Overview

## Purpose

Ferramentas is the canonical registry and consultation surface for the physical Tools known to DMO.

The primary surface is the **list of existing Tools**, not a create/edit form. Creating or editing a Tool is an action available from that registry.

## Canonical identity

Every Tool is identified by `tool_id`.

The registry covers the Tool types currently in scope:

- CM
- MF
- BQ

A different lot represents a different canonical Tool and therefore a different `tool_id`.

This is an identity invariant:

```text
new lot
→ new Tool
→ new tool_id
```

A lot change is never an in-place update of an existing Tool identity.

## Main surface

The Ferramentas surface must allow the user to consult the existing Tool park and narrow it using operational metadata that belongs to the current application scope.

The relevant filtering dimensions include:

- Tool type;
- reference;
- lot;
- compatible machine/line;
- process.

Filtering narrows the visible candidates. It never selects a Tool automatically.

The surface may expose actions such as:

- view Tool details;
- edit a Tool where the workflow allows it;
- create a missing Tool.

## Contextual use

Ferramentas remains contextual in the current application. It is reached from workflows that need a Tool, such as Job On or Boquilhas, rather than becoming a mandatory top-level destination.

When Ferramentas is opened from another workflow, the origin context is preserved.

The user explicitly selects the required Tool. The selected canonical `tool_id` is then returned to the originating workflow.

Ferramentas never creates `jobon_id`, `cm_id`, `mf_id`, or `bq_id`. Those identities belong to their owning workflows.

## Creation is an action, not the page identity

Creating a Tool does not redefine Ferramentas as a creation form.

If the required Tool does not exist, the user may invoke **Create Tool** from the registry, create the missing canonical Tool, and continue the originating workflow with the resulting `tool_id`.

The UI implementation of this action belongs to `dmo-app/dmo-design`; this blueprint defines the functional behavior.

## Optional technical values

A Tool may have specialized reusable technical values stored in the optional `tool_technical_values` extension.

That extension:

- is 1:1 with the Tool;
- is keyed directly by `tool_id`;
- creates no second identity or UUID;
- has no independent lifecycle;
- is not loaded as part of the normal registry/search path;
- is retrieved only when a consuming workflow needs those values;
- contains reusable Tool-owned technical facts, not production-specific or module-specific facts.

A separate table does not imply a separate domain identity.

The dedicated contract is in [VALORES_TECNICOS.md](./VALORES_TECNICOS.md).

## Ownership boundary

Ferramentas owns canonical Tool identity and Tool-owned reusable facts.

It does not absorb:

- production-specific configuration;
- Controlo measurements or decisions;
- Boquilhas movement history;
- arbitrary module-specific records.

A consuming module may reach Tool-owned information through the real persisted relations without taking ownership of that information.

## Current implementation association

In `dmo-app/dmo-app-beta`, the current implementation is anchored by:

- `src/DMO.Application/Tools/IToolService.cs`
- `src/DMO.Application/Tools/ToolService.cs`
- `src/DMO.Application/Tools/ToolTechnicalValuesReadModel.cs`
- `src/DMO.Infrastructure/Persistence/ToolJobOn/`
- migration `20260927114825_011_ToolTechnicalValues`

These paths describe current implementation reality; this document remains the functional blueprint for Ferramentas.
