# DMO Authority — Index

This repository is the master project authority for DMO.

Read this repository to understand how the application must be created before changing product behavior, architecture, backend, frontend, persistence, identities, flows, or module behavior.

## Global authority

- `HOW_THE_APP_WORKS.md` — global end-to-end explanation of the application.
- `IDENTITIES.md` — canonical identities and identity boundaries.
- `RULES.md` — cross-cutting product and architecture rules.
- `SCOPE.md` — Beta scope.
- `OPEN_DECISIONS.md` — genuinely unresolved decisions only.
- `AUTHORITY.md` — authority/governance boundary.
- `README.md` — repository entry point.

## Detailed module authority

Module detail is organized under `modules/`.

- `modules/job-on/`
- `modules/ferramentas/`
- `modules/controlo-create/`
- `modules/controlo-approve/`
- `modules/boquilhas/`
- `modules/admin/`

See `modules/INDEX.md` for the task-level map.

## Reading rule

Start with `HOW_THE_APP_WORKS.md` for the global model.

Then read the relevant module folder. A module file should contain the complete detail needed for that task: frontend behavior, backend behavior, identities, relations, inputs, writes, reads, validations, history, integrations, and implementation status where relevant.

Do not infer missing behavior.

Legacy labels such as Operador, Responsável, and Controlador are not current authorization authority. Preserve the underlying functional action, but express current behavior through the current module/capability model.

The detailed module files will be populated progressively from verified current knowledge. Empty or placeholder task files must not be treated as product authority until populated.
