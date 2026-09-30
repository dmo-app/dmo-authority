# Tool Technical Values — Implementation Slice

**Readiness:** BLOCKED — canonical ownership/identity is defined, but value representation details still need closure before persistence/UI implementation.

**Owning domain:** Ferramentas

**Canonical sources:**
- `modules/ferramentas/OVERVIEW.md`
- `modules/ferramentas/VALORES_TECNICOS.md`
- `IDENTITIES.md`

## Objective

Support optional reusable technical values owned by a canonical Tool without creating a second Tool identity or copying those values into consuming workflows.

The application must support this relationship:

```text
tool_id
  └── optional tool_technical_values
```

The extension exists only when one or more applicable technical values are registered.

## Canonical identity and anchor

Operation anchor:

```text
tool_id
```

The technical-values extension has **no independent canonical ID**.

It must not create:

- `technical_values_id`;
- another UUID;
- an independent lifecycle identity.

A separate persistence structure is allowed, but physical storage does not create another business identity.

## Confirmed Tool-owned values

The currently confirmed reusable Tool technical facts are:

- `volume_marisa`;
- `volume_puncao`;
- `diametro_gargalo`.

These values belong to the canonical Tool.

They are not Job On facts, Controlo facts, Peso measurements, Pegamentos results or production-specific configuration.

## Inputs

For a create/update of Tool technical values, the client may provide only the Tool-owned technical facts that the Ferramentas frontend contract allows the user to edit.

The client carries the existing canonical `tool_id`.

The client must not create or derive another technical-values identity.

For guarded edits, the final backend contract must also carry the applicable concurrency version according to the global concurrency rule.

## Backend resolves

The backend resolves:

- that `tool_id` exists;
- that the current account has the capability required to edit Tool-owned data;
- current persisted technical values for the Tool when editing;
- backend-owned audit/concurrency facts where the final persistence contract requires them.

The client must not supply actor/time as authority.

## Reads

Normal Ferramentas list/search remains light.

It must not automatically load specialized technical values for every Tool.

Technical values are read only when a surface or consuming workflow actually needs them.

Consumer path example:

```text
cm_id
→ tool_id
→ tool_technical_values
```

The consumer follows the real production-context relation to the canonical Tool.

## Writes

This slice persists only reusable technical facts owned by the Tool.

It must support:

- registering technical values for an existing canonical Tool;
- editing registered Tool technical values when the owning workflow permits it;
- leaving technical values absent when they are not known or not applicable.

Adding or editing technical values must never replace the Tool or create a new `tool_id`.

## Missing values

Missing technical values remain missing.

The backend and frontend must not:

- invent defaults that pretend to be real Tool facts;
- infer missing values from another Tool;
- create a replacement Tool merely because technical data is absent.

A consuming workflow that requires a missing value must follow that workflow's own explicit missing-data behavior.

This slice must not invent that consumer behavior.

## Consumers and ownership

Known consumers may include:

```text
Peso
→ Tool technical values required by its calculation

Pegamentos
→ diametro_gargalo where required

Comparação
→ diametro_gargalo where required
```

Consumption does not transfer ownership.

Do not persist a second mutable copy of these Tool facts inside Peso, Pegamentos or Comparação merely for query convenience.

Where a consuming workflow needs historical stability, that workflow must explicitly define which consumed value is frozen and why. This slice does not turn all current Tool values into historical snapshots automatically.

## Derived state

No new business identity, lifecycle state or duplicate aggregate is derived from the presence of technical values.

```text
technical values present
!=
new Tool

technical values absent
!=
invalid Tool
```

A Tool can exist canonically without the optional extension.

## Frontend contract requirements

The Ferramentas frontend contract must eventually define:

- where technical values are viewed;
- where an authorized user can add/edit them;
- field-level missing/optional presentation;
- validation feedback;
- loading/error/conflict states;
- how unsaved edits are preserved if the flow temporarily navigates elsewhere.

The normal Tool search/list must not become heavy merely to display these specialized values.

## Backend contract requirements

The Ferramentas backend contract must eventually define:

- read operation(s) by `tool_id`;
- create/update semantics for the optional extension;
- concurrency anchor/version behavior;
- typed refusals for missing Tool, invalid value and stale write;
- purpose-specific read model returned to Ferramentas;
- narrow consumer read contract(s) where another workflow needs one or more values.

## Out of scope

This slice does not define:

- Job On production configuration;
- Peso measurements/results;
- Pegamentos measurements/results;
- Comparação decisions/results;
- historical snapshot policy for every consumer;
- a generic metadata/value framework;
- another Tool identity.

## Missing product decisions blocking implementation

Before persistence and UI for this slice are considered READY, the owning blueprint must explicitly define enough representation rules to avoid implementation invention.

At minimum, close:

1. canonical unit for `volume_marisa`;
2. canonical unit for `volume_puncao`;
3. canonical unit for `diametro_gargalo`;
4. required numeric precision/accepted input precision for each value;
5. whether zero/negative values are valid or must be refused;
6. whether any of these values are Tool-type-specific rather than applicable to every Tool type;
7. which Ferramentas capability is allowed to edit these values, once the access catalogue is closed.

If any of these are already defined elsewhere, point this slice to that canonical source rather than duplicating the rule here.

## Acceptance evidence

When this slice becomes READY and is implemented, reviewers must be able to prove that:

- technical values are anchored only by the existing canonical `tool_id`;
- no `technical_values_id` or second Tool identity exists;
- a Tool may exist without technical values;
- adding technical values does not replace the Tool;
- normal Tool search/list does not require loading the extension;
- consumers obtain Tool-owned values through the canonical Tool relation;
- consumers do not become a second mutable owner of the same Tool facts;
- missing values are not invented;
- stale guarded edits are refused without silent overwrite;
- implementation matches the units/precision/validation rules closed in the canonical Ferramentas blueprint.
