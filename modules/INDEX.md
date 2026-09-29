# Module Authority Index

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

## Admin

`admin/`

- `OVERVIEW.md`
- `SETUP.md`

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

## Controlo — shared context

`controlo/`

- `OVERVIEW.md`

This directory contains only rules genuinely shared across Controlo Create and Controlo Approve.

It includes the planned `controlo_id` evolution where applicable. The existence of this shared context must not be interpreted as permission to make `controlo_id` a generic parent for every Controlo record.

## Controlo Create

`controlo-create/`

- `OVERVIEW.md`
- `RESUMO.md`
- `PESO.md`
- `COMPARACAO.md`
- `PEGAMENTOS.md`
- `FOLHA.md`
- `DEFINICOES.md`

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

Saldo/discrepancy is an evolution of that movement behavior. A separate `bq_repair_trace_id` is not a current requirement and must not be inferred merely from older authority text.

## Templates

Template-related behavior is documented only where a concrete owning workflow is already defined.

Access templates belong to the access/admin model.

Email templates used by Controlo belong to the relevant Controlo configuration workflow.

Do not invent a separate Templates product domain merely from the shared word "template" without an explicit product requirement.

## File rule

Each detailed file should be self-contained enough to implement or review that area without loading the entire authority repository.

Where another module is involved, include only the minimum cross-module context and link to the owning module for deeper detail.

Do not use historical role titles as current authorization identities.

Do not promote implementation accidents, historical schemas or speculative identities into product authority merely because they appear in older documentation.
