# Job On Context Change Awareness

**Status:** FUNCTIONALLY DEFINED — NOT IMPLEMENTED

**Type:** Cross-cutting product feature

## Purpose

A Job On may remain the same production while one of its production contexts is replaced because operational reality changed.

Examples:

```text
jobon_id stays the same

old_cm_id -> new_cm_id
old_mf_id -> new_mf_id
old_bq_id -> new_bq_id
```

The purpose of this feature is to make relevant changes visible to the workflows that already depend on that context.

This is an **awareness mechanism**, not an automatic operational decision system.

## Identity boundary

A previously created production-context identity must not silently change the canonical Tool underneath it.

Example:

```text
old_bq_id -> tool_id X
new_bq_id -> tool_id Y
```

Not:

```text
same bq_id
tool_id X -> tool_id Y
```

The previous context remains referencable by historical records that used it.

The same principle applies to CM/MF/BQ production-context identities.

## Three separate concerns

### 1. Job On change log

A relevant Job On context change is recorded permanently as a historical fact.

The record must be able to identify, at minimum:

- the `jobon_id`;
- what production context/fact changed;
- the previous identity/value;
- the new identity/value;
- actor;
- timestamp.

The log is not a snapshot of the whole Job On.

It records the fact of the change.

Acknowledgement must never delete this history.

### 2. Lightweight awareness signal

When a relevant context changes, consumers of that context receive a lightweight awareness signal.

Conceptually:

```text
Job On context changed
-> jobon_id
-> changed context type
```

The signal must not become a second copy of Job On truth.

It means only:

> Something this workflow depends on changed.

The exact transport mechanism is an implementation decision. This blueprint does not require an event bus, message bus, polling model or any other specific technical mechanism.

### 3. Per-consumer acknowledgement

A consumer may expose a lightweight pending indication until the change is acknowledged.

```text
check = "I saw this change"
```

Acknowledgement does **not** mean:

- corrected;
- resolved;
- recalculated;
- approved;
- automatically synchronized;
- historical data rewritten.

One consumer acknowledging a change must not clear awareness for another consumer.

Example:

```text
one Job On BQ change

Boquilhas -> acknowledged
Controlo  -> still pending
```

The underlying Job On change log remains permanent in both cases.

## Consumer routing

Notification/awareness routing must not create a second dependency model.

The same production-context dependency that tells the application which workflows consume CM, MF, BQ, TP/Calote or another Job On fact must also determine which consumers need awareness when that fact changes.

Conceptually:

```text
workflow depends on CM
-> CM changes
-> that workflow is an interested consumer
```

The exact dependency map is documented as the owning workflows are finalized.

Do not maintain one map for operational data dependency and another independent map for notification routing.

## BQ example

A BQ may be physically replaced because of repair timing, lot availability or another operational reason.

The Job On keeps the same `jobon_id`, but the BQ production context is replaced:

```text
old_bq_id -> canonical Tool X
new_bq_id -> canonical Tool Y
```

The old `bq_id`, its repair trace and its historical movements remain intact.

The change log records that the Job On BQ context changed.

Consumers that already depend on BQ receive awareness.

The feature does not attempt to guess the physical replacement or decide the required human action.

## Human role

The application guarantees visibility of a relevant change.

The human decides what the change means operationally.

This feature must not grow into automatic recalculation, automatic correction, automatic approval or a general task-resolution engine unless a separate module-specific rule explicitly requires such behavior.

## Current planning boundary

Machine changes are not currently treated as equivalent to sudden CM/MF/BQ context replacement.

Machine allocation is normally planning information changed with substantial notice. If a real workflow later requires awareness for machine changes, that requirement must be documented from that workflow rather than assumed globally.

## Implementation decisions still open

The functional behavior above is defined. The following are implementation choices or later detail:

- persistence shape for the change log;
- persistence shape for per-consumer acknowledgement;
- exact UI location and visual treatment of pending awareness;
- delivery/revalidation mechanism;
- final production-context dependency map for CM/MF/BQ/TP and other facts;
- retention/query UX for viewing the permanent Job On change log.

## Reviewer checks

A reviewer must reject an implementation that:

- mutates a historical CM/MF/BQ context to point to a different Tool instead of creating the replacement context required by the domain;
- deletes the permanent change fact when acknowledgement occurs;
- treats acknowledgement as automatic operational resolution;
- rewrites historical Peso, Boquilhas traces or other records merely because the Job On now exposes a different context;
- introduces a separate notification dependency map that can disagree with the real workflow dependency map;
- turns the awareness signal into a full Job On snapshot without a separately justified need.
