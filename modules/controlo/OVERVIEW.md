# Controlo — Common Context

Controlo is one operational domain exposed through two capability surfaces:

```text
Controlo
├── Controlo Create
└── Controlo Approve
```

Create and Approve are not separate product domains. They are different operational capabilities over shared production/control context.

This directory contains only the rules that are genuinely common to both surfaces.

## Production relationship

Controlo operates in the context of a Job On production.

The production already provides the identities required to reach the selected component contexts:

- `jobon_id`;
- `cm_id`;
- `mf_id`;
- `bq_id`.

Individual Controlo functions keep their own real identities and persistence boundaries.

Examples include:

- `peso_id`;
- `comparacao_id`;
- the durable records required by Folha or Pegamentos where applicable.

UI grouping under Controlo does not, by itself, make all of these children of one generic parent.

## `controlo_id` — planned evolution

`controlo_id` is an evolution planned for the shared Controlo production context where a durable Controlo-level identity is genuinely required.

It is not permission to rewrite every existing Controlo relation.

When introduced, it must represent the Controlo context for a production and provide a truthful home for facts that belong to Controlo as a shared production context rather than to:

- Job On;
- a canonical Tool;
- one component context;
- Peso;
- Comparação;
- another individual Controlo function.

Its exact implementation and migration boundary must be designed against the existing working model before schema changes are made.

Until that implementation is performed, documentation must not pretend that all current records already use `controlo_id`.

## What `controlo_id` must not become

It must not become:

- a replacement for `jobon_id`;
- a replacement for `cm_id`, `mf_id` or `bq_id`;
- a replacement for function-specific identities such as `peso_id` or `comparacao_id`;
- a generic parent added only because several screens appear under the Controlo navigation;
- a shortcut introduced solely to avoid query traversal.

## Resumo

Resumo is a read/composition surface.

There is no canonical persisted `resumo_id`.

Resumo may compose the state of several Controlo functions for one production, but that read composition does not take ownership of those records.

`controlo_id` and Resumo are therefore different concepts:

```text
controlo_id
→ planned durable shared Controlo production context where justified

Resumo
→ derived read/document composition
```

## Create / Approve boundary

Controlo Create owns create-side operational actions such as measurement, editing and submission where the workflow uses submission.

Controlo Approve owns review and approval-side decisions where the workflow is approvable.

The same underlying record identity persists across both surfaces. Approval must not create a duplicate record simply because it occurs in another capability surface.

## Human decisions

Measurements, calculations, alerts and technical OK/NOK states may inform a person.

They must not silently become human approval/rejection decisions unless the specific workflow explicitly defines such behavior.

## Related authority

Create-side details:

- `../controlo-create/OVERVIEW.md`

Approve-side details:

- `../controlo-approve/OVERVIEW.md`

Production context:

- `../job-on/OVERVIEW.md`

Canonical identities:

- `../../IDENTITIES.md`
