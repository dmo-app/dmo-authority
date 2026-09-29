# DMO — Current Implementation Status

This file records the current implementation state of the Beta.

It is **not product canon**. Functional truth belongs in `HOW_THE_APP_WORKS.md`, `IDENTITIES.md`, `RULES.md`, `SCOPE.md`, and the detailed module files.

This file exists so an implementation agent can distinguish:

- canonical product behaviour;
- functionality already implemented;
- functionality still missing;
- transitional Beta conditions;
- current defects;
- environment/integration limitations.

A current defect or missing implementation must never be promoted into product behaviour.

## Proven runtime baseline

The current Beta has been verified to:

- build successfully;
- apply its current EF Core migrations to a clean PostgreSQL database;
- start successfully;
- run against PostgreSQL.

## Current product-code defect

### Login redirect

Unauthenticated access to a protected route challenges to:

`/Account/Login`

That route currently returns 404 because `CookieAuthenticationOptions.LoginPath` is not configured for the actual login route.

This is a defect, not intended product behaviour.

## Current implementation gaps

### Controlo identity

`controlo_id` is a canonical product identity.

The currently inspected schema/application model does not yet persist that identity.

Resumo is currently composed from `jobon_id`; this implementation state must not be interpreted as authority against `controlo_id`.

The implementation shape of `controlo_id` must follow current authority and must not invent parentage for all Controlo function records.

### Module availability / routes

The current build has:

`ModuleRegistrations.CurrentBuildAvailable = []`

Operational modules therefore resolve as unavailable at runtime.

This is transitional implementation state. The availability/route registration work remains to be completed.

### Live Supabase authentication

The authentication architecture exists, but end-to-end live Supabase authentication has not yet been proven with real credentials in the verified runtime session.

This is an integration/test status, not a product rule.

### Pegamentos

Functional authority exists.

The current inspected application does not yet contain the production backend/persistence implementation for Pegamentos.

Prototype/browser-only behaviour must not be promoted into backend authority.

### Folha

Functional authority exists.

The persisted Folha evaluation layer is not yet implemented in the inspected application state.

### Comparação UI

The Comparação backend/domain/persistence path was reported implemented, while the user-facing UI remained unfinished in the inspected baseline.

### Documents / PDF configuration

Peso PDF generation/storage/send support exists.

The inspected environment did not have the required base-directory configuration row seeded, so document operations can report the workspace as not configured.

That environment state is not product behaviour.

### Email transport

SMTP transport support exists but may be unconfigured in the runtime environment.

An unconfigured transport must produce the defined typed refusal/outcome rather than becoming a functional product rule.

## Known implementation-sensitive area still requiring confirmation

### Boquilhas pending trace multiplicity

The inspected schema permits multiple simultaneous pending Boquilhas registers for one Tool because the provisional Tool index is non-unique.

Current product authority has not yet established whether that multiplicity is intended.

Do not infer the product rule from the current index shape.

## Rule for future updates

When a product decision is closed, update the canonical authority where that rule belongs.

When an implementation defect, gap, or transitional condition is discovered, update this file.

Do not leave a closed product decision in `OPEN_DECISIONS.md`.
