# DMO — Global Frontend Contract

This file contains cross-module frontend rules only. Module-specific interactions belong in the owning module.

## Presentation is not domain authority

The frontend presents and collects user interaction for the owning module.

It must not invent:

- canonical IDs;
- persistence relationships;
- backend authorization;
- domain ownership;
- industrial formulas as a second authority;
- audit actor/time;
- decisions that belong to a human.

A visible grouping, card, tab, side panel or read model does not create a database/domain entity.

## Explicit choice

When a workflow requires a human choice, the UI must preserve it.

Candidate filtering/ranking may reduce work but must not silently select the remaining item.

Warnings may draw attention but must not silently become a refusal or decision unless the owning module defines that behavior.

## Unsaved-state preservation

When a user temporarily leaves an in-progress form to select/create a Tool or resolve required context:

- preserve unrelated values already entered;
- Cancel returns without changing the original form;
- successful return applies only the explicitly selected/created result;
- do not silently reset the page because a contextual picker was opened.

## Desktop target

The current product frontend targets a fixed desktop working surface around **1366×768**.

The current scope does not require structural responsive reflow.

Do not introduce responsive breakpoints or alternate structural page hierarchies merely because generic frontend frameworks make them easy.

Shared layout/navigation/table/form behavior should remain predictable across modules, with dense operational presentation suitable for the desktop workflow.

Keyboard/focus behavior must remain usable for repetitive operational entry.

## Data loading

Frontend list/filter controls send focused criteria to the backend and render the returned packet.

Do not fetch the complete operational dataset merely to recreate backend filtering in JavaScript.

The frontend may keep current filter/form state locally as presentation state.

## Loading/error/conflict states

Operational surfaces should distinguish relevant states instead of collapsing everything into one generic error, including where applicable:

- loading;
- empty result;
- permission refusal;
- validation refusal;
- stale/concurrency conflict;
- unavailable dependency/workspace;
- missing/not-yet-generated derived artifact.

These presentation states do not create domain status values unless the owning workflow explicitly defines one.

## Prototype boundary

A frontend prototype may use fake/fixture support to exercise:

- layout;
- navigation;
- interaction;
- form behavior;
- draft preservation;
- visual/error states;
- intentionally prototyped UX flows.

Prototype support is not production backend authority.

It must not establish canonical:

- database schema;
- API contract;
- persistence ownership;
- ID allocation;
- authentication/authorization model;
- implementation completeness.

Fake support should be obviously fake/minimal rather than imitating a historical backend so closely that humans or automated reviewers mistake it for production architecture.

Prototype behavior that appears to create a new identity, ownership rule or backend relationship is not product truth unless the owning module defines it.

The production backend must never be redesigned simply to match prototype fake storage/API shapes.

## Styling / structure

Shared UI rules may define shell, navigation, dense tables, forms, side panels, dialogs and focus behavior.

A frontend convenience must not force a backend/domain redesign.

If a desired UI requires a new identity, relation, lifecycle, ownership rule or persistence fact, that is a product/domain question and must be resolved in the owning module before frontend implementation treats it as truth.
