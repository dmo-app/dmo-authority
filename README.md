# DMO Blueprint

This repository defines the DMO application independently of any implementation state.

It describes **what the application is required to mean and do**, including business rules, scope, identities, ownership, relationships, module boundaries, backend contracts and frontend contracts.

It must not be read as a report of code that already exists or work that remains to be implemented.

## Documentation layers

```text
functional blueprint
→ product meaning, ownership, identities, lifecycle and allowed behavior

backend contract
→ stack-independent server/application contract required to support that behavior

frontend contract
→ stack-independent interface contract required to consume that behavior

implementation
→ concrete code, framework, schema, routes, classes and components in the application repository
```

The first three layers belong here. Concrete implementation does not.

## Repository map

- `HOW_THE_APP_WORKS.md` — concise global explanation of application behavior and main relationships.
- `IDENTITIES.md` — canonical identities and identity boundaries.
- `RULES.md` — cross-cutting product and architecture rules.
- `SCOPE.md` — current product scope.
- `TECHNICAL_CONTRACT_STANDARD.md` — standard for backend/frontend contract documentation.
- `modules/` — module-level functional truth plus backend/frontend contracts.

## Module structure

```text
modules/<area>/
├── *.md
├── backend/
│   └── README.md
└── frontend/
    └── README.md
```

- Module-root `*.md` files are the **functional truth**.
- `backend/` describes the server/application contract required to support that truth.
- `frontend/` describes the interface contract required to consume that truth.

A functional rule does not originate in `backend/` or `frontend/`. If either contract exposes an unresolved business decision, resolve it in the owning functional file first.

## Relationship to design and development

- `dmo-app/dmo-design` owns visual and interaction design decisions within the functional/frontend contract.
- `dmo-app/development-dmo` owns the development workflow and role/process rules.
- Application repositories consume this blueprint; their current code state does not redefine it.

Historical repositories may be used as evidence when recovering knowledge, but they do not define current product behavior by themselves.

## Implementation-state rule

Implementation progress is deliberately kept out of the blueprint.

Do not write statements here such as:

```text
already implemented
not implemented yet
current code does...
existing schema...
migration pending...
this is the implementation base...
```

When implementation progress needs tracking, track it in the implementation/development workspace, not inside product or technical-contract truth.
