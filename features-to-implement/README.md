# Features to Implement

This folder captures DMO features and implementation-enabling changes that are sufficiently understood to preserve for later Planning, Architect, Reviewer and Developer work, but are **not yet implemented** or still require explicit verification against the current implementation.

A file existing here does **not** mean the behavior exists in `dmo-app-beta`.

Each file must state its own status and distinguish:

- confirmed functional behavior;
- implementation work still required;
- verification-only work where code may already match;
- technical choices that remain open;
- explicit non-goals and boundaries;
- reviewer checks that must hold when the feature is planned or implemented.

## Purpose

The folder prevents confirmed work from being lost between conversations, planning passes or development phases.

It is not a replacement for `IMPLEMENTATION_STATUS.md`.

Current implementation reality remains in `dmo-app/dmo-app-beta` and is summarized in `IMPLEMENTATION_STATUS.md`. Before implementing any volatile code gap, Planning must verify that the gap still exists on the selected current app baseline.

## Current entries

### Cross-cutting / setup / prototype

- `JOB_ON_CONTEXT_CHANGE_AWARENESS.md` — lightweight awareness when a Job On production context changes.
- `SETUP_MODE_PROVIDER_CONNECTION.md` — Blank/Setup Mode that configures infrastructure through the application, beginning with Supabase.
- `PROTOTYPE_FAKE_BACKEND_REWORK.md` — redesign of the GitHub Pages prototype fake backend so it cannot be mistaken for real backend architecture.
- `FOUNDATION_RUNTIME_GAPS.md` — login redirect, module availability/routes, live-auth verification, document configuration and SMTP runtime gaps.

### Boquilhas

- `BOQUILHAS_TRACE_IMPLEMENTATION.md` — reconcile the existing Boquilhas implementation with the canonical one-trace-per-BQ-production-context model.

### Controlo

- `CONTROLO_CONTEXT_IMPLEMENTATION.md` — implement the canonical shared `controlo_id` context without inventing generic parentage or blanket snapshots.
- `CONTROLO_PESO_COMPARACAO_KNOWN_BUGS.md` — the two explicitly recorded open historical-comparison defects from `dmo-app-beta/docs/KNOWN_FUNCTIONAL_ISSUES.md`.
- `PESO_TECHNICAL_VALUES_ALIGNMENT.md` — verify/align Peso with Tool technical-value ownership and historical-stability rules.
- `COMPARACAO_UI_COMPLETION.md` — complete the unfinished Comparação user-facing flow against the existing domain path.
- `PEGAMENTOS_BACKEND_IMPLEMENTATION.md` — implement production backend/persistence for Pegamentos.
- `FOLHA_PERSISTENCE_IMPLEMENTATION.md` — implement the persisted Folha evaluation layer and integrate it with the shared Controlo context correctly.

### Job On / access

- `JOB_ON_DUPLICATION_ALIGNMENT.md` — verify/align Job On duplication against current identity rules.
- `ACCESS_TEMPLATES_BLUEPRINT_COMPLETION.md` — complete Access Templates product definition before implementation relies on it.

## Source backlog

The main sources used to populate this folder are:

- `IMPLEMENTATION_STATUS.md` in this repository;
- current detailed module blueprint files;
- `dmo-app/dmo-app-beta/docs/KNOWN_FUNCTIONAL_ISSUES.md` for the explicitly recorded Controlo/Peso comparison defects;
- owner-confirmed features captured during current blueprint work.

If another pending item is discovered, add it here rather than relying on chat memory.

## File lifecycle

When one of these features reaches implementation:

1. Planning and Architect use the relevant file as functional input together with the current blueprint and implementation reality.
2. Reviewers verify that the implementation respects the confirmed behavior and boundaries in the file.
3. Once implementation is complete and the durable behavior belongs in an owning module/cross-cutting blueprint document, that rule is integrated there.
4. The feature file may then be marked implemented, reduced to a pointer, or retired so this folder remains a useful list of work still to do.

Do not infer missing behavior. Open technical or functional questions must remain explicit.
