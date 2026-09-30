# Job On — Editar

Editing belongs to Job On Create.

An existing Job On opens in a safe View state and requires an explicit user action to enter Edit state before editable values are changed.

## Identity

Editing mutates the same `jobon_id`.

It does not create a replacement production merely because one field changed.

## Context safety

Changes must preserve the distinction between:

- canonical Tool identity in Ferramentas;
- Tool selection/context for this Job On;
- downstream records already created from the production context.

Editing must not silently rewrite historical downstream facts.

If a selected CM, MF or BQ Tool is replaced inside the same production:

```text
jobon_id = unchanged

<old_context_id> -> <old_tool_id>
<new_context_id> -> <new_tool_id>
```

The previous `cm_id`, `mf_id` or `bq_id` remains attached to the Tool it originally represented. It is never retargeted to the replacement `tool_id`.

The replacement receives a new context identity so records that already reference the previous context remain historically truthful.

A lot change is a common example of this rule because a different lot is a different canonical Tool:

```text
same jobon_id

old cm_id
→ tool_id A / lot 001

operator selects Tool for lot 002

new cm_id
→ tool_id B / lot 002

old cm_id remains historical
```

The implementation must not edit `tool_id A` so that lot 001 becomes lot 002, and it must not retarget the old `cm_id` to `tool_id B`.

### Context-change awareness

A relevant production-context edit also follows the lightweight awareness rule:

```text
change
-> permanent Job On change log
-> lightweight awareness for consumers of that context
-> module acknowledgement when seen
```

Acknowledgement means only that the change was seen. It does not resolve, correct, recalculate, approve or rewrite downstream work.

The awareness mechanism is defined in `CONTEXT_CHANGE_AWARENESS.md`.

Where a requested change would otherwise invalidate or conflict with already-created dependent records, the implementation must preserve those historical facts rather than pretending the change is harmless.

The exact allowed edit boundary beyond this awareness mechanism is determined by the applicable dependency rules; it must not be inferred from UI convenience.
