# DMO Blueprint

This repository is the canonical functional blueprint for the DMO application.

It defines the **business rules**, **scope**, **canonical identities**, **relationships**, **module boundaries**, and cross-module functional behavior that implementation must obey.

## Ecosystem

- **`dmo-app/dmo-blueprint`** — product and functional blueprint: what DMO means and how it must behave.
- **`dmo-app/dmo-app-beta`** — implementation reality: code, schema, migrations, routes, tests and runtime wiring.
- **`dmo-app/dmo-design`** — visual and interaction design source: UI prototypes and presentation decisions.
- **`dmo-app/development-dmo`** — development workflow, phase process and role responsibilities.

Implementation or design may reveal a conflict or missing decision, but they do not silently redefine the product blueprint.

## Core principle

The Beta is a **scope-reduced product**, not a disposable test version of a larger application.

A capability outside `SCOPE.md` must not be introduced into the Beta merely because it existed in an older repository.

Historical repositories may be used as evidence when recovering information, but they do not define current product behavior by themselves.

## Repository map

- `HOW_THE_APP_WORKS.md` — concise global explanation of how the application works.
- `IDENTITIES.md` — canonical identities and identity boundaries.
- `RULES.md` — cross-cutting product and architecture rules.
- `SCOPE.md` — Beta scope.
- `OPEN_DECISIONS.md` — genuinely unresolved product decisions only.
- `IMPLEMENTATION_STATUS.md` — current Beta implementation state, transitional conditions and known gaps; not product canon.
- `modules/` — detailed functional blueprint organized by module.

If a product decision is unresolved, it belongs in `OPEN_DECISIONS.md` and must not be inferred as canon.

The detailed functional rule belongs in the file owned by the relevant module or cross-cutting concern. Git history preserves how the blueprint evolved; historical curation records do not define a second product source.
