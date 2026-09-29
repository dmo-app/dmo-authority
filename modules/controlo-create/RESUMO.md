# Controlo Create — Resumo

Resumo is a function/surface inside Controlo.

## Persistence model

`resumo_id` does not exist.

Resumo is:
- a read composition;
- a derived dashboard/document surface;
- not a persisted parent;
- not a foreign-key anchor;
- not an independent canonical identity.

Peso, Comparação, Pegamentos and Folha do not acquire a `resumo_id` parent because Resumo displays or composes them.

## UI role

The Controlo experience may expose:

```text
Controlo
→ Resumo
→ generate the Folha/Resumo document when applicable
```

Exact navigation remains a UX concern. It does not change the persistence model.

Resumo must not be confused with `controlo_id`: the latter is the durable Controlo context identity for a production; Resumo is a derived reading/composition over real records and context.
