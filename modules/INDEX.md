# Module Blueprint Index

The fast application map is in `../HOW_THE_APP_WORKS.md`.

This directory contains the canonical detailed explanation of each application area.

The normal context pattern for a person or AI is:

```text
HOW_THE_APP_WORKS.md
→ understand the app and main relationships

modules/<relevant-module>/
→ load the detailed canonical rules needed for the current task

features-to-implement/<associated-slice>.md
→ when implementing, load the bounded construction work tied to that module
```

`features-to-implement/` is part of normal implementation context, not a detached optional backlog. It never overrides the module canon.

Do not load unrelated module detail merely because it exists.

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


## Implementation-slice associations

The canonical module documents above own product behavior. The following implementation files are associated work packages only; they never override the module canon.

```text
Admin
→ ACCESS_TEMPLATES_BLUEPRINT_COMPLETION.md
→ SETUP_MODE_PROVIDER_CONNECTION.md

Ferramentas
→ TOOL_TECHNICAL_VALUES_IMPLEMENTATION.md
→ PESO_TECHNICAL_VALUES_ALIGNMENT.md (consumer alignment)

Job On
→ JOB_ON_DUPLICATION_ALIGNMENT.md
→ JOB_ON_CONTEXT_CHANGE_AWARENESS.md
→ CONTROLO_CONTEXT_IMPLEMENTATION.md (creation-time Controlo association)

Controlo shared
→ CONTROLO_CONTEXT_IMPLEMENTATION.md
→ FOLHA_PERSISTENCE_IMPLEMENTATION.md

Controlo Create
→ PESO_HISTORICAL_DIFFERENCE.md
→ PESO_TECHNICAL_VALUES_ALIGNMENT.md
→ COMPARACAO_UI_COMPLETION.md
→ PEGAMENTOS_BACKEND_IMPLEMENTATION.md
→ FOLHA_PERSISTENCE_IMPLEMENTATION.md

Boquilhas
→ BOQUILHAS_TRACE_IMPLEMENTATION.md
→ JOB_ON_CONTEXT_CHANGE_AWARENESS.md (consumer behavior)

Prototype / development support
→ PROTOTYPE_FAKE_BACKEND_REWORK.md

Runtime/status verification
→ FOUNDATION_RUNTIME_GAPS.md
```

For implementation work, load the relevant entries above as part of the module context rather than waiting until a missing feature is discovered accidentally.

Always read `../features-to-implement/README.md` before treating one of these files as a Developer contract. Some remain recovery/alignment drafts and some are explicitly BLOCKED. The module canon remains authoritative.

## Templates

Access Templates are defined in `admin/TEMPLATES.md`.

They connect normal Users to configured modules/capabilities/permissions.

Email templates used by Controlo remain module-owned configuration and must not be merged with Access Templates merely because both use the word "template".

## File rule

Each detailed file should be self-contained enough to implement or review that area without loading the entire blueprint repository.

Where another module is involved, include only the minimum cross-module context and link to the owning module for deeper detail.

Do not use historical role titles as current authorization identities.

Do not promote implementation accidents, historical schemas or speculative identities into current product rules merely because they appear in older documentation.
