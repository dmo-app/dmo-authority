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

## Shared state surfaces — Folha and Resumo

Folha and Resumo are shared Controlo surfaces over the same production/control state.

They are **not duplicated into separate Create and Approve copies**.

The capability changes what the user may do; it does not create a second Folha, a second Resumo, or a second set of control facts.

Conceptually:

```text
same Controlo production/control state
        │
        ├── Controlo Create
        │   ├── may create/edit the operational control facts it owns
        │   ├── may edit/evaluate/submit Folha where applicable
        │   └── sees Resumo composed from the current control state
        │
        └── Controlo Approve
            ├── reads the same Folha/state
            ├── reads the same Resumo/state
            └── may write approval decisions/history only
```

When Create changes an operational fact or edits Folha, the approval surface must read the updated state through the same underlying records/relations.

There is no synchronization-by-copy step from Create to Approve.

### Approve read-only boundary

Controlo Approve must not edit the operational control content merely because it can see it.

In particular, the approval capability must not modify the Create-owned measurements, Folha fields, observations, technical values or other operational facts while reviewing them.

Approve may persist only the approval-side facts defined by the approval workflow, such as:

- approve;
- reject;
- reopen where permitted;
- required decision reason/context;
- actor;
- timestamp;
- decision history.

Those approval facts do not turn the approval surface into an editor of the underlying operational content.

### Resumo is the same composition on both capabilities

Resumo is one read/composition concept over the current Controlo state.

Create and Approve may expose different controls around that read because their capabilities differ, but they must not derive two conflicting Resumo truths.

A Create-side change that affects the composed control state must be visible when Approve reads Resumo.

## `controlo_id` — canonical shared Controlo identity

`controlo_id` identifies the durable shared Controlo production context where facts genuinely owned by Controlo at production level belong.

It is not permission to rewrite every other Controlo relation or to make every record a child of one generic parent.

The technical contract for `controlo_id` must provide a truthful home for facts that belong to the shared Controlo production context rather than to:

- Job On;
- a canonical Tool;
- one component context;
- Peso;
- Comparação;
- another individual Controlo function.

Its technical representation must be the minimum representation that preserves this identity and ownership boundary. Existing truthful relation chains remain valid and are not duplicated merely to route them through `controlo_id`.

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
→ canonical durable shared Controlo production context

Resumo
→ derived read/document composition
```

## Create / Approve boundary

Controlo Create owns create-side operational actions such as measurement, editing and submission where the workflow uses submission.

Controlo Approve owns review and approval-side decisions where the workflow is approvable.

The same underlying record identity persists across both surfaces. Approval must not create a duplicate record simply because it occurs in another capability surface.

Visibility is not edit ownership:

```text
Approve can see Create-owned operational state
!=
Approve can edit that operational state
```

## Human decisions

Measurements, calculations, alerts and technical OK/NOK states may inform a person.

They must not silently become human approval/rejection decisions unless the specific workflow explicitly defines such behavior.

## Related blueprint references

Create-side details:

- `../controlo-create/OVERVIEW.md`

Approve-side details:

- `../controlo-approve/OVERVIEW.md`

Production context:

- `../job-on/OVERVIEW.md`

Canonical identities:

- `../../IDENTITIES.md`
