# DMO Blueprint — Index

This repository is the canonical functional blueprint for DMO.

Read it to understand how the application must behave before changing product behavior, architecture, backend, frontend, persistence, identities, flows, or module behavior.

## Global blueprint map

- `HOW_THE_APP_WORKS.md` — global end-to-end explanation of the application.
- `IDENTITIES.md` — canonical identities and identity boundaries.
- `RULES.md` — cross-cutting product and architecture rules.
- `SCOPE.md` — Beta scope.
- `IMPLEMENTATION_STATUS.md` — current Beta implementation state, bugs, transitional conditions, and known gaps; not product canon.
- `README.md` — repository entry point.

There is no separate governance/definition file outside this map that an agent must discover before using the blueprint.

The blueprint contains decided product behavior only. Unresolved product questions are not kept as a canonical "open decisions" queue; once the owner decides them, the resulting rule is written directly into the file that owns that behavior.

## Features to implement

Planned or confirmed work that is not yet implemented is documented under `features-to-implement/`.

Each file states its own status and separates confirmed functional behavior from implementation work that is still pending.

A feature file is planning/review input. Its existence does **not** mean the feature already exists in `dmo-app-beta`.

See `features-to-implement/README.md` for the reading and lifecycle rules.

## Detailed module blueprint

Module detail is organized under `modules/`.

- `modules/job-on/`
- `modules/ferramentas/`
- `modules/controlo/`
- `modules/controlo-create/`
- `modules/controlo-approve/`
- `modules/boquilhas/`
- `modules/admin/`

See `modules/INDEX.md` for the task-level map.

## Reading rule

Start with `HOW_THE_APP_WORKS.md` for the global model.

Then read the relevant module folder. A module file should contain the complete detail needed for that task: frontend behavior, backend behavior, identities, relations, inputs, writes, reads, validations, history, integrations, and implementation status where relevant.

If the task concerns a planned feature that is not yet implemented, also read the corresponding file under `features-to-implement/`.

Do not infer missing behavior.

Historical labels such as Operador, Responsável, and Controlador are not current authorization identities. Preserve the underlying functional action, but express current behavior through the current module/capability model.

The detailed module files are populated from verified current knowledge. Empty or placeholder task files must not be treated as defined product behavior until populated.
