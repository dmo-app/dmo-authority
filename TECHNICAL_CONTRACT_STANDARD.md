# Technical Contract Standard

This standard applies to every `modules/<area>/backend/` and `modules/<area>/frontend/` contract.

The technical contracts are part of the blueprint. They define **what an implementation must provide**, not how a particular framework or codebase currently provides it.

## Boundary

A technical contract may define identities, operation anchors, accepted facts, context resolution, reads, writes, outputs, refusals, concurrency, UI states and navigation when those are required behavior.

It should not name implementation details such as EF entities, controllers, React hooks, database column layouts, concrete class names or framework-specific patterns unless that detail itself has become a required product/architecture contract.

## Functional-first rule

```text
functional rule
→ backend contract
→ frontend contract
→ implementation
```

A business rule does not originate in a backend or frontend contract.

If contract work discovers an unresolved question such as whether an association is automatic or human, who owns a fact, what identity exists, or what lifecycle transition is allowed:

```text
stop
→ resolve the question in the owning functional document
→ then update the technical contract
```

## Backend contract

For each server/application operation, define only what is needed:

```text
operation

intent
- exact requested action

anchor
- nearest stable identity/context that truthfully anchors the operation

client facts
- facts newly entered or explicitly chosen for this operation

backend resolves
- authenticated actor
- related identities
- server-owned facts reachable through canonical relations
- only the context required for this operation

backend creates
- canonical IDs, timestamps or other server-owned facts created here

reads
- minimum facts/relations required to perform or validate the operation

persists
- durable facts/relations created or changed

concurrency
- observed version or other concurrency contract when required

returns
- purpose-specific result/read model

refusals
- defined validation/not-found/conflict/domain refusal outcomes

cross-module contract
- identities or published operations consumed from another owning module

non-implications
- nearby behavior this operation must not invent
```

### Minimal-context rule

```text
request
= intent
+ nearest truthful anchor
+ newly supplied business facts
+ observed version when required
```

The client does not resend Tool, production, account or other server-resolvable facts merely because they are visible on screen.

The backend resolves only the context required by the operation and returns only the purpose-specific data the consumer needs.

## Frontend contract

For every page/function/interaction, define:

```text
function

user goal
- what the person is trying to accomplish

entry context
- how the function is reached
- stable identity/context already available

read model
- exact information required to render the function

displayed facts
- live, historical, derived or read-only facts shown to the user

user supplies
- data entered or explicitly selected here

local state
- unsaved/draft interaction state owned only by the interface

request sends
- operation anchor
- explicit user intent
- newly entered business facts
- observed version when required

must not send as authority
- copied server facts
- actor
- server timestamps
- canonical calculations
- relationships inferred from labels

states
- loading
- ready
- empty where meaningful
- validation error
- backend refusal/conflict
- submit pending
- success
- unavailable where meaningful

navigation
- entry/exit behavior
- return behavior
- preservation of local draft state when required

permissions
- visibility/action behavior based on backend-resolved capability
- frontend never grants access itself

design handoff
- actions, regions and states that dmo-design must represent
```

Visible text is not identity. The interface carries canonical IDs/context supplied by the backend.

## Additional-file rule

`backend/README.md` and `frontend/README.md` are the default technical-contract entry points.

Create additional files only when the real documented complexity justifies separation. Do not create empty `READS.md`, `WRITES.md`, `ERRORS.md`, `STATES.md` or similar files merely to anticipate future complexity.

File structure must follow actual documented complexity, not imply subsystems that do not yet exist as product concepts.
