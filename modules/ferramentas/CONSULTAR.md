# Ferramentas — Consultar

## Goal

Consult the canonical Tool registry and find an existing Tool using operational metadata.

## Required behavior

The user starts from the list of registered Tools.

The list exposes enough information to distinguish candidates, including:

- type;
- reference;
- lot;
- quantity;
- compatible machines/lines;
- process;
- state.

`quantity` is Tool-owned data: the number of physical tools represented by that canonical Tool/lot. It is display/operational data, not identity.

Example:

```text
BQ · lot 4 · quantity 120
```

For BQ Tools, Boquilhas consumes this quantity from Ferramentas when it needs the accounted lot total for its repair-movement projections.

The user may filter the list by the applicable operational dimensions.

For process, the current values are `NNPB` and `PS`.

Filtering follows the global surgical-query rule:

```text
filter/search criteria
→ frontend sends criteria to Ferramentas backend
→ backend applies criteria to the Tool-registry query
→ backend returns only the matching Tool rows/read model
→ frontend renders the table
```

The frontend may keep filter controls locally, but it must not load the complete Tool registry simply to hide non-matching rows in JavaScript.

Filters only narrow the candidate set. They never infer or auto-select the correct Tool, including when only one candidate remains.

Selection is always an explicit human action.

## Result

When consultation happens inside an originating workflow, explicit selection returns the selected canonical `tool_id` to that workflow.

No new production context identity is created by consultation or selection.
