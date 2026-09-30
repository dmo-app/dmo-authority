# Ferramentas — Consultar

## Goal

Consult the canonical Tool registry and find an existing Tool using operational metadata.

## Required behavior

The user starts from the list of registered Tools.

The list exposes enough information to distinguish candidates, including:

- type;
- reference;
- lot;
- compatible machines/lines;
- process;
- state.

The user may filter the list by those dimensions.

For process, the current values are `NNPB` and `PS`.

Filters only narrow the candidate set. They never infer or auto-select the correct Tool, including when only one candidate remains.

Selection is always an explicit human action.

## Result

When consultation happens inside an originating workflow, explicit selection returns the selected canonical `tool_id` to that workflow.

No new production context identity is created by consultation or selection.
