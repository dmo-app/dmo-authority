# Module Authority Index

The global application model remains in `../HOW_THE_APP_WORKS.md`.

This directory provides deeper authority by real module and task. Do not split one task into separate frontend/backend/flow files: each task document should remain vertically complete.

## Job On

`job-on/`

- `OVERVIEW.md`
- `CRIAR.md`
- `CONSULTAR.md`
- `EDITAR.md`
- `DUPLICAR.md`
- `SELECIONAR_FERRAMENTAS.md`

## Ferramentas

`ferramentas/`

- `OVERVIEW.md`
- `CONSULTAR.md`
- `CRIAR.md`
- `SELECIONAR.md`
- `VALORES_TECNICOS.md`

## Controlo

Controlo must also be documented as its own canonical domain context, not only through the UI/capability surfaces below.

This documentation must explain the persistent Controlo context and its identity (`controlo_id`), what facts belong to that context, how it relates to production and other canonical identities, and which rules are shared across Controlo Create and Controlo Approve.

The detailed Controlo domain module still needs to be added. It must not be reduced to a UI tab or treated as a generic parent merely because the UI groups functions under Controlo.

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

## Admin

`admin/`

- `OVERVIEW.md`
- `SETUP.md`

## File rule

Each detailed file must be self-contained enough that an implementation agent does not need to guess the missing half of the task.

Where another module is involved, repeat the minimum required context and then reference the related module document for deeper detail.

Do not use historical role titles as current authorization identities.
