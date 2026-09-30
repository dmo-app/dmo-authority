# Boquilhas — Backend Contract

This directory translates the module's decided functional behavior into explicit server-side contracts **before implementation**.

It does not replace the functional files. Functional meaning stays in the owning module documents; this layer records how the backend must expose, resolve, validate and persist that behavior.

## Functional sources

- `../OVERVIEW.md`
- `../REGISTO.md`
- `../MOVIMENTOS.md`
- `../HISTORICO.md`
- `../DEFINICOES.md`

## Core interaction rule

For each backend operation, prefer the smallest truthful contract:

```text
request
= operation intent
+ nearest real context/record anchor
+ facts newly supplied by the user
+ observed version when concurrency protection is required

backend
= validate anchor and authorization
+ resolve only the context required by the operation
+ read only the required persisted relations
+ allocate backend-owned identities / actor / time
+ perform the write or compose the read
+ return a purpose-specific result/read model
```

A client must not resend production, Tool, account or other server-resolvable facts merely because they are visible on screen.

## Contract required before implementation

For each operation implemented in this module, document:

1. **Intent** — the exact operation being requested.
2. **Anchor** — the nearest stable identity/context that truthfully anchors it.
3. **Client-supplied facts** — only facts genuinely entered/chosen for this operation.
4. **Server-resolved context** — facts reached through existing identities/relations.
5. **Server-generated facts** — canonical IDs, actor, timestamps and other backend-owned facts.
6. **Reads** — the minimum traversals/query data required.
7. **Writes** — records changed, transaction boundary and durable relations created.
8. **Concurrency** — whether an observed version is required and what a stale write does.
9. **Result/read model** — the minimum response required by the consuming function.
10. **Validation/refusals** — meaningful failure cases and whether they are validation, not-found, conflict or another defined outcome.
11. **Cross-module dependency** — which published identity/contract is consumed and which module owns it.
12. **Non-implications** — nearby behavior this operation must not invent.

## Module boundary

A dependency on another module does not transfer ownership.

When this module needs facts owned elsewhere, the contract must identify the stable identity or published read/operation used to reach them. Do not create duplicate facts, convenience identities or direct cross-module ownership merely to simplify one query.

If a future module needs something this module does not yet expose, extend the owning module's contract first, verify its existing behavior, and only then consume the new contract.

## Status

This file establishes the technical-contract structure only. It does **not** declare endpoints, schema shapes, DTOs or implementation choices that have not yet been explicitly decided.
