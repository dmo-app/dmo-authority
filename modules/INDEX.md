# Module Blueprint Index

The fast application map is in `../HOW_THE_APP_WORKS.md`.

This directory contains the canonical detailed explanation of each application area.

The normal context pattern for a person or AI is:

```text
HOW_THE_APP_WORKS.md
→ understand the app and main relationships

modules/<relevant-module>/
→ load the detailed rules needed for the current task
```

Do not load unrelated module detail merely because it exists.

## Functional vs technical contract structure

Each module keeps its product behavior in the existing module files and its implementation-facing contracts in two subdirectories:

```text
modules/<module>/
├── <functional files>.md
├── backend/
│   └── README.md
└── frontend/
    └── README.md
```

- **functional files** define what the module means and how it behaves;
- **`backend/`** defines server interaction, anchors, reads, writes, validation, persistence/concurrency boundaries and published cross-module contracts;
- **`frontend/`** defines page/function context, read needs, user inputs, request payloads, interface states, navigation and the handoff to `dmo-app/dmo-design`.

The technical folders must not duplicate or silently change functional rules. They make decided behavior precise enough to design and implement.

The standing interaction principle is:

```text
frontend sends the minimum truthful anchor + intent + new facts
backend resolves only the context required for that operation
backend returns only the purpose-specific result/read model required
```

Do not invent endpoints, schema or UI behavior merely to fill these folders. Concrete technical contracts are added as each module is prepared for implementation.

## Admin

`admin/`

- `OVERVIEW.md`
- `USERS.md`
- `TEMPLATES.md`
- `APP_DEFINICOES.md`
- `SETUP.md`

Admin has no standalone Modules tab.

Modules appear inside Access Templates for permission configuration and inside App Definições as the selected owner of administrative settings. Those are different concerns.

## Ferramentas

`ferramentas/`

- `OVERVIEW.md`
- `CONSULTAR.md`
- `CRIAR.md`
- `SELECIONAR.md`
- `VALORES_TECNICOS.md`

## Job On

`job-on/`

- `OVERVIEW.md`
- `CRIAR.md`
- `CONSULTAR.md`
- `EDITAR.md`
- `DUPLICAR.md`
- `SELECIONAR_FERRAMENTAS.md`
- `CONTEXT_CHANGE_AWARENESS.md`

## Controlo — shared context

`controlo/`

- `OVERVIEW.md`

This directory contains only rules genuinely shared across Controlo Create and Controlo Approve.

It includes the canonical `controlo_id` functional identity and the still-pending technical implementation boundary. The existence of this shared context must not be interpreted as permission to make `controlo_id` a generic parent for every Controlo record.

Folha and Resumo are shared Controlo surfaces over the same underlying production/control state:

```text
Controlo Create
→ may edit/write the operational state allowed by its workflows

Controlo Approve
→ reads that same state
→ writes approval decisions/history only
→ does not edit the operational content
```

Create and Approve therefore do not own separate copies of Folha or Resumo.

## Controlo Create

`controlo-create/`

- `OVERVIEW.md`
- `RESUMO.md`
- `PESO.md`
- `COMPARACAO.md`
- `PEGAMENTOS.md`
- `FOLHA.md`
- `DEFINICOES.md`
- `DOCUMENTS.md`

## Controlo Approve

`controlo-approve/`

- `OVERVIEW.md`
- `RESUMO.md`
- `APROVAR.md`
- `HISTORICO.md`

## Boquilhas

`boquilhas/`

- `OVERVIEW.md`
- `REGISTO.md`
- `MOVIMENTOS.md`
- `HISTORICO.md`
- `DEFINICOES.md`

The existing Boquilhas register/movement model is the implementation base.

The canonical Boquilhas model uses one `bq_repair_trace_id` for each `bq_id` / production BQ context, with many movements inside that trace. A pre-production trace may temporarily exist from canonical `tool_id` with `bq_id` unresolved and later associate to the matching production context according to the Boquilhas rules.

## Templates

Access Templates are defined in `admin/TEMPLATES.md`.

They connect normal Users to configured modules/capabilities/permissions.

Email templates used by Controlo remain module-owned configuration and must not be merged with Access Templates merely because both use the word "template".

## File rule

Each detailed file should be self-contained enough to implement or review that area without loading the entire blueprint repository.

Where another module is involved, include only the minimum cross-module context and link to the owning module for deeper detail.

Do not use historical role titles as current authorization identities.

Do not promote implementation accidents, historical schemas or speculative identities into current product rules merely because they appear in older documentation.
