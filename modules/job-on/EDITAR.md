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

Where a requested change would invalidate or conflict with already-created dependent records, the implementation must handle that dependency explicitly rather than pretending the change is harmless.

The exact allowed edit boundary is determined by the implemented dependency rules; it must not be inferred from UI convenience.
