# Module Blueprint Index

The fast application map is in `../HOW_THE_APP_WORKS.md`.

This directory contains the DMO application documentation organized by real module.

The normal pattern is:

```text
HOW_THE_APP_WORKS.md
→ global app context

modules/<module>/*.md
→ functional/product behavior

modules/<module>/backend/*.md
→ deeper backend contracts and technical detail

modules/<module>/frontend/*.md
→ deeper frontend states, interactions and technical integration
```

Backend/frontend folders do not define separate products. They are technical views of the same module.

## Admin

`admin/`

Functional:
- `OVERVIEW.md`
- `USERS.md`
- `TEMPLATES.md`
- `APP_DEFINICOES.md`
- `SETUP.md`

Backend:
- `backend/ACCESS_TEMPLATES.md`
- `backend/SETUP_PROVIDER_CONNECTION.md`

Admin has no standalone Modules tab.

## Ferramentas

`ferramentas/`

Functional:
- `OVERVIEW.md`
- `CONSULTAR.md`
- `CRIAR.md`
- `SELECIONAR.md`
- `VALORES_TECNICOS.md`

Backend:
- `backend/VALORES_TECNICOS.md`

## Job On

`job-on/`

Functional:
- `OVERVIEW.md`
- `CRIAR.md`
- `CONSULTAR.md`
- `EDITAR.md`
- `DUPLICAR.md`
- `SELECIONAR_FERRAMENTAS.md`
- `CONTEXT_CHANGE_AWARENESS.md`

Backend:
- `backend/DUPLICAR.md`
- `backend/CONTEXT_CHANGE_AWARENESS.md`

## Controlo — shared context

`controlo/`

Functional:
- `OVERVIEW.md`

Backend:
- `backend/CONTEXT.md`
- `backend/FOLHA.md`

Shared Controlo technical detail must not turn `controlo_id` into a generic parent for every Controlo record.

## Controlo Create

`controlo-create/`

Functional:
- `OVERVIEW.md`
- `RESUMO.md`
- `PESO.md`
- `PESO_PDF.md`
- `COMPARACAO.md`
- `PEGAMENTOS.md`
- `FOLHA.md`
- `DEFINICOES.md`
- `DOCUMENTS.md`

Backend:
- `backend/PESO_HISTORICAL_DIFFERENCE.md`
- `backend/PESO_TECHNICAL_VALUES.md`
- `backend/PEGAMENTOS.md`

Frontend:
- `frontend/COMPARACAO.md`

## Controlo Approve

`controlo-approve/`

Functional:
- `OVERVIEW.md`
- `RESUMO.md`
- `APROVAR.md`
- `HISTORICO.md`

## Boquilhas

`boquilhas/`

Functional source:
- `BOQUILHAS.md`

Backend/frontend material may add technical detail only when needed and must not duplicate or redefine the functional rules in `BOQUILHAS.md`.

## Cross-cutting technical material

- `../backend/FOUNDATION_RUNTIME.md` — application-wide backend/runtime gaps and verification.
- `../prototype/backend/FAKE_BACKEND.md` — prototype-only fake backend support; never production backend authority.

## File rule

A module should have one consolidated functional source by default.

Backend/frontend files may add implementation or presentation contracts when needed, but must not become competing copies of functional truth.

Current implementation state belongs in `../IMPLEMENTATION_STATUS.md`.

Do not infer missing product behavior from technical implementation convenience, historical code or prototype support.
