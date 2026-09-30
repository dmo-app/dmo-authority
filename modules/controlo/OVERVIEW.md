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

## Future Job On preparation context

A successfully created Job On immediately receives its associated canonical `controlo_id`.

That association belongs to the production context itself:

```text
jobon_id A ↔ controlo_id A
jobon_id B ↔ controlo_id B
```

The association is stable, but the Resumo surface is not locked to one Job On/Controlo context. The operator may navigate between productions and their respective `controlo_id` contexts.

A successfully saved future Job On is therefore immediately available to Controlo as a selectable preparation context.

Controlo does not need to wait for that production to become the current machine production before the operator can inspect or prepare its Resumo context.

```text
Job On saved
→ jobon_id exists
→ associated controlo_id already exists
→ Controlo Resumo can list that production
→ user explicitly selects the production/context
→ backend resolves the existing jobon_id ↔ controlo_id association
→ preparation may begin where the owning Controlo workflow allows it
```

This is an explicit read/context selection, not automatic activation.

```text
future jobon_id selectable in Controlo
!=
machine production already changed
```

Controlo receives no duplicated production snapshot. Its selection points to the persisted `jobon_id`, and the backend resolves the required current Job On context through the normal focused-read rules.

This early preparation behavior is Controlo-specific. It must not be generalized so that Boquilhas or another operational module adopts a future Job On before its own real transition rule applies.

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

## `controlo_id` — canonical Controlo production context

`controlo_id` is the canonical functional identity of the shared Controlo production context where durable Controlo-level facts belong.

Each Job On creates and associates its `controlo_id` as part of Job On creation. Controlo therefore has an existing context before Peso, Folha, Pegamentos or another Controlo record is created.

The exact technical representation may still be chosen during implementation, but the functional lifecycle is fixed:

```text
create Job On
→ create jobon_id
→ create associated controlo_id
```

It is not permission to rewrite every existing Controlo relation.

It provides a truthful home for facts that belong to Controlo as a shared production context rather than to:

- Job On;
- a canonical Tool;
- one component context;
- Peso;
- Comparação;
- another individual Controlo function.

Its exact implementation boundary must preserve the ownership rules of the individual Controlo functions. Existing function-specific records do not become children of `controlo_id` merely for navigation convenience.

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
→ canonical durable shared Controlo context created with and associated to one Job On

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
