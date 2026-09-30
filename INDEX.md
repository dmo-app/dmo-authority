# DMO Blueprint — Index

This repository is the canonical DMO blueprint.

It describes the required application independently of whether any particular implementation already exists.

## Global map

- `README.md` — repository purpose and documentation layers.
- `HOW_THE_APP_WORKS.md` — global end-to-end model.
- `IDENTITIES.md` — canonical identities and identity boundaries.
- `RULES.md` — cross-cutting product and architecture rules.
- `SCOPE.md` — current product scope.
- `TECHNICAL_CONTRACT_STANDARD.md` — shared backend/frontend contract standard.
- `modules/INDEX.md` — module-level map.

There is no implementation-status or features-to-implement layer inside this fresh-start blueprint.

## Module layers

Each module/area is documented in three distinct layers:

```text
modules/<area>/*.md
= functional truth

modules/<area>/backend/README.md
= server/application contract

modules/<area>/frontend/README.md
= interface contract
```

Functional files own business meaning, ownership, identity, lifecycle and allowed behavior.

Backend/frontend contracts make that truth precise enough to implement while remaining independent of a specific framework or codebase.

If technical-contract work exposes a missing functional decision, resolve it in the owning functional file before continuing.

## Reading rule

Start with `HOW_THE_APP_WORKS.md` for the global model, then load only the relevant module and its backend/frontend contract.

Do not infer missing behavior from filenames, historical repositories, prototypes, design mocks or implementation code.

Historical labels such as Operador, Responsável and Controlador are not authorization identities. Authorization follows the current capability/access model defined by the blueprint.

Implementation progress, bugs in an old baseline, migrations and recovery work belong in development/implementation tracking rather than in this blueprint.
