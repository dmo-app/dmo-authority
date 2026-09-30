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

## Production-context selection

Resumo may be used to select a saved Job On context explicitly, including a future Job On that has already been created but has not yet become the current machine production.

```text
saved future Job On
→ appears as a Resumo production/context candidate
→ user explicitly selects it
→ jobon_id becomes the active Resumo/preparation context
```

The selector is a focused read over Job On planning data. It does not copy Job On production facts into Resumo and does not create `resumo_id`.

If several Job Ons are relevant, the system shows the candidates and the user chooses the intended `jobon_id`. The frontend must not silently activate one.

Selecting a future Job On in Resumo is preparation only. It does not tell Boquilhas or another operational module that production has already changed.

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
