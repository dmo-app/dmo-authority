# DMO Authority

This repository is the single source of truth for the **business rules**, **scope**, **canonical identities**, and cross-module functional decisions of the DMO application.

## Ecosystem

- **`dmo-app/dmo-authority`** — functional and architectural authority: what DMO means and the rules implementation must obey.
- **`dmo-app/dmo-app-beta`** — implementation reality: code, schema, migrations, routes, tests and runtime wiring.
- **`dmo-app/dmo-design`** — visual and interaction authority: UI prototypes and presentation decisions.

## Core principle

The Beta is a **scope-reduced product**, not a disposable test version of a larger application.

A capability outside `SCOPE.md` must not be introduced into the Beta merely because it existed in an older repository.

Historical repositories may be used as evidence when recovering information, but they have no authority by themselves.
