# DMO — Global

DMO is an operational application for glass-container manufacturing.

This file contains only rules that genuinely apply across modules. Module-specific truth belongs in the owning module.

## Module map

```text
modules/
├── global/
├── admin/
├── ferramentas/
├── job-on/
├── controlo/
└── boquilhas/
```

The current operational model is:

```text
Admin
→ users, access templates, application settings

Ferramentas
→ canonical Tool registry

Job On
→ production occurrence and selected Tool contexts

Controlo
→ Create: Peso, Pegamentos, Folha, Resumo, Comparação
→ Approve: approval of submitted Peso only

Boquilhas
→ BQ repair movements and local history
```

Each module owns its own facts. Consuming another module's context does not transfer ownership.

## Core modelling principle

Persist real operational facts and durable relationships.

Compose the context required by a screen or operation when it is read.

Do not create new identities, foreign keys, snapshots, catch-all parents or duplicated facts merely because one UI/query would be more convenient.

```text
READ MODEL != ENTITY
QUERY JOIN != DOMAIN RELATION
UI GROUPING != DATABASE OWNERSHIP
DTO / PROJECTION != DOMAIN IDENTITY
```

A screen needing related information is normally a read problem, not evidence that the write model needs another persistent relation.

## Identity principle

Canonical identities are owned by the module/process that creates the real durable thing or event.

Examples include:

- `tool_id` — Ferramentas;
- `jobon_id`, `cm_id`, `mf_id`, `bq_id` — Job On production context;
- `controlo_id`, `peso_id`, `comparacao_id` — Controlo according to their defined lifecycle;
- `bq_repair_trace_id`, `movement_id` — Boquilhas.

The detailed meaning/lifecycle of each identity belongs in its module document.

Do not create an ID for every named concept. A new identity needs a real durable entity/event, lifecycle, multiplicity, ownership or concurrency reason.

Derived/read concepts do not gain identities merely because they have a screen or document. For example, Resumo has no `resumo_id`.

## Explicit human choice

DMO must not silently infer operational decisions that a person is expected to make.

This includes, where applicable:

- selecting a Tool;
- selecting a Job On/production candidate;
- selecting a historical Peso;
- approval/not-approval decisions;
- operational decisions such as `Manter` / `Colocar de parte`.

Filters, ranking, warnings and calculated results may assist the user. They do not become the decision.

Even when only one candidate remains, the application must not silently associate it where the workflow requires explicit choice.

## Warnings are not decisions

An informational warning must not silently become:

- a backend refusal;
- an approval/rejection;
- an automatic replacement;
- a production-stop decision;
- a hidden eligibility rule;

unless the owning module explicitly defines such behavior.

## Access model

Operational access is resolved through:

```text
User
→ current Access Template or none
→ module/capability permissions
→ backend-authorized operation
```

Template names, historical role labels, page labels, hidden buttons and URLs do not grant access.

ADMIN is an administration identity/surface and does not implicitly receive every operational permission.

Module documents define their own capabilities. For example, Controlo is one module with Create and Approve capabilities, and Approve applies to submitted Peso only.

## Historical truth

A later configuration/master-data/context change must not rewrite an already recorded historical fact.

Use the real durable records/relations to preserve history.

Freeze an externally owned/current value into an operational record only when the exact value consumed at that moment is required for historical reproducibility.

A frozen historical value does not become a second editable owner.

Do not create a generic global History entity merely because several modules have history. Module-local history remains owned by the module that created those facts.

## Context and replacement

Production/context replacement must preserve historical truth.

When a module-specific identity has already been operationally consumed, replacement must not retarget old records to a different physical/context entity.

The owning module defines when an in-place planning edit is still safe and when a new durable context/event identity is required.

## Derived artifacts

Persisted records are different from generated artifacts and storage locations.

```text
record ≠ PDF ≠ filesystem path
```

Documents, exports and UI projections do not replace persisted operational truth and do not gain ownership merely because they combine facts from several sources.

## Scope boundary

Current DMO scope includes the modules documented under `modules/`.

The following are not part of the current operational scope unless deliberately added later:

- Armazém;
- Reparação Interna;
- Reparação Programada;
- Tampões as an independent module/identity system;
- full Tool lifecycle/usage-percentage features beyond the documented Ferramentas scope;
- a global Histórico product/module;
- MCaliper integration;
- Boquilhas PDF/email artifacts.

An out-of-scope concept must not leak back into current implementation merely because it existed historically or appears in old code/prototypes.

## Source-of-truth rule

The current module documents in this repository describe the current DMO truth.

Historical repositories, old implementations, backups, prototypes and reports may be evidence during investigation, but they do not override current module truth.

When a rule changes, update its owning module source. Do not keep a second contradictory version around as historical documentation inside the active source set.
