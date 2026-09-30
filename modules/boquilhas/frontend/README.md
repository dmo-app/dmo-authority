# Boquilhas — Frontend Contract

This directory translates the module's decided functional behavior into explicit interface contracts **before design and implementation**.

It defines what each interface function must receive, show, collect and request. Visual composition remains owned by `dmo-app/dmo-design`.

## Functional sources

- `../OVERVIEW.md`
- `../REGISTO.md`
- `../MOVIMENTOS.md`
- `../HISTORICO.md`
- `../DEFINICOES.md`

## Core interaction rule

The frontend carries the current operation context, but it does not become a duplicate domain store.

```text
frontend
→ receives the minimum read model required by the function
→ keeps only the local interaction/draft state it needs
→ sends the operation anchor + explicit user choice + newly entered facts

backend
→ validates the anchor
→ resolves server-owned context
→ performs the operation
→ returns the next purpose-specific result/read model
```

Visible text is not identity. Canonical identity is carried by the appropriate stable ID/context supplied by the backend.

## Contract required before design or implementation

For every page/function in this module, document:

1. **User goal** — what the person is trying to accomplish.
2. **Entry context** — how the function is reached and which stable identity/context it receives.
3. **Read model** — exactly what the interface needs to render.
4. **Displayed facts** — including which are live, historical, derived or read-only.
5. **User inputs/choices** — facts genuinely entered or explicitly selected here.
6. **Local state** — unsaved draft/interaction state that belongs only to the interface.
7. **Backend action** — the operation invoked and the minimal request payload.
8. **Do not send** — server-resolvable/copied facts that must not be echoed back as truth.
9. **UI states** — loading, empty, validation, refusal/conflict, unavailable and success states where applicable.
10. **Navigation/return behavior** — including preservation of unsaved state when a contextual selection flow temporarily leaves the function.
11. **Permissions/capability behavior** — what the user may see or invoke; the frontend never grants access by itself.
12. **Design handoff** — the functional regions/actions/states that `dmo-app/dmo-design` must represent visually.

## Frontend boundary

The frontend must not:

- mint canonical IDs;
- infer identity from labels/reference/lot text;
- duplicate backend-owned business calculations;
- invent relationships because two facts appear on the same page;
- send actor/timestamp as authoritative facts;
- turn warnings into decisions unless the functional workflow explicitly defines that behavior;
- send a large copied production/module object when a stable anchor lets the backend resolve the required context.

## Function-to-design trace

Each designed interaction should be traceable to a named function in this module's functional/frontend contract. If the designer needs an action, state or association that is not defined here, close that functional question first instead of inventing behavior in the prototype.

## Status

This file establishes the frontend-contract structure only. It does **not** declare pages, routes, components or visual patterns that have not yet been explicitly decided.
