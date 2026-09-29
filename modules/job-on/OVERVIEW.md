# Job On — Overview

Job On represents one concrete production occurrence.

Its canonical identity is `jobon_id`.

Job On owns the production context: reference, production number, machine, production date, selected Tools and the production-specific configuration that belongs to that run.

It does not own Tool master data, quality-control measurements, approval decisions or Boquilhas repair history.

## Capabilities

Job On has two access capabilities:

- **Job On View** — consultation only;
- **Job On Create** — creation/editing capability and the viewing behavior required to perform that work.

These are module capabilities, not legacy role titles.

Inside Job On Create, an existing Job On normally opens in a safe View state and requires an explicit switch to Edit before editable values are changed. This UI state is not a separate permission.

## Production context

A Job On selects canonical Tools from Ferramentas.

For the production, the application establishes:

```text
jobon_id
├── cm_id → tool_id
├── mf_id → tool_id
└── bq_id → tool_id
```

The component contexts belong to that production occurrence and preserve the required production-time Tool context.

The same canonical Tool may be used in several Job Ons. Each production receives its own component context identity.

## Downstream use

Controlo and other operational modules consume the production context established by Job On.

They must not silently reinterpret or replace the selected Tool identities.

Detailed flows:

- `CRIAR.md`
- `CONSULTAR.md`
- `EDITAR.md`
- `DUPLICAR.md`
- `SELECIONAR_FERRAMENTAS.md`
