# Controlo Create — Resumo

Resumo is a shared read/composition surface inside Controlo.

The Resumo exposed through Controlo Create and Controlo Approve is the same functional composition over the same underlying production/control state.

There is not a separate persisted "Create Resumo" and "Approve Resumo".

## Persistence model

`resumo_id` does not exist.

Resumo is:
- a read composition;
- a derived dashboard/document surface;
- not a persisted parent;
- not a foreign-key anchor;
- not an independent canonical identity.

Peso, Comparação, Pegamentos and Folha do not acquire a `resumo_id` parent because Resumo displays or composes them.

## Shared-state behavior

Create-side changes to the underlying Controlo state must be reflected when either capability reads Resumo.

Conceptually:

```text
Create changes a real underlying control record
-> Resumo composition changes
-> Approve sees that updated composition
```

This does not require copying or synchronizing a second Resumo record.

Controlo Approve may read the composed information but does not gain edit ownership over the underlying Create-owned operational facts merely because those facts are visible in Resumo.

## UI role

The Controlo experience may expose:

```text
Controlo
→ Resumo
→ generate the Folha/Resumo document when applicable
```

Exact navigation remains a UX concern. It does not change the persistence model.

Resumo must not be confused with `controlo_id`: the latter is the durable Controlo context identity for a production; Resumo is a derived reading/composition over real records and context.
