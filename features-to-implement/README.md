# Features to Implement

This folder captures DMO features and implementation-enabling changes that are sufficiently understood to preserve for later Planning, Architect, Reviewer and Developer work, but are **not yet implemented**.

A file existing here does **not** mean the behavior exists in `dmo-app-beta`.

Each file must state its own status and distinguish:

- confirmed functional behavior;
- implementation work still required;
- technical choices that remain open;
- explicit non-goals and boundaries;
- reviewer checks that must hold when the feature is planned or implemented.

## Purpose

The folder prevents confirmed work from being lost between conversations, planning passes or development phases.

It is not a second implementation-status system and it must not be used to claim that code already exists.

Current implementation reality remains in `dmo-app/dmo-app-beta` and is summarized where appropriate in `IMPLEMENTATION_STATUS.md`.

## Current entries

- `JOB_ON_CONTEXT_CHANGE_AWARENESS.md` — lightweight awareness when a Job On production context changes.
- `SETUP_MODE_PROVIDER_CONNECTION.md` — Blank/Setup Mode that configures infrastructure through the application, beginning with Supabase.
- `PROTOTYPE_FAKE_BACKEND_REWORK.md` — redesign of the GitHub Pages prototype fake backend so it cannot be mistaken for real backend architecture.

## File lifecycle

When one of these features reaches implementation:

1. Planning and Architect use the relevant file as functional input together with the current blueprint and implementation reality.
2. Reviewers verify that the implementation respects the confirmed behavior and boundaries in the file.
3. Once implementation is complete and the durable behavior belongs in an owning module/cross-cutting blueprint document, that rule is integrated there.
4. The feature file may then be marked implemented, reduced to a pointer, or retired so this folder remains a list of work still to do.

Do not infer missing behavior. Open technical or functional questions must remain explicit.
