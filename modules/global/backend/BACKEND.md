# DMO — Global Backend Contract

This file contains cross-module backend rules only. Module-specific persistence and calculations belong in the owning module.

## Persist facts, compose reads

Persist the real operational fact at its truthful owner.

For reads, traverse the real persisted relationships required by the operation and return a purpose-specific read model.

Do not denormalize the write model merely to make an exceptional read shorter.

A backend query may join several modules without creating new domain ownership between them.

## Focused reads and filtering

Normal operations should load only the data required for the current action.

```text
frontend supplies operation context / filters
→ backend validates identities and relationships
→ backend applies focused query/filter
→ backend returns compact purpose-specific packet
```

Do not load an entire registry/history into the frontend merely to perform normal operational filtering there.

Deeper historical reads may traverse longer relation chains when the user explicitly requested history.

## Backend authority

The backend is authoritative for:

- canonical ID allocation;
- persisted identity/relationship validation;
- authorization;
- actor attribution;
- server-trusted timestamps;
- domain calculations owned by the application;
- guarded mutation/concurrency checks.

The frontend must not mint canonical IDs or submit actor/time as trusted authority.

## Authorization

Every protected operation must be authorized against current effective access, regardless of whether the frontend hid or showed the action.

```text
User
→ current Template
→ permission/capability
→ requested operation
```

Labels and URLs are not authorization evidence.

## Guarded writes and concurrency

Mutations that can conflict must use an explicit guarded-write contract rather than silent last-write-wins behavior.

Where version-based concurrency is used:

```text
client reads ExpectedVersion
→ mutation validates ExpectedVersion
→ successful mutation changes state once
→ version increments once
```

A stale mutation must return an explicit conflict/refusal. Do not silently retry against newer state and do not overwrite newer work invisibly.

## Derived values

A derived value should not automatically become a separately editable persisted source merely because the UI displays it.

Persist/freeze a derived or owner-external value only when the owning workflow requires the exact consumed result/input for durable historical truth.

Industrial/domain calculations must have one application authority. Do not duplicate the same formula independently in frontend and backend.

## Audit and history

Actor/time/history must be derived from trusted persisted application activity, not synthesized by the browser.

Module-specific correction/removal/history rules remain in the owning module.

Do not create a generic global audit/history business domain merely for convenience.

## Notifications / awareness

Cross-module awareness must not become a duplicated source of domain truth.

Where a module needs to revalidate another module's context, the signal should remain lightweight and the consumer should re-read the owning source according to the functional timing rules.

Do not duplicate full domain snapshots into notification payloads without a separately justified requirement.

## External providers

Authentication/database/storage/email/provider integrations may use provider-specific technical adapters.

Those adapters must not redefine product identities or ownership.

Provider secrets and definitive authentication passwords must not be stored as ordinary DMO business data.

## Technical implementation state

Temporary facts such as “currently implemented”, “missing in inspected baseline”, route availability, local configuration defects or one old repository's schema are development evidence, not durable product/backend rules.

They belong in the active development workflow/reports, not in this stable module contract.
