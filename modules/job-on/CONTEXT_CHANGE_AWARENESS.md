# Job On — Context Change Awareness

## Purpose

This document defines the functional v1 awareness mechanism used when Job On information relevant to another module changes.

There are two distinct event kinds:

```text
PRODUCTION_TRANSITION
= a consumer module moves from one planned jobon_id to another for a machine

CONTEXT_CHANGED
= the same jobon_id remains, but a relevant production fact/context changes
```

The goal is awareness and revalidation, not automatic operational decision-making.

No additional resolution workflow is implied.

---

## 1. Two different events must remain distinct

### Planned production transition

A machine has a different planned production for the new production date:

```text
machine = B1

previous jobon_id
-> next jobon_id
```

Job On remains the source of the planned production information.

There is **no single global application hour** at which every module must adopt the next production.

Each consuming module owns its own configurable production-activation time.

A transition ping may already exist before that time. The consumer waits until its configured activation time, then reads Job On and resolves the production/context it should use.

Conceptually:

```text
production-transition awareness
-> pending for consumer

consumer's configured activation time arrives
-> consumer reads Job On
-> consumer adopts the applicable production context
```

The production transition must not be inferred from midnight and must not depend on a hardcoded example hour such as `07:00`.

### Job On context changed

This means the same production occurrence remains identified by the same `jobon_id`, but a relevant fact inside that production was replaced:

```text
jobon_id = unchanged

<old_cm_id> -> <new_cm_id>
<old_mf_id> -> <new_mf_id>
<old_bq_id> -> <new_bq_id>
old TP/Calote value -> new TP/Calote value
```

This event is **immediate** for consumers of the changed context.

A consumer must not wait for its configured production-transition activation time before re-reading the Job On after a same-`jobon_id` context change.

Therefore:

```text
PRODUCTION_TRANSITION
-> consumer reacts at its configured module time

CONTEXT_CHANGED
-> consumer reacts immediately
```

These events may share notification infrastructure, but they must not be modeled as having the same timing behavior.

---

## 2. Production identity and replacement contexts

A substitution inside the same production does not create a replacement `jobon_id`.

For Tool-backed production contexts, the old context identity is never mutated so that it points to another physical Tool.

Forbidden:

```text
existing bq_id
tool_id X -> tool_id Y
```

Required model:

```text
<old_bq_id> -> tool_id X
<new_bq_id> -> tool_id Y
```

The same principle applies to `cm_id` and `mf_id`.

Therefore:

- the `jobon_id` may remain the same;
- the replacement Tool receives a new production-context identity;
- the previous context remains referencable;
- downstream records that already use the previous context remain historically truthful.

Notation rule for documentation:

```text
cm_id -> <old_cm_id> / <new_cm_id>
mf_id -> <old_mf_id> / <new_mf_id>
bq_id -> <old_bq_id> / <new_bq_id>

machine -> B1 / B2 / B3 / C1 / C2 / C3
```

Machine labels such as B1/B2/C1 must not be reused as fictional component-context IDs in examples.

---

## 3. The mechanism has three pieces

```text
JOB ON CHANGE LOG
= permanent memory of a real change inside an existing Job On

PING / AWARENESS
= draws attention to a relevant event

MODULE ACKNOWLEDGEMENT
= "seen / taken notice of"
```

These concepts must remain separate.

The awareness signal also preserves which event kind occurred:

```text
event_kind = production_transition
or
event_kind = context_changed
```

### Acknowledgement means only "seen"

A module acknowledgement does **not** mean:

- resolved;
- corrected;
- recalculated;
- approved;
- operationally treated;
- that a planned production transition has already been applied.

It means only that the awareness was seen/taken notice of.

Acknowledgement never deletes or rewrites the Job On change log.

Acknowledgement is scoped to the consuming module. If the same event matters to more than one module, acknowledgement by one module must not clear another module's pending awareness.

If nobody has acknowledged a relevant event for a module, the awareness remains available when that module is opened later. The mechanism must not depend on a transient on-screen event being witnessed at the exact time of delivery.

### Acknowledgement does not cancel a scheduled production transition

For `PRODUCTION_TRANSITION`, seeing or acknowledging the awareness before the configured activation time does not complete the transition.

Conceptually:

```text
transition awareness arrives
-> human/module may acknowledge that it was seen

configured activation time has not arrived
-> transition is still operationally pending

configured activation time arrives
-> consumer re-reads Job On
-> consumer resolves and applies the production/context that is applicable then
```

Therefore:

```text
acknowledged
!=
production transition applied
```

An implementation must keep the scheduled activation obligation distinct from the lightweight acknowledgement state.

---

## 4. Permanent Job On change log

When a relevant Job On fact changes inside the same production, Job On records the change permanently.

For a BQ replacement the conceptual fact is:

```text
jobon_id
changed_context = BQ
<old_bq_id> -> <new_bq_id>
actor
timestamp
```

Equivalent context identifiers apply to CM and MF changes.

The log is the durable operational memory. It remains available after acknowledgement.

The user-facing presentation does not need to expose UUIDs. It may show the human information required to understand the change.

A planned production transition does not require a duplicated Job On snapshot in the awareness system. At its configured activation time, the consumer re-reads Job On and resolves the applicable production context.

---

## 5. Lightweight awareness signal

The awareness signal is deliberately small.

For a same-Job-On context change:

```text
event_kind = context_changed
jobon_id
context_changed = BQ
```

or the corresponding CM, MF, TP/Calote or other relevant production context.

For a planned production transition:

```text
event_kind = production_transition
machine
jobon_id
```

The signal does not carry a complete Job On snapshot and does not become a second source of production truth.

For `PRODUCTION_TRANSITION`, any `jobon_id` carried by the awareness identifies the planned production known when that awareness was produced. It is not the final source of truth at activation time.

When the configured activation time arrives, the consumer must re-read Job On and use the production/context that is applicable at that moment.

Therefore a later planning edit may change what the consumer ultimately adopts without requiring the awareness payload itself to become a mutable production snapshot.

The normal module/backend relationship already knows how to read the Job On context it needs.

The signal tells the consumer **when to revalidate**, according to the event kind:

```text
production_transition
-> at the consumer module's configured activation time

context_changed
-> immediately
```

---

## 6. Consumers come from functional dependencies

DMO must not create a separate notification-routing model that duplicates the operational dependency model.

The question is:

> Which Job On production context does this module/function already depend on?

That same dependency determines whether a context change should be exposed to that consumer.

Conceptually:

```text
production-context dependency
-> consumer

context changes
-> same consumer receives awareness
```

Notification routing must not become a separate source of domain truth.

The exact consumer mapping for CM, MF, BQ, TP/Calote and other production facts is refined with the functional dependency documentation of the consuming modules.

Each consumer that uses planned production transitions owns its own activation-time setting. One module's setting must not silently control another module.

---

## 7. Role of the application

This is an awareness feature, not an automation engine.

The transverse mechanism does not automatically:

- decide the operational impact of the change;
- create work;
- decide that a new Peso is required;
- recalculate Peso;
- resolve Boquilhas;
- create resolution workflows;
- mark operational work as treated;
- rewrite historical records.

For an immediate context change the minimal flow is:

```text
change
-> immediate awareness
-> consumer re-reads Job On
-> human verifies where required
-> acknowledgement when seen
```

For a planned production transition:

```text
transition awareness
-> pending if received early
-> may be acknowledged as seen without completing the transition
-> consumer activation time arrives
-> consumer re-reads Job On
-> consumer uses the production/context applicable at that moment
```

A module may still have its own independent domain behavior. For example, Boquilhas may use its own canonical identity rules to associate a pre-production trace to a later BQ production context. That behavior is not performed by the generic Job On awareness mechanism.

---

## 8. Historical truth is preserved

A context change never rewrites a downstream record that already belongs to the previous context.

For example:

```text
existing record
-> <old_cm_id>

Job On later changes CM
-> <new_cm_id>
```

The existing record keeps `<old_cm_id>`.

The awareness mechanism tells relevant consumers that the Job On context changed. It does not move the existing record to the new context.

Likewise, moving a module to the next planned production does not rewrite records created under the previous production.

The same principle applies to BQ repair traces, movement history and other context-bound operational records.

---

## 9. Machine changes

A machine reassignment inside planning is not automatically equivalent to an urgent CM/MF/BQ context-change alert.

This is separate from the normal **planned production transition for a machine**, which uses the consumer module's configured activation time.

If a real workflow requires a special immediate awareness rule for machine reassignment itself, that behavior must be defined explicitly rather than inferred from the production-transition mechanism.

---

## 10. Functional v1 boundary

The mechanism is conceptually closed at this level:

```text
1. awareness distinguishes production_transition from context_changed
2. a production_transition is handled at the consuming module's configured activation time
3. acknowledging a production_transition does not mark that transition as applied
4. at activation time the consumer re-reads Job On and uses the then-applicable production/context
5. a context_changed event is handled immediately
6. no global midnight/hardcoded-hour rule controls all modules
7. real same-jobon context changes are permanently logged
8. awareness remains lightweight and does not duplicate the Job On snapshot
9. consumers are derived from existing functional dependencies
10. pending awareness/acknowledgement is scoped per consumer module
11. acknowledgement means only "seen"
12. historical operational records are never rewritten by awareness
```

Still to refine with module dependency documentation:

```text
CM -> actual consumers
MF -> actual consumers
BQ -> actual consumers
TP/Calote -> actual consumers
other production facts -> actual consumers
```

That refinement does not reopen the awareness timing model itself.

## Core rules

> **A planned production transition and a change inside the same Job On are different awareness events. Planned transitions are consumed at each module's own configured production-activation time; same-`jobon_id` context changes are surfaced immediately.**

> **Acknowledging a planned production-transition awareness means only that it was seen. The transition remains operationally pending until the consumer reaches its configured activation time, re-reads Job On and applies the production/context that is applicable then.**

> **Awareness remains lightweight. The consuming module re-reads Job On instead of receiving a duplicated production snapshot.**

> **The consumers of a change are derived from the same production-context dependencies used by the operational workflows; notification routing must not become a separate source of domain truth.**
