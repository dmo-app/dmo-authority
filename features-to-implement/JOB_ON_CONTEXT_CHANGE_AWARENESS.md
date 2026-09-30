# Job On Context Change Awareness

**Status:** FUNCTIONALLY DEFINED — NOT IMPLEMENTED

**Type:** Cross-cutting product feature

## Purpose

Job On awareness has two distinct operational cases that may share the same delivery infrastructure but must not share the same timing semantics:

1. a **planned production transition** for a machine, where a different `jobon_id` becomes the production a consumer module should use;
2. an **immediate context change** inside the same `jobon_id`, where CM, MF, BQ, TP/Calote or another relevant production fact changes.

This is an **awareness mechanism**, not an automatic operational decision system.

## Two awareness event kinds

### 1. Planned production transition

Job On provides the planned production by machine and production date.

A consumer module may receive awareness that a new production is due, but the module does not have to switch its operational context at midnight or at one application-wide hardcoded hour.

Each consuming module owns its own configurable daily production-activation time.

Conceptually:

```text
Job On
-> production date + machine + jobon_id

consumer module settings
-> production activation time

awareness may already be pending
-> configured module time arrives
-> module reads Job On
-> module resolves the applicable production/context
-> module updates its own current operational context
```

The configured time belongs to the consuming module, not to a global application setting.

Different modules may therefore react to the same planned production on different configured schedules.

A planned-transition ping may exist before the module's activation time and remain pending until the module performs its scheduled read.

The rule must not depend on an arbitrary universal time such as midnight or a hardcoded `07:00`.

### 2. Immediate Job On context change

A Job On may remain the same production while one of its production contexts is replaced because operational reality changed.

Examples:

```text
jobon_id stays the same

old_cm_id -> new_cm_id
old_mf_id -> new_mf_id
old_bq_id -> new_bq_id
old TP/Calote -> new TP/Calote
```

This awareness is **immediate**.

A consumer that depends on the changed context must not wait until its configured planned-production activation time before checking the Job On again.

Conceptually:

```text
same jobon_id
-> relevant context changes
-> immediate awareness
-> consumer reads Job On now
-> historical records remain attached to their previous context
```

The module-specific production-activation time governs planned production transitions only. It does not delay awareness of changes inside an already identified Job On.

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

A relevant change inside an existing Job On is recorded permanently as a historical fact.

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

A planned production transition does not require a duplicate Job On snapshot merely to support awareness. The consumer can re-read the planned/current Job On context when its configured activation time arrives.

### 2. Lightweight awareness signal

The signal remains deliberately small.

For an immediate context change:

```text
event_kind = context_changed
jobon_id
changed context type
```

For a planned production transition:

```text
event_kind = production_transition
machine
jobon_id
```

The signal must not become a second copy of Job On truth.

It means only that the consumer must re-read the Job On context at the timing appropriate to that event kind.

The exact transport mechanism is an implementation decision. This blueprint does not require an event bus, message bus, polling model or any other specific technical mechanism.

### 3. Per-consumer acknowledgement

A consumer may expose a lightweight pending indication until the relevant awareness has been handled/seen by that module.

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

The underlying Job On change log remains permanent where a real Job On context change occurred.

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

The timing rule is then owned by the consumer:

```text
production_transition
-> respond at that module's configured production-activation time

context_changed
-> respond immediately
```

## BQ example

A BQ may be physically replaced because of repair timing, lot availability or another operational reason.

The Job On keeps the same `jobon_id`, but the BQ production context is replaced:

```text
old_bq_id -> canonical Tool X
new_bq_id -> canonical Tool Y
```

The old `bq_id`, its repair trace and its historical movements remain intact.

The change log records that the Job On BQ context changed.

Consumers that already depend on BQ receive immediate awareness.

This immediate BQ-change behavior is independent from the configured hour at which a module normally adopts the next planned production.

The feature does not attempt to guess the physical replacement or decide the required human action.

## Human role

The application guarantees visibility of a relevant change.

The human decides what the change means operationally.

This feature must not grow into automatic recalculation, automatic correction, automatic approval or a general task-resolution engine unless a separate module-specific rule explicitly requires such behavior.

## Current planning boundary

A planned production transition and a Job On context change are different events.

A machine reassignment inside planning is also not automatically equivalent to an urgent CM/MF/BQ context-change alert.

The important v1 timing rule is:

```text
planned production transition
-> module-specific configured activation time

same-jobon context change
-> immediate awareness
```

## Implementation choices

The functional behavior above is defined. The following remain implementation choices or later detail:

- persistence shape for the Job On change log;
- persistence shape for per-consumer acknowledgement;
- exact UI location and visual treatment of pending awareness;
- transport/delivery mechanism;
- final production-context dependency map for CM/MF/BQ/TP and other facts;
- retention/query UX for viewing the permanent Job On change log;
- technical storage shape for each module's production-activation time.

These technical choices must not change the functional distinction between scheduled production-transition handling and immediate context-change handling.

## Reviewer checks

A reviewer must reject an implementation that:

- mutates a historical CM/MF/BQ context to point to a different Tool instead of creating the replacement context required by the domain;
- deletes the permanent change fact when acknowledgement occurs;
- treats acknowledgement as automatic operational resolution;
- rewrites historical Peso, Boquilhas traces or other records merely because the Job On now exposes a different context;
- introduces a separate notification dependency map that can disagree with the real workflow dependency map;
- turns the awareness signal into a full Job On snapshot without a separately justified need;
- uses midnight or one global hardcoded hour as the production-transition rule for every module;
- delays a same-`jobon_id` context change until the module's scheduled production-activation time;
- makes one module's configured activation time silently control another module.
