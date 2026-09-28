# Ferramentas — Criar

## Goal

Create a missing canonical Tool when the required Tool is not already present in the Ferramentas registry.

## Rule

**Create Tool** is an action inside Ferramentas. It is not the primary identity of the Ferramentas surface.

Creating a Tool creates a new canonical `tool_id`.

Lot is Tool metadata and part of the Tool's real-world distinction. A different lot represents a different Tool and therefore receives a different `tool_id`.

## Contextual creation

A workflow such as Job On or Boquilhas may enter Ferramentas because the required Tool does not yet exist.

The originating context must be preserved while the Tool is created.

After successful creation, the resulting canonical `tool_id` can be returned to the originating workflow.

Creation does not mint `jobon_id`, `cm_id`, `mf_id`, or `bq_id`; those identities remain owned by their respective workflows.

## Presentation boundary

Whether creation is rendered as a modal, panel, or another interaction pattern is visual authority owned by `dmo-design`, not by this document.
