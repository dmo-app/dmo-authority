# Ferramentas — Overview

## Purpose

Ferramentas is the canonical registry and consultation surface for the physical Tools known to DMO.

The primary surface is the **list of existing Tools**, not a create/edit form. Creating or editing a Tool is an action available from that registry.

## Canonical identity

Every Tool is identified by `tool_id`.

The registry covers the Tool types currently in Beta scope:

- CM
- MF
- BQ

A different lot represents a different canonical Tool and therefore a different `tool_id`.

## Main surface

The Ferramentas surface must allow the user to consult the existing Tool park and narrow it using operational metadata.

The canonical filtering dimensions are:

- Tool type;
- reference;
- lot;
- compatible machine/line;
- process;
- state.

Filtering narrows the visible candidates. It never selects a Tool automatically.

The surface may expose actions such as:

- view Tool details;
- edit a Tool where the workflow allows it;
- create a missing Tool.

## Contextual use

Ferramentas remains contextual in the Beta. It is reached from workflows that need a Tool, such as Job On or Boquilhas, rather than becoming a mandatory top-level destination.

When Ferramentas is opened from another workflow, the origin context is preserved.

The user explicitly selects the required Tool. The selected canonical `tool_id` is then returned to the originating workflow.

Ferramentas never creates `jobon_id`, `cm_id`, `mf_id`, or `bq_id`. Those identities belong to their owning workflows.

## Creation is an action, not the page identity

Creating a Tool does not redefine Ferramentas as a creation form.

If the required Tool does not exist, the user may invoke **Create Tool** from the registry, create the missing canonical Tool, and continue the originating workflow with the resulting `tool_id`.

The UI implementation of this action belongs to `dmo-design`; this authority defines only the functional behavior.
