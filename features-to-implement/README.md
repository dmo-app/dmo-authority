# Features to Implement

This folder captures DMO features and implementation-enabling changes that are sufficiently understood to preserve for later Planning, Architect, Reviewer and Developer work, but are **not yet implemented**, must be reintroduced during recovery, or still require explicit verification against the selected implementation baseline.

A file existing here does **not** mean the behavior exists in the application baseline being used for recovery/development.

Each file must state its own status and distinguish:

- confirmed functional behavior;
- implementation work still required;
- recovery-baseline gaps;
- verification-only work where code may already match;
- technical choices that remain open;
- explicit non-goals and boundaries;
- reviewer checks that must hold when the feature is planned or implemented.

## Purpose

The folder prevents confirmed work from being lost between conversations, planning passes, recovery work or development phases.

It is not a replacement for `IMPLEMENTATION_STATUS.md`.

The current Beta and an older backup may contain different subsets of the target behavior. Planning must always name the selected implementation/recovery baseline before deciding whether an item is already present, missing or requires migration.

The blueprint remains the definition of intended product behavior. A backup does not become product truth merely because recovery starts from it.

## Current entries

### Cross-cutting / setup / prototype

- `JOB_ON_CONTEXT_CHANGE_AWARENESS.md` — lightweight awareness when a Job On production context changes.
- `SETUP_MODE_PROVIDER_CONNECTION.md` — Blank/Setup Mode that configures infrastructure through the application, beginning with Supabase.
- `PROTOTYPE_FAKE_BACKEND_REWORK.md` — redesign of the GitHub Pages prototype fake backend so it cannot be mistaken for real backend architecture.
- `FOUNDATION_RUNTIME_GAPS.md` — login redirect, module availability/routes, live-auth verification, document configuration and SMTP runtime gaps.

### Ferramentas

- `TOOL_TECHNICAL_VALUES_IMPLEMENTATION.md` — add the canonical optional Tool technical-values extension when recovering from the older backup, preserving existing `tool_id` identities and never inventing missing values.

### Boquilhas

- `BOQUILHAS_TRACE_IMPLEMENTATION.md` — reconcile the existing Boquilhas implementation with the canonical one-trace-per-BQ-production-context model.

### Controlo

- `CONTROLO_CONTEXT_IMPLEMENTATION.md` — add the canonical shared `controlo_id` context to the recovery baseline without inventing generic parentage or blanket snapshots.
- `CONTROLO_PESO_COMPARACAO_KNOWN_BUGS.md` — the two explicitly recorded open historical-comparison defects from `dmo-app-beta/docs/KNOWN_FUNCTIONAL_ISSUES.md`.
- `PESO_TECHNICAL_VALUES_ALIGNMENT.md` — align recovered Peso behavior with Tool technical-value ownership and historical-stability rules.
- `COMPARACAO_UI_COMPLETION.md` — complete the unfinished Comparação user-facing flow against the existing domain path.
- `PEGAMENTOS_BACKEND_IMPLEMENTATION.md` — implement production backend/persistence for Pegamentos.
- `FOLHA_PERSISTENCE_IMPLEMENTATION.md` — implement the persisted Folha evaluation layer and integrate it with the shared Controlo context correctly.

### Job On / access

- `JOB_ON_DUPLICATION_ALIGNMENT.md` — verify/align Job On duplication against current identity rules.
- `ACCESS_TEMPLATES_BLUEPRINT_COMPLETION.md` — complete Access Templates product definition before implementation relies on it.

## Recovery rule

When recovery starts from an older application backup:

```text
backup
!= blueprint
```

The backup is implementation material to preserve where still valid.

Missing canonical structures must be reintroduced from the current blueprint rather than removed from the blueprint to make the backup easier to restore.

Likewise, old implementation structures must not be promoted into current product rules merely because they already exist in the backup.

## Source backlog

The main sources used to populate this folder are:

- `IMPLEMENTATION_STATUS.md` in this repository;
- current detailed module blueprint files;
- `dmo-app/dmo-app-beta/docs/KNOWN_FUNCTIONAL_ISSUES.md` for the explicitly recorded Controlo/Peso comparison defects;
- owner-confirmed recovery-baseline gaps and features captured during current blueprint work.

If another pending item is discovered, add it here rather than relying on chat memory.

## File lifecycle

When one of these features reaches implementation:

1. Planning names the exact implementation/recovery baseline being used.
2. Planning and Architect use the relevant file as functional input together with the current blueprint and that baseline's actual code/schema.
3. Reviewers verify that the implementation respects the confirmed behavior and boundaries in the file.
4. Once implementation is complete and the durable behavior belongs in an owning module/cross-cutting blueprint document, that rule remains there as product canon.
5. The feature file may then be marked implemented, reduced to a pointer, or retired so this folder remains a useful list of work still to do.

Do not infer missing behavior. Open technical or functional questions must remain explicit.
