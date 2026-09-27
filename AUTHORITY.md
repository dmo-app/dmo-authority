# Authority

Este documento define a hierarquia de verdade do projeto DMO.

## Decision Ownership Table

| Decision class | Authority | Consequence |
|---|---|---|
| Global identity, relationships, access and domain invariants | `dmo-authority` (this repo) | Nothing downstream can silently override it. |
| Exact Beta inclusion/exclusion and simplified workflows | `dmo-authority` / SCOPE.md | Defines the target even where not yet implemented. |
| Routes, pages, fields, actions that exist NOW | `dmo-app-beta` | Evidence of CURRENT IMPLEMENTATION REALITY, not product authority by itself. |
| Old information density, grouping, labels and interaction clues | Legacy material (quarantined) | Evidence only; loses every conflict. |
| Final frontend composition and visual requirements | `dmo-design` | May shape presentation, but cannot create domain behavior, access, identity or backend facts. |

## Conflict Resolution Rule

If `dmo-app-beta` (code) does something that contradicts `dmo-authority` (rules), it is a bug or technical debt, unless `dmo-authority` is explicitly updated to reflect a new business decision.

If two files inside `dmo-authority` conflict, stop and record an owner decision. Do not pick the more convenient source.

## Precedence inside this repo

`AUTHORITY.md` owns cross-cutting rules.

`SCOPE.md` owns module boundaries.

`IDENTITIES.md` owns data models.

`RULES.md` owns behavioral constraints.

No other file may restate these rules differently.
