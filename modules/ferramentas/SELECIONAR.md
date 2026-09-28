# Ferramentas — Selecionar

## Goal

Select an existing canonical Tool for an originating workflow.

## Explicit choice

Tool selection is always explicit.

Search, filters, ordering, or a single remaining candidate must never silently choose a Tool for the user.

The selected value passed back to the originating workflow is the canonical `tool_id`.

## Relationship boundary

Ferramentas selects an existing Tool identity.

It does not create or infer the production-context identities that may later reference that Tool:

- `cm_id`;
- `mf_id`;
- `bq_id`;
- `jobon_id`.

Those are created only by their owning workflow according to the relevant module rules.

If the required Tool does not exist, the user may invoke the Ferramentas creation action and then continue with the resulting `tool_id`.
